## DRIFTINGBLUE6 VULNHUB MACHINE

 Hello!  i am mohamed! This is my WriteUp for DriftingBlue6 VulnHub Machine Let's start with Setup Machine you can find Machine from [DriftingBlue6](https://www.vulnhub.com/entry/driftingblues-6,672) ok! setup the machine on vmware  i already finished the setup you should see this : 
 
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

it's login page, that's great we can try another directory like spammer the we found on gobuster : 

![spammer](https://user-images.githubusercontent.com/47929033/130625581-0f93acc5-9eab-475a-b6f0-0aa583c11216.png)

it's ZIP file let's try to extract it with terminal 









