# LAB 07 - Verifying Software Integrity (SHA256 & GPG)

## ⚙️ Task 1: Inspect the Script

```bash
newtyman@Neyc-PC:~$ cat install.sh 
echo "Installing demo software ..."
mkdir src
touch run.sh
echo "echo Hello" > run.sh
mv run.sh src
sh src/run.sh
```

### What does this script do?

- Firstly it created the `src` folder and an empty `run.sh` file in the current working directory
- Then writes the line `echo Hello` to the `run.sh` file
- Lastly it moves the script into the `src` folder and runs it

## ⚙️ Task 2: Generate SHA256 Checksum

```bash
newtyman@Neyc-PC:~$ sha256sum install.sh > install.sh.sha256

newtyman@Neyc-PC:~$ cat install.sh.sha256
89a8257c249020d883241654f07c214cee9d426c949dc1dbcdec0f551e563dee  install.sh
```

## ⚙️ Task 3: Verify File Integrity

```bash
newtyman@Neyc-PC:~$ sha256sum -c install.sh.sha256
install.sh: OK
```

## ⚙️ Task 4: Generate GPG Key Pair

```bash
newtyman@Neyc-PC:~$ gpg --full-generate-key
....

newtyman@Neyc-PC:~$ gpg --list-keys
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
gpg: next trustdb check due at 2027-11-18
/home/newtyman/.gnupg/pubring.kbx
---------------------------------
pub   rsa4096 2026-11-18 [SC] [expires: 2027-11-18]
      54EDA412F96132F5F2BDEF2FC4C0E5B101218F63
uid           [ultimate] Nejc Cifer <nejccifer12@gmail.com>
sub   rsa4096 2026-11-18 [E] [expires: 2027-11-18]
```

## ⚙️ Task 5: Sign the Checksum File

