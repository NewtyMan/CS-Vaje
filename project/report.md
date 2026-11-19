# LabOps: Empirical Security Assessment

**Deliverable:** Security assessment and plan

**Scope:** The local LabOps training environment and the evidence in [`solutions/`](solutions/)

> **Handling note:** This report describes only the fictional training environment. The weak credential shown in the lab output is a lab-only value, not a production credential. No real personal data or production secrets were used.

## 1. Introduction and scenario

LabOps is a fictional four-person team that administers a Linux enrollment server, supports users through `helpdesk@labops.example`, and distributes an internal enrollment helper to students. The helper is assumed to connect to a database containing student personal data. This assessment follows the five weaknesses in the project brief: a deceptive login page, weak SSH authentication, release-key substitution risk, exposed database credentials, and an altered install script.

The relevant information flow is:

1. Students contact the helpdesk; messages may contain identifying information. A compromised mailbox could also be used to send convincing password requests.
2. Administrators connect to the enrollment server over SSH. A stolen account can provide access to the server and its data.
3. A release operator signs release material and publishes the install script, its checksum, and signature. Students download and run the script.
4. The script or enrollment service uses a database password held in `secrets.env` to access the database.

The database and student records are the highest-value downstream assets. The release channel and administrative account are important paths to those assets: compromise of either could enable unauthorized code or access.

## 2. Assets

| Asset | Users / purpose | If lost or compromised | Personal data? |
|---|---|---|---|
| Helpdesk mailbox (`helpdesk@labops.example`) | Helpdesk staff handle student and faculty requests. | An attacker could read conversations, impersonate support, or solicit credentials. | **Potentially yes:** support messages can identify students or contain personal details. |
| SSH account on the enrollment server | Administrators maintain the Linux server. | Unauthorized access could allow configuration changes, data access, or further compromise. | The credential itself is not student data, but the account can provide access to it. |
| GPG release-signing key | Release operator authenticates release material. | A stolen or substituted key could make malicious releases appear trustworthy. | No student data; the key identifies a fictional LabOps release identity. |
| `install.sh` | Students download and run the enrollment helper. | A tampered script could damage devices, steal credentials, or misuse the user's access. | Not by itself, but it can access or expose personal data depending on its permissions. |
| `secrets.env` | Enrollment service or script obtains the database credential. | Exposure could enable unauthorized database access; changing the file alone does not revoke a leaked credential. | The password is not personal data, but it protects a database that contains personal data. |

## 3. Method and lab setup

The tests were limited to the local training container and files. The SSH service was published on `localhost` port `2222`; the scan and password checks targeted that endpoint only. The phishing screenshot shows a private-network address and plain HTTP. The release-key fingerprints, secret-file output, and tampering output are the supplied records in `solutions/`.

No commands in this report should be run against systems outside this lab. The destructive line shown in the tampering evidence was not executed.

## 4. Evidence from the five steps

### 4.1 Phishing page

The screenshot in [`01-phishing.png`](solutions/01-phishing.png) shows a generic login form served over HTTP at a raw private IP address. The evidence demonstrates the page's appearance; it does **not** show that any credentials were entered or captured.

Five signs a helpdesk employee should notice before entering a password:

1. The browser says **“Not Secure”**: the page uses HTTP rather than HTTPS.
2. The address is an IP address, not a LabOps sign-in hostname or domain.
3. The page uses generic wording (“Login to the system”) and does not identify LabOps or explain what service is being accessed.
4. It asks for a username and password without context or a clear reason to re-authenticate.
5. The minimal, unbranded page does not match an expected organization sign-in page. Appearance alone is not proof, but it is a reason to stop and verify.

An employee should navigate to the known service address independently or contact the requester through a known channel. They should not follow an email link and type a password into an unverified page.

### 4.2 SSH exposure and password test

[`02-nmap.txt`](solutions/02-nmap.txt) records an Nmap service scan of `localhost`. It found TCP port `2222` open and identified OpenSSH 9.6p1 on Ubuntu.

[`03-ssh-weak.txt`](solutions/03-ssh-weak.txt) records a Hydra run against `weakuser` on the same local port. It found the lab account's weak password (`123456`) and completed in about three seconds according to the displayed start and finish times. This shows that a short, common-password list was sufficient for this account.

[`04-ssh-strong.txt`](solutions/04-ssh-strong.txt) records a short run against `stronguser`: the supplied list contained 20 attempts, no valid password was reported, and the run took about 5.69 seconds. This is only evidence that this list did not find that account's password in this brief test; it is not proof that the account is secure or that password authentication is safe.

