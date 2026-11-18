# LAB04

### ✅ 1) Generate GPG key pair

```bash
newtyman@Neyc-PC:~$ gpg --full-generate-key
gpg (GnuPG) 2.4.8; Copyright (C) 2025 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
   (9) ECC (sign and encrypt) *default*
  (10) ECC (sign only)
  (14) Existing key from card
Your selection? 1
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 1y
Key expires at Tue Nov 16 23:13:21 2027 CET
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.

Real name: Nejc Cifer
Email address: nejccifer12@gmail.com
Comment: 
You selected this USER-ID:
    "Nejc Cifer <nejccifer12@gmail.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: /home/newtyman/.gnupg/trustdb.gpg: trustdb created
gpg: directory '/home/newtyman/.gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as '/home/newtyman/.gnupg/openpgp-revocs.d/F0AE03D6104DA0758E02074BEFFFB93958F3B08E.rev'
public and secret key created and signed.

pub   rsa4096 2026-11-16 [SC] [expires: 2027-11-16]
      F0AE03D6104DA0758E02074BEFFFB93958F3B08E
uid                      Nejc Cifer <nejccifer12@gmail.com>
sub   rsa4096 2026-11-16 [E] [expires: 2027-11-16]
```

```bash
newtyman@Neyc-PC:~$ gpg --list-keys
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
gpg: next trustdb check due at 2027-11-16
/home/newtyman/.gnupg/pubring.kbx
---------------------------------
pub   rsa4096 2026-11-16 [SC] [expires: 2027-11-16]
      F0AE03D6104DA0758E02074BEFFFB93958F3B08E
uid           [ultimate] Nejc Cifer <nejccifer12@gmail.com>
sub   rsa4096 2026-11-16 [E] [expires: 2027-11-16]
```

### ✅ 2) Export and import public key

```bash
newtyman@Neyc-PC:~/lab04$ gpg --armor --export nejccifer12@gmail.com > student_pubkey.asc
newtyman@Neyc-PC:~/lab04$ ls
student_pubkey.asc
```

```bash
newtyman@Neyc-PC:~/lab04$ gpg --import peer_pubkey.asc 
gpg: key 3AF9A4ECDAFB587B: "Kristian Cifer <kristian.cifer@gmail.com>" not changed
gpg: Total number processed: 1
gpg:              unchanged: 1
newtyman@Neyc-PC:~/lab04$ 
```

```bash
newtyman@Neyc-PC:~/lab04$ gpg --list-keys
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   2  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 2u
gpg: next trustdb check due at 2027-11-16
/home/newtyman/.gnupg/pubring.kbx
---------------------------------
pub   rsa4096 2026-11-16 [SC] [expires: 2027-11-16]
      F0AE03D6104DA0758E02074BEFFFB93958F3B08E
uid           [ultimate] Nejc Cifer <nejccifer12@gmail.com>
sub   rsa4096 2026-11-16 [E] [expires: 2027-11-16]

pub   rsa4096 2026-11-16 [SC] [expires: 2027-11-16]
      6A94068800AB759E32AB67063AF9A4ECDAFB587B
uid           [ultimate] Kristian Cifer <kristian.cifer@gmail.com>
sub   rsa4096 2026-11-16 [E] [expires: 2027-11-16]
```

### ✅ 3) Preparing the message

```bash
echo "To: peer@example.com
From: student@example.com
Date: $(date)
Secret message: Confidential message" > message.txt

newtyman@Neyc-PC:~/lab04$ ls
message.txt  peer_pubkey.asc  student_pubkey.asc
```

### ✅ 4) Encrypting and signing

