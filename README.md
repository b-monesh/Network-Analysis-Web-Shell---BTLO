# BTLO PCAP Challenge – Web Shell Analysis  

another day another task, as I was currently working on the BTLO labs and I found a lovely PCAP challenge on a web shell with the description:  

> “The SOC received an alert in their SIEM for ‘Local to Local Port Scanning’ where an internal private IP began scanning another internal system. Can you investigate and determine if this activity is malicious or not? You have been provided a PCAP, investigate using any tools you wish.”  

---

## 1. What is the IP responsible for conducting the port scan activity?  

I identified the port scan packets and figured out the source (attacker) IP as `10.251.96.4` 

![IP responsible for port scan](https://github.com/b-monesh/Network-Analysis-Web-Shell---BTLO/blob/main/images/1.png)

---

## 2. What is the port range scanned by the suspicious host?  

In order to find this I used the great feature of Wireshark **Statistics → Conversations** to find the total number of ports the IP scanned, which ranges from `1–1024`

![Port range scanned](https://github.com/b-monesh/Network-Analysis-Web-Shell---BTLO/blob/main/images/2.png)

---

## 3. What is the type of port scan conducted?  

While analyzing the port scan packets I found the successful identification of port 80 which follows a classic **SYN scan** — where the destination responds with SYN-ACK and the source immediately sends an RST packet. Therefore I concluded it as **TCP SYN** scan.

![Type of port scan](https://github.com/b-monesh/Network-Analysis-Web-Shell---BTLO/blob/main/images/3.png)

---

## 4. Two more tools were used to perform reconnaissance against open ports, what were they?  

Apart from the port scan I see a lot of HTTP packets travelling back and forth, so I followed the streams.  
I found the attacker tried using `Gobuster 3.0.1` for directory brute forcing and also used `SQLmap 1.4.7` to try SQL injection on fields **username** and **password**.  

![Recon tools used](https://github.com/b-monesh/Network-Analysis-Web-Shell---BTLO/blob/main/images/4.png)

---

## 5. What is the name of the PHP file through which the attacker uploaded a web shell?  

By analyzing the packets continuously for a longer period of time I found there is a flow of packets trying to misuse the file upload feature in **upload.php** which is actually referred by **editprofile.php**. I figured out it as some file inclusion vulnerability. So the answer for this is `editprofile.php` 

![PHP file used for upload](https://github.com/b-monesh/Network-Analysis-Web-Shell---BTLO/blob/main/images/5.png)

---

## 6. What is the name of the web shell that the attacker uploaded?  

Later in the packet stream I found the attacker tried to upload a malicious PHP file which goes into the uploads folder named `dbfunctions.php`

![Web shell name](https://github.com/b-monesh/Network-Analysis-Web-Shell---BTLO/blob/main/images/6.png)

---

## 7. What is the parameter used in the web shell for executing commands?  

It goes with the parameter named `cmd`  

![Web shell parameter](https://github.com/b-monesh/Network-Analysis-Web-Shell---BTLO/blob/main/images/7.png)

---

## 8. What is the first command executed by the attacker?  

The first command the attacker tried is to determine the ID of the user the PHP actually runs as and, as I expected, it returns **www-data**. So the command is `id`  

![First command executed](https://github.com/b-monesh/Network-Analysis-Web-Shell---BTLO/blob/main/images/8.png)

---

## 9. What is the type of shell connection the attacker obtains through command execution?  

Later I found the attacker injects a Python command-line payload which is a typical `reverse shell` one.  

![Type of shell connection](https://github.com/b-monesh/Network-Analysis-Web-Shell---BTLO/blob/main/images/9.png)

---

## 10. What is the port he uses for the shell connection?  

After decoding the URL from CyberChef I figured out the port number that the attacker listens on is `4422`

![Port used for shell connection](https://github.com/b-monesh/Network-Analysis-Web-Shell---BTLO/blob/main/images/10.png)

---
