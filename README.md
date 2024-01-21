# 1337-med-ctf
## web
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
