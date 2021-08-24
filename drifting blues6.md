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




