## DRIFTINGBLUE6 VULNHUB MACHINE

 Hello!  i am mohamed! This is my WriteUp for DriftingBlue6 VulnHub Machine Let's start with Setup Machine you can find Machine from [DriftingBlue6](https://www.vulnhub.com/entry/driftingblues-6,672) ok! download it and setup it on vmware i already finished the setup you should see this : 
 
 ![setup](https://user-images.githubusercontent.com/47929033/130618015-aabced47-56df-4f42-b065-adddd93df422.png)

so! Let's Start with Enumeration i will use netdiscover to find ip address of machine i will use command: `sudo netdiscover`  on Kali Linux i found ip 192.168.1.6 with vmware,inc hostname : 

![net](https://user-images.githubusercontent.com/47929033/130619421-935dc7bf-a883-4c47-bc3d-35392589daa9.png)

Ok That's Great now we can use nmap to scan for open ports and service on machine i used command:
`nmap -Pn -sV -A -sC 192.168.1.6` i found only 1 port open on HTTP web service on port 80 : 

![nmap](https://user-images.githubusercontent.com/47929033/130621054-67f6eaf7-595d-4951-9826-52eed45c40cb.png)

ok let's see web page with browser : 

![web](https://user-images.githubusercontent.com/47929033/130621692-b3e4cf98-e04a-4f64-9bd6-92d996bd7a8d.png)

as you can see >> CHARLES BROWN .. :)

we need now to discover some directories that hidden in website let's do it with gobuster i used command: 
`gobuster dir --url http://192.168.1.6/ -w /home/kali/Desktop/directory-list-2.3-medium.txt -x php,html,css`
i found some interesting directory like (robots.txt and spammer) Just like the photo below: 

![dir](https://user-images.githubusercontent.com/47929033/130623604-a1f861cf-e22c-4ab9-a624-303616c0cd58.png)

let's see robots.txt directory in web browser: 

![ro](https://user-images.githubusercontent.com/47929033/130623878-0db64a64-ea5f-4b07-844e-4f1317894a08.png)

oh!! i found directory "Disallowed" "/textpattern/textpattern" let's see it : 

![log](https://user-images.githubusercontent.com/47929033/130624859-fe2a1a68-b814-4bba-8ec2-f3d8815131de.png)

it's login page!!, that's great we can try another directory like spammer the we found on gobuster : 

![spammer](https://user-images.githubusercontent.com/47929033/130625581-0f93acc5-9eab-475a-b6f0-0aa583c11216.png)

it's ZIP file!! let's try to extract it on terminal nut the ZIP file was encrypted with password :

![ex](https://user-images.githubusercontent.com/47929033/130626167-915633dc-50f5-42de-8697-d6d0e2c59884.png)

we can use fcrack tool to crack password of ZIP file ... you can download fcrack with command : 
`sudo apt install fcrackzip` you can ask Google about fcrack tool, ok i will use command: 
`fcrackzip -u -D -p /home/kali/Desktop/work/rockyou.txt spammer.zip`
-D option for dictionary attack, -U option for unzip-file, -p for init password string, you can use 
`fcrackzip -h` for help options, ok let's do it i will use rockyou.txt to crack it: 

![crack](https://user-images.githubusercontent.com/47929033/130628068-5daaa8af-8445-44df-8088-3d9ffd074762.png)

as you can see password is: myspace4  let's extract file: 

![ext](https://user-images.githubusercontent.com/47929033/130628809-a1d5b6e2-35ad-4e37-85b9-549ea93f4249.png)

as you can see the file extracted is cred.txt ... this is the credentials for login page let's login with credentials: 

![log2](https://user-images.githubusercontent.com/47929033/130629662-599b0da2-2b55-4365-9067-0205dba2eeca.png)

as you can see we are in ok let's try to find way to get command execution on web server let check "content>files"  i found upload page that's great : 

![con](https://user-images.githubusercontent.com/47929033/130630153-2d2043c6-32db-4e4d-a4cf-ffb9d4218c4f.png)















