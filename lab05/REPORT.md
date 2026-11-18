# LAB05 - Identifying MITM attacks on GPG keys (Web of Trust)

## 🔑 1) Key generation

```bash
newtyman@Neyc-PC:~$ gpg --list-keys
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   3  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 3u
gpg: next trustdb check due at 2027-11-17
/home/newtyman/.gnupg/pubring.kbx
---------------------------------
pub   rsa4096 2026-11-17 [SC] [expires: 2027-11-17]
      68FE258020B5B636B8048DCC8B1FA799BF7D5EF8
uid           [ultimate] Alice <alice@example.com>
sub   rsa4096 2026-11-17 [E] [expires: 2027-11-17]

pub   rsa4096 2026-11-17 [SC] [expires: 2027-11-17]
      DA6AABF14369F105378A464197AC7B40B0E2F1E8
uid           [ultimate] Bob <bob@example.com>
sub   rsa4096 2026-11-17 [E] [expires: 2027-11-17]

pub   rsa4096 2026-11-17 [SC] [expires: 2027-11-17]
      738DC7B2420A042DED353CB7A5BA875301202920
uid           [ultimate] Mallory <mallory@example.com>
sub   rsa4096 2026-11-17 [E] [expires: 2027-11-17]
```

## 🧾 2) Print fingerprints (very important)

```bash
newtyman@Neyc-PC:~$ gpg --fingerprint alice@example.com
pub   rsa4096 2026-11-17 [SC] [expires: 2027-11-17]
      68FE 2580 20B5 B636 B804  8DCC 8B1F A799 BF7D 5EF8
uid           [ultimate] Alice <alice@example.com>
sub   rsa4096 2026-11-17 [E] [expires: 2027-11-17]

newtyman@Neyc-PC:~$ gpg --fingerprint bob@example.com
pub   rsa4096 2026-11-17 [SC] [expires: 2027-11-17]
      DA6A ABF1 4369 F105 378A  4641 97AC 7B40 B0E2 F1E8
uid           [ultimate] Bob <bob@example.com>
sub   rsa4096 2026-11-17 [E] [expires: 2027-11-17]

newtyman@Neyc-PC:~$ gpg --fingerprint mallory@example.com
pub   rsa4096 2026-11-17 [SC] [expires: 2027-11-17]
      738D C7B2 420A 042D ED35  3CB7 A5BA 8753 0120 2920
uid           [ultimate] Mallory <mallory@example.com>
sub   rsa4096 2026-11-17 [E] [expires: 2027-11-17]
```

## 🕵️ 3) MITM attack – key substitution

The LAB documentation here is lacking. Mallory must first add Bob's identity to her own public key before exporting it and sending it over. Otherwise when Alice imports Mallory's public key and tries to encrypt message for Bob, the command will fail, since Alice has no GPG key for Bob.

```bash
newtyman@Neyc-PC:~$ gpg --edit-key mallory@example.com
gpg (GnuPG) 2.4.8; Copyright (C) 2025 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

sec  rsa4096/A5BA875301202920
     created: 2026-11-17  expires: 2027-11-17  usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa4096/E6FA3D3C239DB9F6
     created: 2026-11-17  expires: 2027-11-17  usage: E   
[ultimate] (1). Mallory <mallory@example.com>

gpg> adduid
Real name: Bob
Email address: bob@example.com
Comment: 
You selected this USER-ID:
    "Bob <bob@example.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o

sec  rsa4096/A5BA875301202920
     created: 2026-11-17  expires: 2027-11-17  usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa4096/E6FA3D3C239DB9F6
     created: 2026-11-17  expires: 2027-11-17  usage: E   
[ultimate] (1)  Mallory <mallory@example.com>
[ unknown] (2). Bob <bob@example.com>

gpg> save
```

```bash
newtyman@Neyc-PC:~$ gpg --armor --export mallory@example.com > bob_pubkey.asc
newtyman@Neyc-PC:~$ cat bob_pubkey.asc 
-----BEGIN PGP PUBLIC KEY BLOCK-----

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
lUZJOuo=
=7TaV
-----END PGP PUBLIC KEY BLOCK-----
```