```bash
newtyman@Neyc-PC:~/lab04$ gpg --encrypt --sign --armor --recipient kristian.cifer@gmail.com message.txt 

newtyman@Neyc-PC:~/lab04$ cat message.txt.asc 
-----BEGIN PGP MESSAGE-----

hQIMAx7//f+7D7jsAQ/+OzWxEw5EjiF1ZQG5ZwmHVOjZ3BcK9R3wu4Xu9iukLryU
UknEcHwSUNvi9slqQ4bhCUlzDE/EN9YCoA9UWZSG5nFKSsf9rfxjmg6z/BF9RBZl
n3O+q+RSZ3BE4E3T2Tate5C/YtbsjDrBI3fCD5myYquPTZPSOslD8gD/ihTNt9HE
SG9CWIKAFM/JFwYf9Bvaj91OezKWOJ3Irpo7IXAJDr46pkFkj7L9Fm8bupz20ZIi
hBZD8c63GiLdlwwc8kY6SYLw93Vhyo6kS0j65Opid+20mBN5/8eKhdwh2NnIErQV
JwTRXPs/cZ8TdkF3BE5t0HUB2l7zEd15/FO+qRjSm4E901y5KAFXsLzL2I3uZHof
dxUiA5TYD9FPc+DkgYGoM2cNnq1zxAlh0gloBJuJi+ToPXof1RKI/ye/o3VMjmA1
sCStmB8pgEafOUSHeeeXEx3aXb/1b3MRb21jJcXBQHv5BRyKlGTZyiFpV/b30Yqc
gsBeuoQdHRn+GmrViZxE/waWpOO9dtXBOSQOhaGp0yRace4owKtxJ8Yc9ALf9KEI
J8I/frzrMIqr1JU5hUfy8V0YPq4jvWxHcJ6GAfk3k1kSXr8Wt9YSIBEOsbuwFYMW
k9Iyul6rlgxZjvCGGs6nkpdm9S/sQfKAsfcM4HS7M+43v2Z/wRFWubX/Zq9EjhvS
6QFi3Kf09moPi6T+hehCHFqnOoL5S4RZQjLNLqg+JYYIo+3xkvj0kO1s7fL5wgyQ
K1MJs2jZ/1z1ENl3Yk4++5Lt+hjkbgGdVKVG46JsTkoO3TCrUDABC70Z0z+CXeYM
8UI3icAxIKZxJfK6jKg+SqRGqsQE4e5pWUrORk10xf0DhZsuLrg6Tji3BbM9fKAy
c/hr6QGYRIJaxrXkjlGXxIllB5UjPr8iJzJxlMNHvVa0iNXoqMM0HtlX6/GOxlo5
0Tzgup/3C/8ppatt0QQlcv6Rs9amPaq7EUpMVI38cgWlbyU1G+EaRWywYhhA9MEb
B0fFcsBwwD+BiVcLWEX9tOhx3oBPFazkKBf6eAPg6nlJzkUsDJV3QKklVRE3zdyR
JP9KuDtfVQ3TkibupTw9bG9ERIEXERJxIUldNFsvEe7UA4CiRzBv14MvPGWpIJO+
huh7A3UzoDSHgDEm8E9Mvw4Xn66ez++l/zznMvqN7I7qWmyC1h/ijPHEvoVdIIcx
QTh8kiggcUBPqjRjHa6QUVWaH9FyZV4B18/XF8wY6Vm5qsQ5pyy94nS5ZjegywI4
lwEby1tEAe6j7e32gbkRLeA2OzZiM+FvmWzeOxHd+I5RmAfmPsJaD9KRqjxocnCq
CbQ7ceHEzrAzHSMkQISc0daTpAY0l/pd4V+rU7RIuzfEwDnzMvCgSSK1eiWsH7Sw
RMNl+UNA4xiNhJX8XCyge6UvdLzzZEzpDJc72nHfW/vV2NcTFtwReTIlTUDcxcKL
VC014M+N9WQwGzueTkkobGLOPkyU6+/C09Jnxit3NEH0l9qpzSQsPcVnb7ka3Kba
YgobWuxvyh+E+hCSeACAptLt+X+id4b3xo6aR8U3U3omxlAUkXt8msU69svOzXqD
Lth38ch+geeD4i4GjVRGCOx7degXm89YorjpjfQhyw3o2CvBJpCCrMOSTyzcUzR9
IWEiAxyNTCvkS7N6FIDLTS6SH/XwJ5XSeUABqrfV4rfTz9EmPqa2Juec0Nk=
=wV4u
-----END PGP MESSAGE-----
```

### ✅ 5) Decrypting and verifying the signature

```bash
newtyman@Neyc-PC:~/lab04$ gpg --decrypt message.txt.asc > decrypted_message.txt
gpg: encrypted with rsa4096 key, ID 1EFFFDFFBB0FB8EC, created 2026-11-16
      "Kristian Cifer <kristian.cifer@gmail.com>"
gpg: Signature made Mon Nov 16 23:18:39 2026 CET
gpg:                using RSA key F0AE03D6104DA0758E02074BEFFFB93958F3B08E
gpg: Good signature from "Nejc Cifer <nejccifer12@gmail.com>" [ultimate]
```

```bash
newtyman@Neyc-PC:~/lab04$ cat decrypted_message.txt 
To: peer@example.com
From: student@example.com
Date: Mon Nov 16 23:17:52 CET 2026
Secret message: Confidential message
```

