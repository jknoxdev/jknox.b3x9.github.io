---
title: CTF - smashthestack - level 1
description: My first CTF, allreight
date: 2024-11-07
scheduled: 2024-11-07
layout: layouts/post.njk
tags:
  - ctf
---

this is my first attempt at doing a CTF, just finished level 1, time to write up all the things. 

## smashthestack

https://www.smashthestack.org/blowfish.html

first step is to telnet to the host:

```
Trying 84.22.114.35...  
Connected to blowfish.smashthestack.org.  
Escape character is '^]'.  
  
  
Encrypted Password is: GungJnfRnfl  
  
Connection closed by foreign host.
```

This gives us the hash: GungJnfRnfl

At first, of course, I thought this was way more complicated that it should be and I decided to try to get the hash identified. 

https://github.com/blackploit/hash-identifier/blob/master/hash-id.py

```bash   #########################################################################  
  #########################################################################  
  #     __  __                     __           ______    _____           #  
  #    /\ \/\ \                   /\ \         /\__  _\  /\  _ `\         #  
  #    \ \ \_\ \     __      ____ \ \ \___     \/_/\ \/  \ \ \/\ \        #  
  #     \ \  _  \  /'__`\   / ,__\ \ \  _ `\      \ \ \   \ \ \ \ \       #  
  #      \ \ \ \ \/\ \_\ \_/\__, `\ \ \ \ \ \      \_\ \__ \ \ \_\ \      #  
  #       \ \_\ \_\ \___ \_\/\____/  \ \_\ \_\     /\_____\ \ \____/      #  
  #        \/_/\/_/\/__/\/_/\/___/    \/_/\/_/     \/_____/  \/___/  v1.2 #  
  #                                                             By Zion3R #  
  #                                                    www.Blackploit.com #  
  #                                                   Root@Blackploit.com #  
  #########################################################################  
--------------------------------------------------  
  
Not Found.  
--------------------------------------------------  
HASH: GungJnfRnfl  
  
Not Found.  
--------------------------------------------------  
HASH: 'GungJnfRnfl'  
  
Possible Hashs:  
[+] DES(Unix)  
--------------------------------------------------  
HASH: 
```

Another rabbit hole!  I mean Success! 
Now, just need a regex to grab just the line above the from the hashcat output : 
https://regexr.com/

```bash
➜  hashcat --example-hashes  | egrep '(.*Name.*DES)' -B 1      
Hash mode #1500  
 Name................: descrypt, DES (Unix), Traditional DES  
--  
Hash mode #12400  
 Name................: BSDi Crypt, Extended DES  
--  
Hash mode #14000  
 Name................: DES (PT = $salt, key = $pass)  
--  
Hash mode #14100  
 Name................: 3DES (PT = $salt, key = $pass)  
--  
Hash mode #24410  
 Name................: PKCS#8 Private Keys (PBKDF2-HMAC-SHA1 + 3DES/AES)  
--  
Hash mode #24420  
 Name................: PKCS#8 Private Keys (PBKDF2-HMAC-SHA256 + 3DES/AES)
```

Ran this with every hash type, and came up with nothing.. 
maybe it's simpler than I thought: 

https://github.com/Ciphey/Ciphey

```bash
sudo apt install python3-full

python3 -m venv .venv
source .venv/bin/activate

pip3 install -U pip
python3 -m pip install -U ciphey

pip3ls
install -U ciphey
```
^^^ didnt work, found in notes, 
https://github.com/Ciphey/Ciphey/issues/789


```bash
sudo apt install cargo
```
compiled this after installing rust: 
https://github.com/bee-san/Ares

also check out this blog!
https://skerritt.blog/


**omg**:
https://raw.org/tool/caesar-cipher/


ThatWasEasy


lets try it in ares anyways: 

```bash
# add to vim for rust install
export PATH=$PATH:/home/arx/.cargo/bin

source ~/.zshrc

ares -d -t GungJnfRnfl

# no results without humans

```


CyberChef: 
just drag and drop one of the rot modules:

![[https://imagedelivery.net/h6duaPVMwqSx6OPYA68aOw/ed8a8b9d-bbf5-42a0-d8b5-900104253f00/public]]

CyberChef official link:
![[https://gchq.github.io/CyberChef/#]]


also:
https://github.com/isaka-james/caesar

you have to have a crib with this one:

run the installer 

```bash
cd ~/apps
git clone https://github.com/isaka-james/caesar.git
cd caesar
chmod 755 install.sh
sudo ./install.sh

# exit and reopen
caesar -d "GungJnfRnfl" -t "T"

```



### level 2

```bash
ssh -l level2 blowfish.smashthestack.org -p 2222
# password ThatWasEasy
```

```bash
ssh -oHostKeyAlgorithms=+ssh-dss -l level2 blowfish.smashthestack.org -p 2222 -P 'ThatWasEasy'
```


```bash
ssh -oHostKeyAlgorithms=+ssh-dss -l level2 blowfish.smashthestack.org -p 2222 -P 'ThatWasEasy'  

# below is output:
The authenticity of host '[blowfish.smashthestack.org]:2222 ([84.22.114.35]:2222)' can't be established.  
DSA key fingerprint is SHA256:1Q3XwvHjBd3HLHXflel346c1sfdHE7ok0+gfSqEdf1Q.  
This key is not known by any other names.  
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes  
Warning: Permanently added '[blowfish.smashthestack.org]:2222' (DSA) to the list of known hosts.  
level2@blowfish.smashthestack.org's password:    
  _        __                          ___              __    
/\ \     /\_ \                       /'___\ __         /\ \           
\ \ \____\//\ \     ___   __  __  __/\ \__//\_\    ____\ \ \___       
 \ \ '__`\ \ \ \   / __`\/\ \/\ \/\ \ \  __\/\ \  /' __\\ \  _ `\     
  \ \ \_\ \ \_\ \_/\ \_\_\ \ \_/ \_/ \ \ \_/\ \ \/\__  `\\ \ \ \ \    
   \ \____/ /\____\ \____/\ \___x___/'\ \_\  \ \_\/\____/ \ \_\ \_\  
    \/___/  \/____/\/___/  \/__//__/   \/_/   \/_/\/___/   \/_/\/_/  
    wargame ++ smashthestack.org ++ now in version 2.0  
     
    1. Thou shalt NOT root or otherwise harm the box.  
    2. Thou shalt NOT do any network access from this box.  
    3. Thou shalt NOT use /tmp, /levels/tmp or /var/tmp for personal code.    
    4. Thou shalt give the root pass to l3thal if you manage to change it.  
       
    Passwords are in /pass.  
    There is a README in each users home directory.  
    /tmp && /var/tmp will be flushed daily by cron.  
    Use /code plz for umm, code ;D  
    IF YOU LEAVE FILES IN /levels/tmp U SUCK ..plz remove them kthnx! ;D  
    The password for the last level will get you into  
    Tux, the more advanced wargame. Join #blowfish on    
    irc.smashthestack.org with any questions.    
  
    Admin - s0ttle@smashthestack.org
```
That's it for level 1, onward to level 2 ! 

<img src="https://imagedelivery.net/h6duaPVMwqSx6OPYA68aOw/45aa7ce1-cac6-4c52-9779-6c70e667df00/public" alt="">
