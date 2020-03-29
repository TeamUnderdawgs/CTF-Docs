## Owner:

> frost_b

#### Solved?

 - Yes

#### Solution:

ROX referred to XOR and I saw that one character input gave you one character output most probably it was Xoring our input each character with the flag

Output[i] = Input[i] ^ Flag[i]

So, Flag[i] = Output[i] ^ Input[i]

```
from pwn import *

charset="*"*26

r=remote("142.93.113.55",31087)
print(r.recv())
r.sendline("start")
r.recv()
r.sendline(charset)

resp= r.recv()
print(''.join([chr(ord(i)^ord('*')) for i in resp]))

r.close()
````