### 📝 Report preparation

1. Encryption ensures the confidentiality of the message, meaning that only authorized parties can read the data. The signing on the other hand ensures the authenticity and integrity, meaning that recepient can verify who created the data and whether it was modified.
2. Public key is public and is used to encrypt the message you want to send. For example, if I want to send a message to Student B, I need to ues their public key to encrypt the message. Then I send the message over and the student then uses their private key to decrypt the message. Since it was signed with their public key, their private key can decrypt it.
3.  When the encrypted file is modified, the decryption will fail with the CRC error and thus won't be decrypted. This way we can ensure that if the message is decrypted, is was not modified.

### ⭐ Additional tasks

#### Revocation certificate

```bash
newtyman@Neyc-PC:~/lab04$ gpg --gen-revoke nejccifer12@gmail.com > revoke.asc

sec  rsa4096/EFFFB93958F3B08E 2026-11-16 Nejc Cifer <nejccifer12@gmail.com>

Create a revocation certificate for this key? (y/N) y
Please select the reason for the revocation:
  0 = No reason specified
  1 = Key has been compromised
  2 = Key is superseded
  3 = Key is no longer used
  Q = Cancel
(Probably you want to select 1 here)
Your decision? 3
Enter an optional description; end it with an empty line:
> 
Reason for revocation: Key is no longer used
(No description given)
Is this okay? (y/N) y
ASCII armored output forced.
Revocation certificate created.

Please move it to a medium which you can hide away; if Mallory gets
access to this certificate he can use it to make your key unusable.
It is smart to print this certificate and store it away, just in case
your media become unreadable.  But have some caution:  The print system of
your machine might store the data and make it available to others!
```

#### Signing only (no encryption)

```bash
newtyman@Neyc-PC:~/lab04$ gpg --clearsign message.txt
File 'message.txt.asc' exists. Overwrite? (y/N) y
newtyman@Neyc-PC:~/lab04$ cat message.txt.asc 
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA512

To: peer@example.com
From: student@example.com
Date: Mon Nov 16 23:17:52 CET 2026
Secret message: Confidential message
-----BEGIN PGP SIGNATURE-----

iQIzBAEBCgAdFiEE8K4D1hBNoHWOAgdL7/+5OVjzsI4FAmr7g0YACgkQ7/+5OVjz
sI4W/w//acTpHDPligCMaZslQMgE2lXe8zxD/l7mZLtboo4jv5DOec4aRNGq87fI
D3AmEUxHUcd20Uj/+8MUJau0I+AU9dMUeJa9gGBM2zmFMyBXHwV5Iie/0n5tGbTV
srHzJHXwgydaYdM4/0pMzJam7QgkK4mDSttqOwwT8L/elekNcNL85pvNZO5bLvgc
bFI2C4by3BMcDD9r9ndUbVCU490erY4drm+J53wxsIMLVaXP5RWYTRptHO/WGSUt
vG/DhFGWuRis8fdEWwd8SkzciAyZbtIXqy4cgHgbMu7/RoWSV8pFeZyLiw6KI4ai
VLTdnhdolQnfes++5zxgVIIECrQD6GvcM+3ie1CvxBo3B/m++mFyzu1kUJ8Fm/th
ML1ab8eVD7QLteg0aEcfYaBjBaj5i6/7YeS5CRVHUUfZyd8eJg0r2JFMo4N/Bj+C
2ynrRRke9gn6AofGSZd1pr3roi1El0byZ9nhtyKuoqpE2dOyXAyFegMObiJlVJHM
dVnbfFU+IUgoDn3ElAWRNlh198TCagpqOKGvNJ413NBnslqfEQt9wkQMWvmHrkVr
0A/6ze8aGnT9VqHAISIY5d2GbKDKJB5Od8MaGRjAmhV7H7T+f7j62MuJey98QcAw
x7A6u8XPd9oMurfDyS2YSxEgEOXqJWhMxLPoEUgtngXTUxtnmlA=
=HeDL
-----END PGP SIGNATURE-----
```

#### Verifying the signature

```bash
newtyman@Neyc-PC:~/lab04$ gpg --verify message.txt.asc 
gpg: Signature made Mon Nov 16 23:27:18 2026 CET
gpg:                using RSA key F0AE03D6104DA0758E02074BEFFFB93958F3B08E
gpg: Good signature from "Nejc Cifer <nejccifer12@gmail.com>" [ultimate]
gpg: WARNING: not a detached signature; file 'message.txt' was NOT verified!
```