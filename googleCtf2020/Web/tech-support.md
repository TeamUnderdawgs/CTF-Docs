## Owner:

> CaptainFreak

#### Solved?

 - Yes

#### Tried:

 - XSSing bot to update his address field to a Vuln JS which which reads flag and sends it to us.
 - An obfuscated link found in Bots referrer of previous page which was probably used by author to authenticate the bot to webapp, allows us to login to his account and get Flag.

#### Solutions:

Better solution was to Self XSS ourselve in addressfield on /me with payload that steals DOM of /flag. 

And XSS bot for adding our cookie on his /me too. So when in iframe he sees /me he will get our payload but on /flag, his cookie and his DOM will be there. But now we get the flag.

Another Solution, without cookie tossing was

Give iframe of attacker Site to bot which,

first, open /flag in an Iframe now this iframes DOM, after loading will have the flag.

After the above frame loads, we load another frame in which we use CSRF on /login to login the bot in our account where XSS payload is there.

on /me loads, our evil JS loads, now because of both frames having same origin, we read the DOM of first iframe from second iframe.

https://github.com/0xParrot/writeups/tree/master/GoogleCtfQuals2020/web/techsupport