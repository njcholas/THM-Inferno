# THM-Inferno

easy ctf writeup

------------------------------------------------------

nmap scan: 

      Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-04 17:07 EDT
      Nmap scan report for inferno.thm (10.10.227.76)
      Host is up (0.21s latency).
      Not shown: 958 closed ports
      PORT      STATE SERVICE           VERSION
      21/tcp    open  tcpwrapped
      22/tcp    open  ssh               OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
      | ssh-hostkey: 
      |   2048 d7:ec:1a:7f:62:74:da:29:64:b3:ce:1e:e2:68:04:f7 (RSA)
      |   256 de:4f:ee:fa:86:2e:fb:bd:4c:dc:f9:67:73:02:84:34 (ECDSA)
      |_  256 e2:6d:8d:e1:a8:d0:bd:97:cb:9a:bc:03:c3:f8:d8:85 (ED25519)
      23/tcp    open  telnet?
      25/tcp    open  smtp?
      |_smtp-commands: Couldn't establish connection on port 25
      80/tcp    open  http              Apache httpd 2.4.29 ((Ubuntu))
      |_http-server-header: Apache/2.4.29 (Ubuntu)
      |_http-title: Dante's Inferno
      88/tcp    open  kerberos-sec?
      106/tcp   open  pop3pw?
      110/tcp   open  tcpwrapped
      389/tcp   open  tcpwrapped
      443/tcp   open  tcpwrapped
      464/tcp   open  tcpwrapped
      636/tcp   open  tcpwrapped
      777/tcp   open  tcpwrapped
      783/tcp   open  tcpwrapped
      808/tcp   open  ccproxy-http?
      873/tcp   open  tcpwrapped
      1001/tcp  open  webpush?
      1236/tcp  open  tcpwrapped
      1300/tcp  open  tcpwrapped
      2000/tcp  open  tcpwrapped
      2003/tcp  open  tcpwrapped
      2121/tcp  open  ccproxy-ftp?
      2601/tcp  open  zebra?
      2602/tcp  open  ripd?
      2604/tcp  open  ospfd?
      2605/tcp  open  bgpd?
      2607/tcp  open  connection?
      2608/tcp  open  wag-service?
      4224/tcp  open  xtell?
      5051/tcp  open  ida-agent?
      5432/tcp  open  postgresql?
      5555/tcp  open  freeciv?
      5666/tcp  open  nrpe?
      6346/tcp  open  gnutella?
      6566/tcp  open  sane-port?
      6667/tcp  open  irc?
      |_irc-info: Unable to open connection
      8021/tcp  open  ftp-proxy?
      8081/tcp  open  blackice-icecap?
      8088/tcp  open  radan-http?
      9418/tcp  open  git?
      10000/tcp open  snet-sensor-mgmt?
      10082/tcp open  amandaidx?
      Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
      
------------------------------------------------------

acessing the website on port 80:

![1](https://user-images.githubusercontent.com/67773431/171983781-a073c079-7674-4a32-891f-d1d9f0fa6472.png)

------------------------------------------------------

using gobuster to find new directories on the website:

![2](https://user-images.githubusercontent.com/67773431/171984974-fc873910-f9e9-4e9c-b2a4-351e2c052a4a.png)

![3](https://user-images.githubusercontent.com/67773431/171984981-ffbc7c2b-325e-4d15-9a68-0a5e3e6870b0.png)

------------------------------------------------------

acessing the inferno directory:

![4](https://user-images.githubusercontent.com/67773431/171985057-d713a666-6e90-4296-b89f-1c9bca292b9c.png)

------------------------------------------------------

before we try bruteforce this login we need to know what type of request we are sending to the server
when a login attempt is made, wireshark can be used to see it. i started capting my tun0 network interface and got this:

![5](https://user-images.githubusercontent.com/67773431/171985112-0f895444-2413-4e7f-8bfc-f3ea314661ce.png)

following the stream:

![6](https://user-images.githubusercontent.com/67773431/171985142-cb41c502-e06b-416e-b3c0-598671a749bd.png)

------------------------------------------------------

therefore we know the type of request we are sending to the server. 

trying to bruteforce the login:

![7](https://user-images.githubusercontent.com/67773431/171985361-85980405-2040-4e2b-83a5-e2f5491954f7.png)

------------------------------------------------------

after loging with this credencials we can see a web based IDE called Codiad

![8](https://user-images.githubusercontent.com/67773431/171986418-7de245f1-0fa7-4d87-b228-5cddb08e091b.png)

------------------------------------------------------

searching some exploit:

![9](https://user-images.githubusercontent.com/67773431/171986511-4bba29c9-0d1f-44fd-a6f4-664eb9fbd8eb.png)

------------------------------------------------------

https://github.com/WangYihang/Codiad-Remote-Code-Execute-Exploit.git

running the exploit:

![10](https://user-images.githubusercontent.com/67773431/171986591-26505c7f-9a5f-49b9-b460-933068743d81.png)

(run the two commands above the exploit before)

then you get a webshell!

------------------------------------------------------

digging into the files you can find something interest at /home/dante/Downloads

this file contains a hexadecimal text and after you translate the content for strings you get the ssh password for the user dante

![11](https://user-images.githubusercontent.com/67773431/171986653-26636d86-a588-4f0f-b394-a33393cab01d.png)

------------------------------------------------------

use sudo -l to see your permissions

you can use tee for set the dante user for root without password

echo 'dante ALL=(ALL) NOPASSWD:ALL' | sudo tee -a /etc/sudoers

sudo su to get root
















