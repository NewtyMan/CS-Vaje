# LAB 06 - Secrets Management using GPG

## 🔑 1) Prepare secrets

```bash
newtyman@Neyc-PC:~$ echo "API_KEY=super-secret-key-123
DB_PASSWORD=VeryStrongPassword" > secrets.env

newtyman@Neyc-PC:~$ cat secrets.env 
API_KEY=super-secret-key-123
DB_PASSWORD=VeryStrongPassword
```

## 🔐 2) Symmetric encryption of secrets (password)

```bash
newtyman@Neyc-PC:~$ gpg -c secrets.env 

newtyman@Neyc-PC:~$ rm secrets.env

newtyman@Neyc-PC:~$ cat secrets.env.gpg 
�
{i:}��v
       ��xZ��5`LG��
                   Y7̹�&��P R�@�{�� �~�V҈o5;�qG)  �˫��Si+zG&bSS��k�nיM�u*���f� �� :��2~�M�z����oy���k�-�ʹhs�newtyman@Neyc-PC:~$ 
```

## 🔓 3) Decrypt the secrets

```bash
newtyman@Neyc-PC:~$ gpg secrets.env.gpg 
gpg: WARNING: no command supplied.  Trying to guess what you mean ...
gpg: AES256.CFB encrypted data
gpg: encrypted with 1 passphrase

newtyman@Neyc-PC:~$ ls
secrets.env  secrets.env.gpg

newtyman@Neyc-PC:~$ cat secrets.env
API_KEY=super-secret-key-123
DB_PASSWORD=VeryStrongPassword

newtyman@Neyc-PC:~$ gpg -d secrets.env.gpg 
gpg: AES256.CFB encrypted data
gpg: encrypted with 1 passphrase
API_KEY=super-secret-key-123
DB_PASSWORD=VeryStrongPassword
```

## 🔐 4) Asymmetric encryption (recommended)

```bash
newtyman@Neyc-PC:~$ gpg --encrypt --recipient alice@example.com secrets.env

newtyman@Neyc-PC:~$ cat secrets.env.gpg 
�
����@����5�������q�bT���q͟&�����
       (ni�e3&�N"�����Ȍ)y�,��g�]�       ��^}8�
��)q��*�iuv��Р���u��'����B6�þ�3Q�M2��˷^���o�O�B������"�M�Qo���:�I.��KS���b�?<�8x��^�¥�
M46�'�b���<��i��c)�N�
                     jf���jq_
�L�vs�4��kK�t�"�⚛((nʭ�t>����<MYg��k[���ÀF�E�=��.Ko���\Y�=�B"���KJ�86��И4�`�L���!a)�&��Ϗ�LH#6��c�����D�֯�3�瓮�݆�C���"��f�?�G���a�D�@F���=�l�I��^�s���K��MTÑ��B��
                                                                                                                                                             sȄMPZ�%sC�&�)�P(���'�i���~�J�j�Z� �.�4����F�^3"I94}BY�\
saW��.�G+�O��<����Uǡ��TG�2��I3�>|kA=n�%@��>����\�F�2�C�aI\U�U�X@��newtyman@Neyc-PC:~$ 
```

## 🔁 5) Using secrets in an application (simulation)

```bash
newtyman@Neyc-PC:~$ source secrets.env 
newtyman@Neyc-PC:~$ echo $API_KEY
super-secret-key-123
```

## 🧪 6) Simulating a repository leak

```bash
newtyman@Neyc-PC:~$ gpg secrets.env.gpg 
gpg: WARNING: no command supplied.  Trying to guess what you mean ...
gpg: AES256.CFB encrypted data
gpg: encrypted with 1 passphrase
gpg: decryption failed: Bad session key
```

## 🧠 Reflection (required)

### Why don't secrets belong in the source code?

Because source code can either be publicly accessible, leaked or accessed by attacker in another way. This means that as soon as they get access to the source code, they get all the secrets and credentials automatically. Additionally you may also not want all of your employees to have access to all the services/passwords/databases. Putting them in source code will give everyone with access to the code, the access to those services and secrets as well.

### What is the difference between symmetric and asymmetric secret encryption?

Symmetric secret encryption uses a password that must then be shared alongside the secrets file itself. Anyone with the password can decryp and access the secret, whether they should be allowed to or not.

Asymmetric secret encryption uses the private/public key encryption system, where the secrets can then only be decrypted by the recepients private key. Even if the attacker obtains the file containing the secrets, they can't do anything with it or decrypt it.

### What happens if we lose the private key?

If we lose the private key, then we lose access to any encrypted data. We are not able to decrypt secrets in this case or any message in general, without having the private key. We must create a new one, share the new public key and establish the trust again.

### How would you handle this in a larger enterprise?

Larger enterprises usually use existing services and solutions to handle this. One of the best examples is AWS Secrets Manager. You store all the secrets that a software needs in there and then fetch it in the runtime. AWS ensures that only the roles with appropriate access can fetch the secret.

You could also generate a private/public key for each service. The secrets are then encrypted with the public key of the service they belong to. The private key is injected into the services volume on startup and used to read the secrets. After they have been read, the private key is unmounted, so that even if an attacker gets into the service/pod, they cannot steal the private key.

## ⭐ Additional challenge

```bash
newtyman@Neyc-PC:~$ gpg --encrypt --recipient alice@example.com --recipient bob@example.com secrets.env 

newtyman@Neyc-PC:~$ cat secrets.env.gpg 
�
ν�CQ@�%��^3�p�!�NވӐȦ�+m: �w�X�;��)��n�0���v� LYs�3i]F���(�q)��tyY�i����GO�壎_N��cOu��7�,���'#9��3�q�ѮSS�w)�����v�&�uLU\Ec�ܘNK�B�]�l��򋂆��d��f���ߠ�m��͟ua��m6q�l"V2�l�
7��Q�'wE�o��@�������n=�Ri��1�qX�_�N?�`�W�V���W�����M��p�De{�>��fPN*�ݬa�" 2�RƽXu�a&��w��j�X����L��
�r�������x�CoV��J|����T�.w�                                                                      ^�˥F�T�C��3B�ѣ�
         �k=�*-c���Z������v�p���T��&��
�d\w<�aD        OH}+@^Q�QvP�"s������P{��~3qEM�����Y��
                                                     A-�gH6�-Pq���
=
��Z�����&�g����E��(t�^c}���I1��R��Qk�1f��n�u���0c�GY�"qȚ1O�/����
                                                          y��H�!s�U/�Í4��6���%�Gy��@�F��u�!难������w���q��אU,2��byM�ar$�[��F+En�´gZ�8�c�%dr���@i�<fNƩ^�.�8TK�2Ϝ�����~�3�P�E��5T�f�H�������*%N��4�}fK�U鐓�*G9q3
�����`(h���4$X��4hУ���6�o�i�G��<��wy�������n�l`�oA]E��~8W��s�-ȤF��?9
���4���ք"ۚr�)lh��\((��O|pg
�-1q�-�b���6�}h�U�5��+��#�j��a�
```

```bash
newtyman@Neyc-PC:~$ gpg --decrypt secrets.env.gpg
gpg: encrypted with rsa4096 key, ID 5E2150ADC5A6C9C6, created 2026-11-17
      "Bob <bob@example.com>"
gpg: encrypted with rsa4096 key, ID 4EF916CF3142A98A, created 2026-11-17
      "Alice <alice@example.com>"
API_KEY=super-secret-key-123
DB_PASSWORD=VeryStrongPassword
```