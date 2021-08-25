# BrainPan BufferOverflow VulnHub Machine 
Hello! I am [Mohamed](https://twitter.com/0xMohomiester) This is My WriteUp for Brainpan BufferOverflow VulnHub Machine ok Let's start with download machine from [VulnHub Brainpan](https://www.vulnhub.com/entry/brainpan-1,51/) and setup it on vmware or virtual box as you like I already finished the setup you shoud see :

![brainp](https://user-images.githubusercontent.com/47929033/130876624-1b4dc000-bcb9-467c-87db-30b5ec3af179.png)

That's great Let's start with enumeration I need to now ip address of machine so i will use command 
`sudo netdiscover` to find ip address: 

![Screen Shot 2021-08-26 at 12 44 34 AM](https://user-images.githubusercontent.com/47929033/130876911-b5242212-661d-4646-989c-c8772a9b85d2.png)

as you can see the ip address: `192.168.1.6` with `vmware,inc` hostname now we need to perform port scanning I will use nmap to scan for open port and service on machinewith command:

`sudo nmap 192.168.1.6` : 

![Screen Shot 2021-08-26 at 12 48 27 AM](https://user-images.githubusercontent.com/47929033/130877152-5af6c1f5-28b4-4bc2-bc16-dc239560a2c5.png) 

as you can see two ports are open `9999,10000` let's check port `9999` with netcat using command 
`nc -nvv 192.168.1.6 9999`, maybe port `10000` is web server let's check it with open it in web browser: 

![Screen Shot 2021-08-26 at 12 49 00 AM](https://user-images.githubusercontent.com/47929033/130877337-dbaddc9d-fb3c-4a03-add2-ed49cbea2e7e.png)

![Screen Shot 2021-08-26 at 12 49 20 AM](https://user-images.githubusercontent.com/47929033/130877350-d1d98332-3831-4c9b-a820-031966af52d9.png) 

as you can see this is the vulnerable service: `Brainpan`, That's Great, It's web server that run on port `10000` now we need to discover some interesting directories that hidden in webiste by using Gobuster using command: 
`gobuster dir  --url http://192.168.1.13:10000/  -w /home/kali/Desktop/directory-list-2.3-medium.txt`




