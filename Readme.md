# Session Writeup: Pentesting Class - TryHackMe

## Date: August 11, 2024  
**Time:** 6:00 PM - 8:30 PM IST

---

### Topics Covered:

1. **Network Scanning with Nmap**
2. **Directory Enumeration with Gobuster and Dirb**
3. **Reverse Shell Connection Setup**
4. **Privilege Escalation**

---

### What I Learned

In this session, I focused on a hands-on approach to pentesting by working on the RootMe box from TryHackMe. Here's a step-by-step summary of what I learned and accomplished:

#### 1. **Deploy the Machine**
   - The first step was deploying the machine in TryHackMe (no answer needed here).

#### 2. **Port Scanning**
   - **Port Scanning with Nmap:**  
     I learned to scan the machine to identify open ports using the command:

     ```bash
     nmap -sV <ip_addr>
     ```

     - **Open Ports:**  
       The scan revealed two open ports:
       - Port 22: `ssh` (Secure Shell service)
       - Port 80: `http` (Apache web server)

     - **Apache Version:**  
       The version of Apache running on the target machine was `2.4.29`.

   - **Directory Enumeration with Gobuster:**  
     I used Gobuster to find directories on the web server. The command used was:

     ```bash
     gobuster dir -u <ip_addr> -w /root/Desktop/Tools/wordlists/dirbuster/directory-list-2.3-medium.txt
     ```

     - **Hidden Directory Found:**  
       The scan revealed a hidden directory `/panel/`.

#### 3. **Getting a Shell**
   - **Accessing the Panel:**  
     Navigating to `<ip_addr>/panel/` in the web browser, I found an upload form that could potentially be exploited to gain a reverse shell.

   - **Creating a PHP Reverse Shell:**  
     I created a `shell.php` file using a script from [pentestmonkey's GitHub repository](https://github.com/pentestmonkey/php-reverse-shell). After customizing the script with my AttackBox IP address and port, I attempted to upload the file.

   - **Overcoming Upload Restrictions:**  
     The server didn't accept `.php` files, so I modified the file extension to `.php5`, which was accepted.

   - **Netcat Listener Setup:**  
     I started a Netcat listener to catch the reverse shell:

     ```bash
     nc -lvnp 1234
     ```

   - **Triggering the Shell:**  
     Once the shell was uploaded to `/uploads/`, I navigated to the file in the browser and successfully gained access to the target machine.

   - **Retrieving `user.txt`:**  
     I found the `user.txt` file using the following command:

     ```bash
     find / -type f -name user.txt 2> /dev/null
     ```

     - **User Flag:**  
       The content of `user.txt` was `THM{y0u_g0t_a_sh3ll}`.

#### 4. **Privilege Escalation**
   - **Identifying SUID Files:**  
     To escalate privileges, I searched for files with SUID permissions using:

     ```bash
     find / -user root -perm /4000
     ```

     - **Exploitable File Found:**  
       The file `/usr/bin/python` stood out as a potential target for privilege escalation.

   - **Exploiting SUID with Python:**  
     I utilized a command from [GTFOBins](https://gtfobins.github.io/) to escalate privileges:

     ```bash
     python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
     ```

   - **Confirming Root Access:**  
     Running `whoami` confirmed that I had successfully gained root access.

   - **Retrieving `root.txt`:**  
     Finally, I found the `root.txt` file:

     ```bash
     find / -type f -name root.txt
     ```

     - **Root Flag:**  
       The content of `root.txt` was `THM{pr1v1l3g3_3sc4l4t10n}`.

---