### 4.3 Release-key identity

[`05-fingerprints.txt`](solutions/05-fingerprints.txt) shows two keys with the same displayed name and email address but different fingerprints:

- Release key: `AE98 C4B2 ACBA 81CF A4BE 5DA5 8D04 A387 EDB5 116E`
- Other key: `6BC6 9814 F9BE A7CE A6C3 7D9E DC0A 5D74 C129 FB04`

[`08-gpg-keys.txt`](solutions/08-gpg-keys.txt) adds the workflow evidence: the public key was exported from the attacker keyring, imported into the employee keyring, used as the recipient for an encrypted note, and the note was decrypted by the attacker keyring. The imported key ID matches the attacker key fingerprint recorded in `05-fingerprints.txt`, and the decryption output recovers the note. During encryption, GPG warned that there was no assurance the key belonged to the named user; the operator proceeded anyway. That warning is the key lesson: a matching email address and successful encryption do not authenticate the recipient key. The decrypted message also carries a good signature from a separate LabOps Employee key, but GPG marks that signing key as unknown and uncertified, so the signature does not establish a trusted employee identity.

Before trusting a newly received key, LabOps should compare its complete fingerprint with a value already authenticated through an independent channel—for example, in person or through a separately verified phone number.

### 4.4 Database secret

[`06-secrets.txt`](solutions/06-secrets.txt) shows `secrets.env` in plaintext and the output of encrypting it with GPG (`gpg -c`) as `secrets.env.gpg`. The displayed ciphertext is not readable as the original file.

Encryption is useful only if the decryption passphrase and key are protected separately. If a repository clone or backup already contains the plaintext, later encrypting the working copy or deleting the file does not erase those copies. LabOps must treat the database credential as exposed, rotate it, and investigate whether it was used.

### 4.5 Tampered install script

[`07-install-tampering.txt`](solutions/07-install-tampering.txt) records an added destructive command, followed by a checksum check that reports `install.sh: FAILED`. The SHA256 checksum validation failed due to altering of the original script.

The same evidence shows that GPG reports a **good signature** on `install.sh.sha256`. This is not a contradictory result: the detached signature authenticates the unchanged checksum file, while the checksum comparison detects that the current script no longer matches the digest recorded in that file. The correct release check is to verify the signature against the independently trusted release key **and** verify the downloaded script against the signed checksum. A valid signature on a manifest does not mean every file currently present matches that manifest.

## 5. Risk assessment

| Asset | Weakness | Evidence | Likelihood | Impact | Risk |
|---|---|---|---|---|---|
| Helpdesk mailbox | A user may trust a generic login form reached through a deceptive request. | [01-phishing.png](solutions/01-phishing.png) | Medium | High | **High.** A convincing request could expose mailbox access, enable further impersonation, or expose personal support messages. |
| SSH account | A weak account password is accepted by the exposed SSH service. | [02-nmap.txt](solutions/02-nmap.txt), [03-ssh-weak.txt](solutions/03-ssh-weak.txt), [04-ssh-strong.txt](solutions/04-ssh-strong.txt) | High | High | **High.** A common password was found quickly on the lab account. If an equivalent account were reachable by an attacker, server access could lead to data exposure or service changes. |
| GPG release key | A key with the expected name and email could be mistaken for the trusted signing key. | [05-fingerprints.txt](solutions/05-fingerprints.txt), [08-gpg-keys.txt](solutions/08-gpg-keys.txt) | Low | Medium | **Medium.** The two keys have different fingerprints despite sharing the same user ID. The substitute was imported and used after GPG warned that its identity was unassured; accepting it could undermine release trust. |
| `secrets.env` / database | A database password is stored as plaintext in a file that could be committed or cloned. | [06-secrets.txt](solutions/06-secrets.txt) | Medium | High | **High.** A repository leak could expose a credential to every recipient of the plaintext history or clone; the protected database contains personal data. |
| `install.sh` | Users could run a changed script without an enforced verification step. | [07-install-tampering.txt](solutions/07-install-tampering.txt) | Medium | High | **High.** The checksum failure shows the changed script differs from the recorded release digest. If users ignore or omit this check, a malicious release could affect student devices or data. |

These are qualitative judgments for the described small team, not measured incident probabilities. The evidence is from a controlled lab and does not establish that any real LabOps system or account has been compromised.

## 6. Security plan

