
####  Challenge : Scripting
* Difficulty : Easy
* Level : Beginner
* Category : Web


![alt text](images/f1.png)
```

#### Solved?

Yes

#### Tried:

* As the challenge header says "Fingerprint" , we just run a dirsearch with the common paths and found `.git` directory was accessible.

![alt text](images/f2.png)

* Then we did git dumping where we fetched all common files and many refs possible aswell objects.

![alt text](images/f3.png)

* In the `.git/logs/HEAD` file we got our flag.