## Owner:

> CaptainFreak

#### Solved?

 - Almost

#### Tried:

 RTFM, but not enough.

#### Solutions:

Code for Challenge:

```javascript
const fastify = require('fastify');
const nunjucks = require('nunjucks');
const crypto = require('crypto');


const converters = {};

const flagConverter = (input, callback) => {
  const flag = '*** CENSORED ***';
  callback(null, flag);
};

const base64Converter = (input, callback) => {
  try {
    const result = Buffer.from(input).toString('base64');
    callback(null, result)
  } catch (error) {
    callback(error);
  }
};

const scryptConverter = (input, callback) => {
  crypto.scrypt(input, 'I like sugar', 64, (error, key) => {
    if (error) {
      callback(error);
    } else {
      callback(null, key.toString('hex'));
    }
  });
};


const app = fastify();
app.register(require('point-of-view'), {engine: {nunjucks}});
app.register(require('fastify-formbody'));
app.register(require('fastify-cookie'));
app.register(require('fastify-session'), {secret: Math.random().toString(2), cookie: {secure: false}});

app.get('/', async (request, reply) => {
  reply.view('index.html', {sessionId: request.session.sessionId});
});

app.post('/', async (request, reply) => {
  if (request.body.converter.match(/[FLAG]/)) {
    throw new Error("Don't be evil :)");
  }

  if (request.body.input.length < 10) {
    throw new Error('Too short :(');
  }

  if (request.body.input.length > 1000) {
    throw new Error('Too long :(');
  }

  converters['base64'] = base64Converter;
  converters['scrypt'] = scryptConverter;
  converters[`FLAG_${request.session.sessionId}`] = flagConverter;

  const result = await new Promise((resolve, reject) => {
    converters[request.body.converter](request.body.input, (error, result) => {
      if (error) {
        reject(error);
      } else {
        resolve(result);
      }
    });
  });

  reply.view('index.html', {
    input: request.body.input,
    result,
    sessionId: request.session.sessionId,
  });
});

app.setErrorHandler((error, request, reply) => {
  reply.view('index.html', {error, sessionId: request.session.sessionId});
});

app.listen(59101, '0.0.0.0');

```

1. We had to abuse the fact that we can control what function of converter object is called. 
`converters[request.body.converter](input, callback)`

2. Objects have multiple intrinsic JS function, one of them can be used to spit out the flag in errors :)

`https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/__defineSetter__`

The defineSetter function of any object is way to do extra things in callback when a values are assigned to its keys.


```
var o = {};
o.__defineSetter__('key', function(value) { //we access value in callback });
```


So following code

```javascript
converters[request.body.converter](request.body.input, (error, result) => {
      if (error) {
        reject(error);
      } else {
        resolve(result);
      }
    });

```

with `request.body.converter = __defineSetter__` and `request.body.input = FLAG_sessionId`

will assign following callback for whenever converter object gets key `FLAG_sessionId`
assigned a value.

```
(error, result) => {
      if (error) {
        reject(error);
      } else {
        resolve(result);
      }
    });

```


In the first request, We just assign the callback and the Promise does not gets resolved so we are stuck at `const result = await new Promise((resolve, reject)`

In the second consecutive request, The code 
```converters[`FLAG_${request.session.sessionId}`] = flagConverter;```

will assign the value `flagConverter` function to our unique key for which we have set a callback, this `flagConverter` function will get passed down to callback as error and Our Promise will get rejected and the result gets assign with the whole function, and this result gets spit out in response to our first request which was waiting for this to happen.

Peace.