| Control | Weakness addressed | Monday-morning change | Risk that remains |
|---|---|---|---|
| Helpdesk sign-in rule and phishing reporting | Password entry into an unverified page | Tell staff never to enter credentials through an email link; use a bookmarked/known sign-in URL, verify unusual requests through a second channel, and report suspicious messages. Enable MFA for the mailbox. | A user can still be deceived by a convincing page or approve an unexpected MFA prompt; training and reporting do not prevent every compromise. |
| Remove or lock unused weak accounts | Guessable/abandoned SSH account | Lock and remove `weakuser` if it is not required. Review the local account list and assign named accounts rather than shared or abandoned accounts. | A different account may still be compromised, and account cleanup does not fix other server vulnerabilities. |
| Restrict administrative SSH | Password-based remote access | Require individual SSH keys for administrators, disable SSH password authentication after verifying key access, and limit which accounts may connect. Keep a documented recovery path. | Stolen keys, compromised administrator devices, or misconfigured access controls can still permit access. |
| Pin and independently verify release-key fingerprints | Same-name substitute key | Publish the approved fingerprint through a second authenticated channel and have a second staff member verify it before importing or replacing a release key. | A compromised independent channel or signing workstation can still defeat the check; revocation and key rollover procedures are also needed. |
| Verify signed release manifests | Modified install script | Publish the script, SHA-256 manifest, and detached signature together. Before use, verify the signature against the pinned fingerprint and then verify the script's checksum; stop distribution or execution on either failure. | A compromised signing key or malicious but properly signed release can still pass verification. |
| Keep secrets out of Git and rotate exposed credentials | Plaintext `secrets.env` | Move the database credential to a managed secret store or protected deployment configuration, exclude local secret files from Git, restrict database privileges, and rotate any credential that has appeared in a repository or clone. | Secret stores and deployment hosts can themselves be compromised; rotation cannot recall data already accessed. |

## 7. Legal and ethical note

### GDPR considerations

Because the enrollment database is assumed to contain student personal data, LabOps should define and follow a legitimate, specific purpose for collecting and using that data, and avoid using it for unrelated purposes. Access should be limited to staff and services that need it. Appropriate security—including credential protection, least privilege, access review, and logging—supports the GDPR principles of integrity and confidentiality and the security-of-processing obligation (including Article 32).

If the database password has already leaked, LabOps should promptly contain the exposure, revoke or rotate the password, review relevant access logs, determine what systems and records may have been accessed, and document the assessment and response. A leaked password alone does not establish that personal data was accessed. If a personal-data breach occurred, the controller must assess the risk to individuals: where required, notify the competent supervisory authority without undue delay and, where feasible, within 72 hours after becoming aware; if the breach is likely to result in a high risk to individuals, communicate it to affected people without undue delay, subject to the GDPR's exceptions. This is an operational summary, not legal advice.

### NIS2 applicability

On the facts supplied, LabOps is a small fictional team maintaining an internal helper for a faculty; the brief does not identify a covered NIS2 sector, a qualifying size, or a national designation. It therefore cannot be classified as an **essential** or **important** entity from this scenario alone, and it would not ordinarily be described as either solely because it handles student data. The answer could change if the responsible legal entity provides an in-scope service, meets applicable criteria, or is designated under the relevant national law. The country's NIS2 transposition and the university's legal status should be checked before making a real-world determination.

### Scope and authorization

All tests described here were limited to the local container, local files, and the locally displayed phishing page. No external server or person was tested or contacted. Before testing anyone else's system, LabOps would need explicit, written authorization from the system owner defining the permitted targets, methods, time window, and handling of any data encountered; testing must also comply with applicable law and organizational policy.

## 8. Conclusion

The evidence shows how weaknesses across identity, administration, release distribution, and secret handling can combine into a serious risk for student data. The most direct finding is the weak SSH account: the supplied wordlist found its lab password in a few seconds. The fingerprint evidence also shows why a displayed name is not a trustworthy key identity, and the tampering evidence demonstrates that the checksum check detects a changed script even though the signed checksum file itself still has a valid signature.

LabOps should first remove weak or abandoned access, require controlled administrative authentication, protect and rotate database credentials, and make release verification mandatory. The phishing page and release-key substitution both reinforce one operational rule: verify identity through a channel independent of the message or file being evaluated. The evidence supports a local training assessment only; it does not establish a compromise outside the lab.

## Evidence completeness and limits

The supplied files cover the phishing-page appearance, Nmap scan, weak and strong account Hydra runs, key fingerprints and key-substitution exchange, plaintext/ciphertext secret output, and tampered-script checks. The tampering evidence captures a failed checksum comparison but a good signature on the unchanged checksum manifest; it does not show a failed GPG signature check. Those distinctions are reflected above rather than inferred as successful or failed steps.