```bash
┌──(newtyman㉿Neyc-PC)-[~/lab05]
└─$ gpg --import bob_pubkey.asc 
gpg: key A5BA875301202920: public key "Bob <bob@example.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1

┌──(newtyman㉿Neyc-PC)-[~/lab05]
└─$ gpg --list-keys
/home/newtyman/.gnupg/pubring.kbx
---------------------------------
pub   rsa4096 2026-11-17 [SC] [expires: 2027-11-17]
      738DC7B2420A042DED353CB7A5BA875301202920
uid           [ unknown] Bob <bob@example.com>
uid           [ unknown] Mallory <mallory@example.com>
sub   rsa4096 2026-11-17 [E] [expires: 2027-11-17]
```

## 🔐 4) Alice encrypts the message

```bash
┌──(newtyman㉿Neyc-PC)-[~/lab05]
└─$ gpg --encrypt --recipient bob@example.com secret.txt 
gpg: E6FA3D3C239DB9F6: There is no assurance this key belongs to the named user

sub  rsa4096/E6FA3D3C239DB9F6 2026-11-17 Bob <bob@example.com>
 Primary key fingerprint: 738D C7B2 420A 042D ED35  3CB7 A5BA 8753 0120 2920
      Subkey fingerprint: 6FC5 621F 942A BD23 0D76  A702 E6FA 3D3C 239D B9F6

It is NOT certain that the key belongs to the person named
in the user ID.  If you *really* know what you are doing,
you may answer the next question with yes.

Use this key anyway? (y/N) y
```

## 👀 5) Mallory decrypts the message

```bash
newtyman@Neyc-PC:~$ gpg --decrypt secret.txt.gpg 
gpg: encrypted with rsa4096 key, ID E6FA3D3C239DB9F6, created 2026-11-17
      "Bob <bob@example.com>"
Confidential message for Bob
```

## 🚨 6) Attack detection – fingerprint verification

```bash
newtyman@Neyc-PC:~$ gpg --fingerprint bob@example.com
pub   rsa4096 2026-11-17 [SC] [expires: 2027-11-17]
      DA6A ABF1 4369 F105 378A  4641 97AC 7B40 B0E2 F1E8
uid           [ultimate] Bob <bob@example.com>
sub   rsa4096 2026-11-17 [E] [expires: 2027-11-17]
```

```bash
┌──(newtyman㉿Neyc-PC)-[~/lab05]
└─$ gpg --fingerprint bob@example.com
pub   rsa4096 2026-11-17 [SC] [expires: 2027-11-17]
      738D C7B2 420A 042D ED35  3CB7 A5BA 8753 0120 2920
uid           [ unknown] Bob <bob@example.com>
uid           [ unknown] Mallory <mallory@example.com>
sub   rsa4096 2026-11-17 [E] [expires: 2027-11-17]
```

## 🛡️ 7) Web of Trust – trust setting

```bash
┌──(newtyman㉿Neyc-PC)-[~/lab05]
└─$ gpg --import bob_pubkey.asc 
gpg: key 97AC7B40B0E2F1E8: public key "Bob <bob@example.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1

┌──(newtyman㉿Neyc-PC)-[~/lab05]
└─$ gpg --fingerprint bob@example.com
pub   rsa4096 2026-11-17 [SC] [expires: 2027-11-17]
      DA6A ABF1 4369 F105 378A  4641 97AC 7B40 B0E2 F1E8
uid           [ unknown] Bob <bob@example.com>
sub   rsa4096 2026-11-17 [E] [expires: 2027-11-17]
```

