###  NO NAME
####  Solved 
        Yes
In the given challenge we were given a website a URL
[Alternate](https://alternative.q.2020.volgactf.ru:7780/)

So on opening thee website was a complete static website which said

```
Hello there!

Welcome to our first crypto challenge!

Try to find some secret information here.
```

So with the curiousity that whats hidden inside it i ran directory search and paramter fuzz with url but 
failed with no result next i decided to run a nmap scan over the host with port 7780 and aggresive scan 
```
 nmap alternative.q.2020.volgactf.ru -p 7780 -A
 ```
 
![Scan output](alternate.png)
here we can see that the SAN Subject Alternative Name was carrying our flag with itself the challenge catch was hiding in the word alternate and where most people got confused.

and finally our flag was 


#####  VolgaCTF{s0.many.fields.in.certificate.com}
