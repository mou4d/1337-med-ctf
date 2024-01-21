# 1337-med-ctf
## web

### jwita_lmongol

this challenge is about the concept of **cookies injection** specially the JWT (JSON Web Token) attak,
where the JWT cookie holds information about the current logged in user and it's cryptographically signed.
the main thing about this challeg is to change your user and appear to be logged in as a admin in order to get the flag from a specific  web page /flag

extracting the value of the cookie reveals the incrypted JWT token wich consists of 3 parts:
a header, a payload, and a signature
```eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoxMSwiZXhwIjoxNzA1ODczNjA0fQ.0cnW63IUFSc0fF94ugQjj0NY4vcZkDwKHMjvyfAeIgU```
the header and payload parts of a JWT are just base64url-encoded JSON objects.
the header contains metadata about the token itself, while the payload contains the actual "claims" about the user. the signature is used to verify that the sender of the JWT is who it says it is and to ensure that the message (payload) wasn't changed along the way.
The server that issues the token typically generates the signature by hashing the header and payload. In some cases, they also encrypt the resulting hash. Either way, this process involves a secret signing key. 

the important part that we will need to modify is the pyload and by decrypting it it reveals : 
```json
{
	"user_id":15,
	"exp":1705774845
}
```
so assuming that the server checks the current logged in user looking up the value of **user_id** in the **JWT payload** in the server's database, sounds good, as we know almost all developers use auto incremented value for the user id field in the users table, so we will make a guess that the admin is the first user created and it's user_id is 1 so we will modify and encrypt the payload.

now our payload is ready but we still need to prepare the full token to do that we need to determin the secret key to sign our token before injecting it in the cookie that is done using the one and only **Brute-forcing**

we will use hashcat for this, you just give it the full token from the target server and a wordlist of well-known secrets.

```
hashcat -a 0 -m 16500 <jwt> <wordlist>
```

and from here everything needs to be good we got the secret signed our token copy past it to the cookies or whatever method you prefer for the cookies injection and it's done

More details about **JWT** and commun **JWT attacks** : https://portswigger.net/web-security/jwt

### bahlawan_l7afalat 

The challenge involved making a [POST request](https://en.wikipedia.org/wiki/POST_(HTTP)) with an exposed username on a website that reveals its backend code and a hidden password.
The objective was to **bypass the password check** and receive the **flag** in the server response.

The key to solving the challenge lies in exploiting [PHP type juggling](https://secops.group/php-type-juggling-simplified/). In PHP, the '==' operator allows for loose comparison, where different data types, like strings and integers, can be compared. For example, 'test' == 0 evaluates to true because PHP tries to convert the string 'test' to an integer, treating it as 0.

Here's the important part of the backend code:

```php
if ($username == "lmongol") {
    echo "blan\n";
    if ($real_password == $password) {
        echo "blan hak ha lflag\n";
        $response = ['success' => true, 'message' => $flag];
    }
}
```
To exploit this, a POST request with the following payload can be made:

```curl -d '{"username":"lmongol","password":0}' -X POST http://167.99.193.9:6967```

Alternatively, by using boolean conversion:

```curl -d '{"username":"lmongol","password":true}' -X POST http://167.99.193.9:6967```

This leverages PHP's conversion behavior to bypass the password check and retrieve the flag.

In essence, the crafted payload exploits PHP's loose comparison, where the string 'lmongol' matches the username condition, and the loose comparison of the password ('0' or 'true') allows access to the flag.

To summarize, the challenge involves understanding PHP type juggling and exploiting it to manipulate the comparison operators for successful authentication.
