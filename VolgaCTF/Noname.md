###  NO NAME
####  Solved 
        Yes
In the given challenge we were given two files encrypted and encrypted.py where encrypted.py was used to encrypt the files and encrypted was a flag file which was in encrypted format so the code was 
```
from Crypto.Cipher import AES
from secret import flag
import time
from hashlib import md5


key = md5(str(int(time.time()))).digest()
padding = 16 - len(flag) % 16
aes = AES.new(key, AES.MODE_ECB)
outData = aes.encrypt(flag + padding * hex(padding)[2:].decode('hex'))
print outData.encode('base64')
```
Now here we can see that time is used as a key to encrypt the flag and also as per hint the challenge was made two days ago so epoch time should have been taken as two days ago and to decrypt the flag we need to bruteforce among those values so we build our code as:
``` 
from Crypto.Cipher import AES
from hashlib import md5
from base64 import b64decode
import time

encrypted= b64decode("uzF9t5fs3BC5MfPGe346gXrDmTIGGAIXJS88mZntUWoMn5fKYCxcVLmNjqwwHc2sCO3eFGGXY3cswMnO7OZXOw==")

def decryption(time):
	time = md5(str(time).encode()).digest()
	aes = AES.new(time, AES.MODE_ECB)
	flag = aes.decrypt(encrypted)
	if b"VolgaCTF" in flag:
		print(flag)
		quit()
tst = 1585142000

for i in range(pow(2,21)):
	decrypt(tst)
	tst += 1
```
The above script simply start its time from 1585142000 i.e. Wednesday, March 25, 2020 and runs a for loop for a range
of 2097152 times to check the correct time stamp if timestamp is correct then flag will be decrypted as 
#####  VolgaCTF{5om3tim3s_8rutf0rc3_i5_th3_345iest_w4y}
