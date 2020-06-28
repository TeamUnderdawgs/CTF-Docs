## Owner:

> CaptainFreak

#### Solved?

 -Yes

#### Tried:

1. Gynvael's node challenges helped in quickly solving this.

#### Solutions:

1. Express itslef uses npm package `qs` for parsing the query parameter and creating `req.query` object. It uses it with option of option of allowPrototypes by default for having query paramters as JS arrays and Objects.

References: 
a. Express Code: https://github.com/expressjs/express/blob/master/lib/middleware/query.js#L36
b. QS security considerations: https://www.npmjs.com/package/qs#parsing-objects

Challenges had following code:

```javascript
const express = require('express')
const path = require('path')
const app = express()

//Don't forget to redact from published source
const flag = '[REDACTED]'

app.get('/', (req, res) => {
    res.redirect('/page?path=index.html')
})

app.get('/page', (req, res) => {

    let path = req.query.path

    console.log(path)

    //Handle queryless request
    if(!path || !strip(path)){
        res.redirect('/page?path=index.html')
        return
    }

    path = strip(path)

    path = preventTraversal(path)

    res.sendFile(prepare(path), (err) => {
        if(err){
            if (! res.headersSent) {
                try {
                    res.send(strip(req.query.path) + ' not found')
                } catch {
                    res.end()
                }
            }
        }
    })
})

//Prevent directory traversal attack
function preventTraversal(dir){
    if(dir.includes('../')){
        let res = dir.replace('../', '')
        return preventTraversal(res)
    }

    //In case people want to test locally on windows
    if(dir.includes('..\\')){
        let res = dir.replace('..\\', '')
        return preventTraversal(res)
    }
    return dir
}

//Get absolute path from relative path
function prepare(dir){
    console.log(path.resolve('./public/' + dir))
    return path.resolve('./public/' + dir)
}

//Strip leading characters
function strip(dir){
    const regex = /^[a-z0-9]$/im

    //Remove first character if not alphanumeric
    
    if(!regex.test(dir[0])){
        if(dir.length > 0){
            return strip(dir.slice(1))
        }
        return ''
    }

    return dir
}

app.listen(3000, () => {
    console.log('listening on 0.0.0.0:3000')
})

```


so if we pass path[]=a&path[]=/../../index.js

req.path will become 

```
req.path = ['a', '/../../index.js']
```

and strip() will now work on path[0] = 'a'

path.includes('../') in `preventTraversal` will fail because this array does not include that string.


but when and array is concatenated with string, its converted into string before, so path will become

'./public' + 'a,/../../index.js' = './publica,/../../index.js'

which when resolving will point to index.js where our flag is present.








