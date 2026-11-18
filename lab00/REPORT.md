# Lab00 Report

### 1️⃣ Navigating the system

✅ Task:

- Move to your home directory.
- Create the linux-exercise directory.
- Enter it.

```bash
newtyman@Neyc-PC:~$ cd ~
newtyman@Neyc-PC:~$ mkdir linux-exercises
newtyman@Neyc-PC:~$ cd linux-exercises/
newtyman@Neyc-PC:~/linux-exercises$ 
```

### 2️⃣ Working with files and directories

✅ Task:

- Create the file description.txt and write your name in it.
- Create the directory test.
- Move description.txt to test.

```bash
newtyman@Neyc-PC:~/linux-exercises$ touch description.txt
newtyman@Neyc-PC:~/linux-exercises$ ls
description.txt
newtyman@Neyc-PC:~/linux-exercises$ echo "Nejc Cifer" >> description.txt
newtyman@Neyc-PC:~/linux-exercises$ cat description.txt 
Nejc Cifer
newtyman@Neyc-PC:~/linux-exercises$ mkdir test
newtyman@Neyc-PC:~/linux-exercises$ mv description.txt test/
newtyman@Neyc-PC:~/linux-exercises$ ls test/
description.txt
newtyman@Neyc-PC:~/linux-exercises$ 
```

### 3️⃣ Moving and copying

✅ Task:

- Rename description.txt to my_profile.txt.
- Copy my_profile.txt to your home folder.

```bash
newtyman@Neyc-PC:~/linux-exercises$ mv test/description.txt test/my_profile.txt
newtyman@Neyc-PC:~/linux-exercises$ ls test/
my_profile.txt
newtyman@Neyc-PC:~/linux-exercises$ cat test/my_profile.txt 
Nejc Cifer
newtyman@Neyc-PC:~/linux-exercises$ cp test/my_profile.txt ~
newtyman@Neyc-PC:~/linux-exercises$ ls ~
linux-exercises  my_profile.txt
newtyman@Neyc-PC:~/linux-exercises$ 
```

### 4️⃣ Permissions and Sizes

✅ Task:

- Check the size of all files in the folder.
- Change the permissions of the file my_profile.txt to be read-only for everyone.

```bash
newtyman@Neyc-PC:~/linux-exercises$ ls -lh test/
total 4.0K
-rw-r--r-- 1 newtyman newtyman 11 Nov 16 20:41 my_profile.txt
newtyman@Neyc-PC:~/linux-exercises$ chmod 444 test/my_profile.txt 
newtyman@Neyc-PC:~/linux-exercises$ ls -l test/
total 4
-r--r--r-- 1 newtyman newtyman 11 Nov 16 20:41 my_profile.txt
newtyman@Neyc-PC:~/linux-exercises$ 
```

### 5️⃣ View System Information

✅ Task:

- Find out your username and the size of your home directory.
- Check how much space is available on your system.

```bash
newtyman@Neyc-PC:~$ whoami
newtyman
newtyman@Neyc-PC:~$ du -hs /home/newtyman/
15G     /home/newtyman/
newtyman@Neyc-PC:~$ df -h /
Filesystem      Size  Used Avail Use% Mounted on
/dev/sde       1007G   19G  938G   2% /
newtyman@Neyc-PC:~$ 
```

### 💡 Extra task

Find the largest file in your home directory:

```bash
newtyman@Neyc-PC:~$ du -ah ~ | sort -rh | head -n 5
15G     /home/newtyman/.cache
15G     /home/newtyman
7.7G    /home/newtyman/.cache/go-build
6.3G    /home/newtyman/.cache/sidekick
3.7G    /home/newtyman/.cache/sidekick/cache/go/mod
newtyman@Neyc-PC:~$ 
```