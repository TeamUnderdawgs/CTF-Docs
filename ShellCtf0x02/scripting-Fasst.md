
####  Challenge : Scripting
* Difficulty : Easy
* Level : Beginner

> There was a socket connection with the timeout of ~60 seconds, which  replies with some random `strings` which we need to md5 encrypt them and send it over again and again for all next strings until we reach the flag. 
```
$ nc 134.209.149.171 4100
Are you ready for the trip deep inside!Return MD5 of strings[Y/n]  Y
String: fuckyou
Send correct value!
```
#### Solved?

Yes

#### Tried:

* Since there  was an timeout limit to the connection, we need to write a script which automatically replies to server by converting the `reciever's` strings into MD5 until we reach the flag.

```python
###!/usr/bin/python3
import socket,time,haslib

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(('134.209.149.171',4100))

def computeMD5hash(my_string):
	m = hashlib.md5()
	m.update(my_string.encode('utf-8'))
	return m.hexdigest()

while True:
	time.sleep(0.2)
	recv_data = str(s.recv(1024)
	if 'Send correct' in recv_data:
		s.disconnect()
	if 'String:' in recv_data:
		get_last_val = str(recv_data).split(': ')[1].split('\\n')[0]
		print(get_last_val)
		md5_conv = computeMD5hash(get_last_val)
		s.send((md5_conv.encode()))
		time.sleep(0.1)
	else:
		toSend = input()
		s.send(bytes(toSend, "utf-8"))
```

* Once we run the script, there was around `125` string replies and then  server responded with the `Flag`
