# ISSS CTF Write-up (April 2017)
The first CTF event I attended was hosted by the Information & Systems
Security Society at the University of Texas at Austin. My team came in
sixth place, finding the flags highlighted in green below.

![Challenges](https://raw.githubusercontent.com/mcastorina/ctfs/master/isss-2017/img/2017-04-13-000748_581x700_scrot.png)

## Challenges
Below is a description of how we found the flags for each of the
challenges highlighted. We also figured out "What The Math?", "New
Beginnings", and "bin_mirrors" after the competition ended ("PHP Magic
Wrap" was explained to us too), so I will go ahead and document those
as well.

### bin_mirrors
This challenge was to figure out what a binary was doing and supply the
correct input so it will give us the flag. My approach was to first try
running the program with various arguments. This revealed it needed two
arguments, so I used gdb to figure out where it was failing and what it
was checking for.

I found here it was calling `atoi` on the first argument and comparing it
against `0x9`, so I know it has to be a number greater than 9. Later on,
it then compared it to `0x7a69` (decimal `31337`). Using this value, it
appeared to pass all the tests for the first argument.
```
 8048f15:	e8 d6 f5 ff ff       	call   80484f0 <atoi@plt>
 8048f1a:	83 c4 10             	add    $0x10,%esp
 8048f1d:	89 45 f4             	mov    %eax,-0xc(%ebp)
 8048f20:	83 7d f4 09          	cmpl   $0x9,-0xc(%ebp)

 8048f60:	81 7d f4 69 7a 00 00 	cmpl   $0x7a69,-0xc(%ebp)
 8048f67:	74 1a                	je     8048f83 <main+0x11c>
```
Testing with `31337` confirms we have gotten further.
```
$ ./bin_mirrors 31337 1
*******************************************************************************
[*] Hello there...
[*] I would like to show you the path that will allow you to exit the Matrix. Unfortunately, I cannot find the file with the key to the secret phone line
[*] Perfect! Let me see if that works...
The vault is not where you said it will be
```
Looking through the code, we see it calls `fopen` on the argument, so it
is looking for a file. Let's give it `/dev/null`.
```
$ ./bin_mirrors 31337 /dev/null
*******************************************************************************
[*] Hello there...
[*] I would like to show you the path that will allow you to exit the Matrix. Unfortunately, I cannot find the file with the key to the secret phone line
[*] Perfect! Let me see if that works...
[*] We got it! The flag...I mean...key...is: flag{}
[*] Now let's exit the Matrix
*******************************************************************************
```
Huh, well that's clearly not the flag. Digging through the assembly
revealed `c_func` was reading the string from the file and xoring the values with a
known value. It was also checking that each character was either a `0` or
a `1`. Fortunately I didn't have to guess the string and I could copy the
value from gdb into a file called `test`.
```
$ echo 111101001101001 > test
$ ./bin_mirrors 31337 test
*******************************************************************************
[*] Hello there...
[*] I would like to show you the path that will allow you to exit the Matrix. Unfortunately, I cannot find the file with the key to the secret phone line
[*] Perfect! Let me see if that works...
[*] We got it! The flag...I mean...key...is: flag{red_pill_truth}
[*] Now let's exit the Matrix
*******************************************************************************
```
And there's our flag.

### Forever Alone
The challenge here was to write a program that would read input and
return the character that there was only one of. I figured I could use
bash to do this easily for a single input:
```bash
$ xsel -ob | python -c "print('\n'.join(input()))" | sort | uniq -c | sort -n | head -1 | awk '{print $2}'
```
Yes, it's a long chain, but it works. I had to do this multiple times,
so it was easier to use a python script to interface with the server.
```python
import socket
from collections import Counter

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((HOST, PORT))

# Read banner
data = s.recv(4096).decode()

# REPL
while data:
    data = s.recv(4096).decode().strip()
    print(data)

    freqs = Counter(data)
    for k in freqs:
        if freqs[k] == 1:
            s.send((k+'\n').encode())
            break

s.close()
```

### What The Math?
Similar to "Forever Alone", this challenge was to read in a sum and an
array of integers and return the two integers in the array that summed
to the provided number.
```python
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((HOST, PORT))

# Read banner
data_sum = s.recv(4096).decode()

# REPL
while data_sum:
    data_sum = s.recv(4096).decode().strip()
    print(data_sum)

    data_arr = None
    # Sometimes the array is sent with the sum, other times it is not
    if 'Array: ' in data_sum:
        data_sum, data_arr = data_sum.split('\n')[:2]
    else:
        data_arr = s.recv(4096).decode().strip()

    # Convert to ints
    sum_num = int(data_sum.split('Sum: ')[1])
    sum_arr = data_arr.split('Array: ')[1][1:-1].split(', ')
    sum_arr = [int(x) for x in sum_arr]

    # Find solution
    for num in sum_arr:
        if (sum_num - num) in sum_arr:
            s.send(("%d %d\n" % (num, sum_num - num)).encode())
            break

s.close()
```

### NSA Math Test
Like the two other programming problems, we had to read in a number and
find the two primes that multiplied to that number.

### Under Pressure
For this challenge we were provided a 7z archive to find the flag in. The
hint was that sometimes people get confused with compression and encoding.
The contents of the file looked like it was base64 encoded, so decoding
that revealed a message:
```bash
$ cat UnderPressure.txt | base64 -d -
Nice work decoding this. Things still feel a little compressed.                              ·ÝXcompressedKËIL¯vÎÏ-(2..Î4ȋOÌswÍK6HÉÌsO
                                       JOÉts3.2Î+/ÉÈôK7­å¿	­4
```
I then removed the message and wrote the compressed bytes to a file to
see what type it was. Seeing it was gzip, all I had to do was gunzip it
and the flag was inside.
```bash
$ cat UnderPressure.txt | base64 -d - | tail +2 > testfile
$ file testfile
testfile: gzip compressed data, was "compressed", last modified: Thu Mar 30 13:57:11 2017, from Unix
$ mv testfile testfile.gz && gunzip testfile.gz
$ cat testfile
flag{Compr3ssi0n_anD_Enc0dinG_aRe_diFF3r3nt_thiNg5}
```

### FlagSync
This challenge was also a 7z file, but the contents were a git
repository with two files. Looking at the git log revealed that they just
moved from mercurial to git. It also appeared they fired the guy who put
the secret key (the flag) directly in the file. Fortunately for us, the
`.hg` directory was still around, so we could view the log and the diff
of the files using the commands below.
```bash
$ hg log flagsync.py
changeset:   4:29df3402992d
tag:         tip
user:        greta
date:        Tue Dec 01 15:51:24 2015 -0600
summary:     Peter thought it would be good to put the flag in the source code. Removed the flag and fired Peter.

changeset:   3:6c397689545b
user:        peter
date:        Tue Dec 01 15:43:27 2015 -0600
summary:     Moved connection details into source to remove the os module dependency.

changeset:   2:b5607d7a808c
user:        tara
date:        Tue Dec 01 15:36:57 2015 -0600
summary:     Flag and connection details read in from environment variables that are loaded from another script. Loader script not in repo for obvious reasons.

changeset:   0:6e26ce2373ad
user:        rosa
date:        Tue Dec 01 15:07:23 2015 -0600
summary:     Initial commit to start repository

$ hg cat -r 3 flagsync.py
#!/usr/bin/env python

##
# File: flagsync.py
# Description: Sync the flag to a remote system.
##

##
# External modules
##
import socket			# Network interaction, duh


##
# Set connection details for host and port
# Connect to remote service
# Send flag and close the connection
##
def sync_flag():
	# Set connection details for host and port
	host = '127.0.0.1'
	port = 9001
	flag = 'flag{Develop3rs_Dev3l0pers_D3velop3rs_Devl0p3rs!}'

	# Connect to remote service
	s = socket.socket()
	s.connect((host, port))

	# Send flag and close the connection
	s.send(flag)
	s.close()


##
# Make it do the thing
##
if __name__ == '__main__':
	sync_flag()
```
Sure enough, the flag was in the diff.

### Dird
A dird is a dog inside a bird. This challenge we were simply provided the image below.

![Dird](https://raw.githubusercontent.com/mcastorina/ctfs/master/isss-2017/img/dird.jpg)

Using `binwalk` I found there was another `jpeg` within the image,
so I extracted it.
```bash
$ binwalk -D 'jpeg image:jpeg' dird.jpg

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, EXIF standard
12            0xC             TIFF image data, big-endian, offset of first image directory: 8
56            0x38            JPEG image data, JFIF standard 1.01
```
The extracted image contained the flag.

![Dird Secret](https://raw.githubusercontent.com/mcastorina/ctfs/master/isss-2017/img/dird-secret.jpg)

### New Beginnings
This challenge contained an input field for pinging an address. It
blacklisted a lot of bash control characters except one: newline.
We found we could send "%0Als" and the contents were given back to
us. We couldn't send arguments though, only a single command.
Fortunately redirecting worked too, so we could `cat<index.php`. The file
we wanted wasn't in the root directory however, so we found it with the
`find` command. This revealed the path
`security/through/obscurity/does/not/work/flag.txt`. Now
we just sent the URL encoded command and we got our flag:
`%0Acat%3Csecurity/through/obscurity/does/not/work/flag.txt`.

![Ping Exploit](https://raw.githubusercontent.com/mcastorina/ctfs/master/isss-2017/img/ping.png)

### H4x0r Site Enumeration
I really liked this challenge because it made me analyze the case to
find the flag. I began by using `httrack` to download the entire website
and `grep` for the word "flag" but none was found. Seeing the site was in
French, I tried searching for the French word for flag, but that didn't
work either. I started reading the website which gave a hint
on where to start. It gave a link to a [Web Application Security Testing Cheat Sheet](https://www.owasp.org/index.php/Web_Application_Security_Testing_Cheat_Sheet).
On the wiki page, it said to look at the `/robots.txt`. Sure enough,
there was a file with the flag in there.

### PHP Magic Wrap
I had no idea how to approach this challenge, but fortunately it was
explained at the end by one of the coordinators of the CTF. Because the
website was using PHP, there's typically an admin page. Navigating to
`/admin` reveals a username and password form, which we don't have. This website
also used PHP's file getter, so we can request the file "admin.php"
as base64 encoded to avoid it being evaluated as PHP. This can be done
with a URI looking something like the one below.
```
http://www.somesite.com/index.php?page=php://filter/convert.base64-encode/resource=admin.php
```
We then decode it, and it has the username and password for us to
login. Once logged in, there was a TODO list with the flag in it.

## Takeaways
I had a lot of fun at this event, and I now know I want to continue
exploring CTF challenges. I learned a new trick to get PHP source code,
and I also learned that I need to work on my Reverse Engineering.
Also, [pwntools](https://github.com/Gallopsled/pwntools) might be better
for CTF events.
