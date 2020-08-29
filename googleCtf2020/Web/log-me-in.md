## Owner:

> CaptainFreak

#### Solved?

 - Yes

#### Solutions:

Another challenge bases on extended true for BodyParser, which will let us pass Objects and Array inro strings,

well this is advantageous to us cause npm package "mysql" will do speacial things if we pass Objects, arrays etc. in place of Strings
https://www.npmjs.com/package/mysql#escaping-query-values

Task is to bypass password check and login to michelle users account and get /flag

We can do that by

`
username=michelle&password[username]=michelle
`

which makes the query

```Select * from users where username = ? and password = ?', ['michelle', {username: 'michelle'}],```

which mysql package turns into

```Select * from users where username = michelle and password = `username` = michelle```

which evaluates to true and we bypass password check and login to michelles account.


https://github.com/csivitu/CTF-Write-ups/tree/master/Google%20CTF/Web/Log-Me-In