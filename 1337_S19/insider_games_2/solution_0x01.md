Insider Games #2
================

**Solution author:** https://twitter.com/s_persianov

**Description:** Hence you are the one who found the insider, we need you help again. 
This time, analyze insider’s drive and find what was leaked.

Flag looks like: `1337.MD{`*something_random_or_not*`}`

This challenge is all about looking for clues in Linux. We start with a bunch of folder/files thrown at us:

```bash
$ ls
bin  etc  home	lost+found  opt  proc  tmp  var
$ ls -lah
total 40K
drwxr-xr-x 10 4.0K Sep 21 09:43 .
drwxr-xr-x  3 4.0K Sep 21 09:43 ..
drwxr-xr-x  2 4.0K Sep  4 18:33 bin
drwxr-xr-x  2 4.0K Sep  4 18:32 etc
drwxr-xr-x  3 4.0K Sep  4 18:34 home
drwx------  2 4.0K Sep  4 18:29 lost+found
drwxr-xr-x  2 4.0K Sep  4 18:32 opt
drwxr-xr-x  2 4.0K Sep  4 18:35 proc
drwxr-xr-x  8 4.0K Sep  4 18:37 tmp
drwxr-xr-x  4 4.0K Sep  4 19:48 var
```

I'll start with `home` directory, as this is the most promising for now.

```bash
$ tree -a home
home
└── joey
    └── .bash_history
```

It turns out Joey archived (password protected) a "secret_recipe.pdf" file, sent the archive over email and removed all local copies.

```bash
$ cat .bash_history
zip sr.zip Desktop/secret_recipe.pdf -e superpassword
zip sr.zip Desktop/secret_recipe.pdf -esuperpassword
zip sr.zip Desktop/secret_recipe.pdf -e
ls
du -sh Desktop/sr.zip
pwd
cd Desktop/
ls
cd .
cd ..
ll
du -sh sr.zip 
file sr.zip 
unzip sr.zip 
ls
rm -rf Desktop/secret_recipe.pdf
history
sendemail -t root@localhost -m "This is the last document I was able to get from their servers." -u "The secret" -a sr.zip -f "joey@protonmail.com"
``` 

Never use passwords in your bash commands, right? Well Joey was probably not aware of the bash history. We now have the archive password `superpassword`, but not archive.

Here comes the `sendemail` command. It shows that email was sent to root@localhost and the archive was attached to it? Why doing that, right? The answer is: We don't care :D

Usually all emails to @localhost are stored locally in something like `/var/spool/mail/<username>`.

```bash
$ cat root 
From joey@protonmail.com  Thu Sep  5 03:43:05 2019
Return-Path: <joey@protonmail.com>
Received: from l0ft (localhost [127.0.0.1])
	by l0ft (8.15.2/8.15.2/Debian-10) with ESMTP id x84Ih45B001890
	for <root@localhost>; Thu, 5 Sep 2019 03:43:05 +0900
Message-ID: <350561.215167062-sendEmail@l0ft>
From: "joey@protonmail.com" <joey@protonmail.com>
To: "root@localhost" <root@l0ft>
Subject: The secret
Date: Wed, 4 Sep 2019 18:43:04 +0000
X-Mailer: sendEmail-1.56
MIME-Version: 1.0
Content-Type: multipart/mixed; boundary="----MIME delimiter for sendEmail-337284.555965201"

This is a multi-part message in MIME format. To properly display this message you need a MIME-Version 1.0 compliant Email program.

------MIME delimiter for sendEmail-337284.555965201
Content-Type: text/plain;
        charset="iso-8859-1"
Content-Transfer-Encoding: 7bit

This is the last document I was able to get from their servers.

------MIME delimiter for sendEmail-337284.555965201
Content-Type: application/x-zip-compressed;
        name="sr.zip"
Content-Transfer-Encoding: base64
Content-Disposition: attachment; filename="sr.zip"

UEsDBBQACQAIAMoVJU8LkBFEhnEAAC53AAAZABwARGVza3RvcC9zZWNyZXRfcmVjaXBlLnBkZlVU
CQADbPhvXYL4b111eAsAAQToAwAABOgDAAAnHu2zMEiWk/HS60wQTd2Hj8KVEKUTAASQFDE1A0Yj
wzw0B/UdiU+inIuGGnQVFKxQ/9YQ5kZXR/4htjvB+4FAhiIBcA8f0Lwk4ZlSXdf5NR8MrUK913PF
k4VDSbaISFeA+Y2Y+p49lDdJxGPCxS2yXX6cd1GrS5o1Em6R8Hp4ab+RIlZJ3NXbuj5mhab+CO6K
```

Let's extract the base64 encoded attachment, decode it and get the flag using `somepassword`.

The flag is: `1337.MD{there_you_go_joey_has_no_chance}`
