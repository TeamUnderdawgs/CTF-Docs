## Owner:

> CaptainFreak

#### Solved?

 - No

#### Tried:

 > No, less on hacker numbers, we are recruiting :)

#### Solutions:

The challenge was to guess that server only accepts HTTP /3. Then see that nginx is misconfigured to Path traversal. Follow hints and get to a signup and role changing fucntionality, become admin.

Steps to solve the challenge:

1. Compile the curl to support `--http3` http3 requests.
2. Read home html and goto /static.
3. Find that directory listing as well as path traversal is possible with nginx.
4. Find out link.txt in directories.
5. Hints for /check.php, which creates user and lets them impersonate each other.
6. Impersonate as admin.

Reference:
1. https://github.com/curl/curl/blob/master/docs/HTTP3.md
2. https://github.com/InsecurityAsso/inshack-2019/blob/master/unchained/writeup.md
3. https://graneed.hatenablog.com/entry/2020/02/09/143359