```bash
┌──(newtyman㉿Neyc-PC)-[~/lab05]
└─$ gpg --edit-key bob@example.com
gpg (GnuPG) 2.4.9; Copyright (C) 2025 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.


pub  rsa4096/97AC7B40B0E2F1E8
     created: 2026-11-17  expires: 2027-11-17  usage: SC  
     trust: unknown       validity: unknown
sub  rsa4096/5E2150ADC5A6C9C6
     created: 2026-11-17  expires: 2027-11-17  usage: E   
[ unknown] (1). Bob <bob@example.com>

gpg> trust
pub  rsa4096/97AC7B40B0E2F1E8
     created: 2026-11-17  expires: 2027-11-17  usage: SC  
     trust: unknown       validity: unknown
sub  rsa4096/5E2150ADC5A6C9C6
     created: 2026-11-17  expires: 2027-11-17  usage: E   
[ unknown] (1). Bob <bob@example.com>

Please decide how far you trust this user to correctly verify other users' keys
(by looking at passports, checking fingerprints from different sources, etc.)

  1 = I don't know or won't say
  2 = I do NOT trust
  3 = I trust marginally
  4 = I trust fully
  5 = I trust ultimately
  m = back to the main menu

Your decision? 5
Do you really want to set this key to ultimate trust? (y/N) y

pub  rsa4096/97AC7B40B0E2F1E8
     created: 2026-11-17  expires: 2027-11-17  usage: SC  
     trust: ultimate      validity: unknown
sub  rsa4096/5E2150ADC5A6C9C6
     created: 2026-11-17  expires: 2027-11-17  usage: E   
[ unknown] (1). Bob <bob@example.com>
Please note that the shown key validity is not necessarily correct
unless you restart the program.

gpg> quit
```

## 🧠 Reflection

### Why doesn't GPG detect MITM attacks automatically?

Because GPG cannot independently determine whether the public key actually belongs to the person that it identifies as (claims to represent).

### What is a fingerprint and why is it important?

Fingerprint is a short and unique string that is used to identify and verify the longer public GPG key. Comparing whether the entire public key is the same as it's supposed to be is very hard and practically impossible. So we use fingerprints instead, to identify whether two public keys are the same.

### Why is email not a secure channel for exchanging keys?

Because email is vulnerable to multiple forms of content manipulation or interception. For example, users can intercept an email and replace the contents of it, to send the malicious public key instead of the originl/verified one.

It is also easier for an attacker to impersonate someone. You need to be extra careful of the address where email comes from, so that you don't import the malicious public key. For example, attacker could send email from account bob1@gmail.com, while the real user has the email bob@gmail.com.

### How does the Web of Trust reduce the risk of MITM attacks?

The Web of Trust reduces the risk of MITM by allows users to establish trust of public keys through certifications made by other users. For example, Charlie meets Bob and verifies his public key. He signs it and thus certifying that the key belongs to Bob. Alice doesn't know Bob, but already trusts Charlie. Since Bob's key was certified/verified by Charlie, Alice can make sure that the key is genuine and actually belongs to the person it proclaims it does.

## ⭐ Additional challenge

```bash
newtyman@Neyc-PC:~$ gpg --sign-key bob@example.com

sec  rsa4096/97AC7B40B0E2F1E8
     created: 2026-11-17  expires: 2027-11-17  usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa4096/5E2150ADC5A6C9C6
     created: 2026-11-17  expires: 2027-11-17  usage: E   
[ultimate] (1). Bob <bob@example.com>


sec  rsa4096/97AC7B40B0E2F1E8
     created: 2026-11-17  expires: 2027-11-17  usage: SC  
     trust: ultimate      validity: ultimate
 Primary key fingerprint: DA6A ABF1 4369 F105 378A  4641 97AC 7B40 B0E2 F1E8

     Bob <bob@example.com>

This key is due to expire on 2027-11-17.
Are you sure that you want to sign this key with your
key "Alice <alice@example.com>" (8B1FA799BF7D5EF8)

Really sign? (y/N) y
```

- **trust:** *the term trust describes how much you trust a particular person to correctly verify and certify other people's public keys.*
- **signed key:** *a signed key means that someone has digitally signed another person's public key and thus validating their identity.*
- **ultimate trust:** *ultimate trust is the highest trust level. You have ultimate confidence in the identity associated with the key.*