```bash
newtyman@Neyc-PC:~$ gpg --detach-sign install.sh.sha256

newtyman@Neyc-PC:~$ cat install.sh.sha256.sig 
�3
!T��a2���/���!�cj�a
        ���!�cI)�r�R-��\n�1 Iy!w��aT�� ��r�4(~j�8���8))a�X�eU
                                                             �����?��7�Cl�Ng��Kbl�����+�xf/Wxx��L{i�4�Uڒ�B�#k
t�ẁ�X�Z(f�&x)������'TrM8�
                         ����������T��[(h�)>�Us�T>{�E)z�*��պ@hA�Q���z �S_�y�
R�      WZe����,���Ϝ��H��Q���-{'�� �l6���(���B����
@|��4���
        #�͹7����M̑g�CgTz��,R��Y�Y�0��    g:7daB�調
                                                 }&�]ԌZ�����y�X�]���l��Oo�<��9 ����Q�{��bV0AI3���#o��叻�Қ��K�#��w9�ڶ�}���Dm
                                                                                                                           �DZB�٩.��j����<aӷ��c�־�;z�R�8�V�X����Iy�=zT3�MX�vU)N{u�s�`D����枣�f�?FS�\Co.h
                                                                                                                                                                                                        �n�w�N6�(U�9 
```

## ⚙️ Task 6: Verify the Signature

```bash
newtyman@Neyc-PC:~$ gpg --verify install.sh.sha256.sig  install.sh.sha256
gpg: Signature made Wed Nov 18 20:49:53 2026 CET
gpg:                using RSA key 54EDA412F96132F5F2BDEF2FC4C0E5B101218F63
gpg: Good signature from "Nejc Cifer <nejccifer12@gmail.com>" [ultimate]
```

## ⚙️ Task 7: Simulate a Tampering Attack

```bash
newtyman@Neyc-PC:~$ echo "echo MALICIOUS CODE EXECUTED" >> install.sh

newtyman@Neyc-PC:~$ sha256sum -c install.sh.sha256
install.sh: FAILED
sha256sum: WARNING: 1 computed checksum did NOT match
```

## ⚙️ Task 8: Safe Execution Workflow

```bash
newtyman@Neyc-PC:~$ wget -q https://github.com/devvmhq/devvm/releases/download/v0.1.1-rc.3/install.sh
newtyman@Neyc-PC:~$ wget -q https://github.com/devvmhq/devvm/releases/download/v0.1.1-rc.3/SHA256SUMS
newtyman@Neyc-PC:~$ wget -q https://github.com/devvmhq/devvm/releases/download/v0.1.1-rc.3/SHA256SUMS.minisig

newtyman@Neyc-PC:~$ sha256sum -c SHA256SUMS
sha256sum: devvm-0.1.1-rc.3-darwin-amd64.tar.gz: No such file or directory
devvm-0.1.1-rc.3-darwin-amd64.tar.gz: FAILED open or read
sha256sum: devvm-0.1.1-rc.3-darwin-arm64.tar.gz: No such file or directory
devvm-0.1.1-rc.3-darwin-arm64.tar.gz: FAILED open or read
sha256sum: devvm-0.1.1-rc.3-linux-amd64.tar.gz: No such file or directory
devvm-0.1.1-rc.3-linux-amd64.tar.gz: FAILED open or read
sha256sum: devvm-0.1.1-rc.3-linux-arm64.tar.gz: No such file or directory
devvm-0.1.1-rc.3-linux-arm64.tar.gz: FAILED open or read
sha256sum: devvm-0.1.1-rc.3.tar.gz: No such file or directory
devvm-0.1.1-rc.3.tar.gz: FAILED open or read
install.sh: OK
sha256sum: WARNING: 5 listed files could not be read
```

## 📝 Questions

### Why should you never execute scripts without inspection?

Because you never know what a script does. It could advertise that it only updates your system packages, while in reality, it installs malware or steals information from your computer. That's why you should always check what it does, since one time is all it takes for attacker to gain control of your PC.

## What does SHA256 guarantee?

The SHA256 guarantees that the file contents have not been modified or altered in any way from the time that the SHA256 has been taken.

## What does GPG guarantee?

The GPG guarantees that the SHA256 checksum is correct and not modified/altered. It achieves that by signing the checksum with a trusted key. If an attacker manages to get access to the website/server, they can replace the file with a malware version and the corresponding SHA256 checksum, but they can never re-sign the SHA256 checksum with the original key and thus prove its validity.

## Can SHA256 prove the author of a file?

No, the SHA256 checksum itself cannot prove the author of a file. It needs to be signed with a GPG key to achieve that.

## Why is signing the checksum important?

Because this way, we can prove who the author of the SHA256 checksum file is and that it has not been altered. As explained in the question above, the attacker can replace the file and checksum with the malware version, but they cannot resign the checksum file with your identitiy.

## What happened after the script was modified?

After he script was modified, its SHA256 checksum has changed. So when we compared it against the checksum of the original file, it was different and thus the check failed.

## 🧪 Bonus Task (Optional)

```bash
newtyman@Neyc-PC:~$ wget -q https://cdimage.kali.org/kali-2026.3/kali-linux-2026.3-installer-amd64.iso

newtyman@Neyc-PC:~$ wget -q https://cdimage.kali.org/kali-2026.3/SHA256SUMS{.gpg,}

newtyman@Neyc-PC:~$ sha256sum -c --ignore-missing SHA256SUMS
kali-linux-2026.3-installer-amd64.iso: OK

newtyman@Neyc-PC:~$ gpg --verify SHA256SUMS.gpg SHA256SUMS
gpg: Signature made Tue Oct 20 05:57:14 2026 CEST
gpg:                using RSA key 827C8569F2518CC677FECA1AED65462EC8D5E4C5
gpg: Good signature from "Kali Linux Archive Automatic Signing Key (2025) <devel@kali.org>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 827C 8569 F251 8CC6 77FE  CA1A ED65 462E C8D5 E4C5
```