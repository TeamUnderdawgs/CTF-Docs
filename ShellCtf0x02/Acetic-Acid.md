
####  Challenge : Crypto


* Difficulty : Easy
* Level : Beginner

**Dica_CITECA**

![alt text](images/a1.png)


#### Solved?

Yes

#### Tried:

* The challenge had a page depicting ch3cooh which meant acetic acid=vinegar

![alt text](images/a2.png)

* Next upon inspecting the source code, we stumpled that there was a ajax request which is fetching the current response of the page from `ch3cooh.acid.php` file.

![alt text](images/a3.png)

*  So we went to the link and got a weird paragraph now from the clues it was clear that it was Vigenere cipher but we had no key so we would bruteforce on it we used:

> https://www.guballa.de/vigenere-solver

![alt text](images/a4.png)

*  Now after breaking we got this link at end bit.ly/chhhcooh which opened  a page which asked us key clearly the key was
`divyanshu` which was used to encrypt it, entering the key gave us the flag :

![alt text](images/a4.png)

` shellctf{ch3mistry_is_myst3ry}`