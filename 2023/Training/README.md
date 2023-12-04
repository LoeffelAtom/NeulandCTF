# Training 2023
https://neuland-ingolstadt.de/stand

---

## The Mensa (stego)

`YXluYXF7YTA3LTBhMWwtN3UzLXozYTU0LTE1LXAwMHgxYTktMjNwMWMzNS11MzIzfQ==`

#### Solution

> Decode the Base64 string however you like

```console
echo 'YXluYXF7YTA3LTBhMWwtN3UzLXozYTU0LTE1LXAwMHgxYTktMjNwMWMzNS11MzIzfQ==' | base64 -d
```

`aynaq{a07-0a1l-7u3-z3a54-15-p00x1a9-23p1c35-u323}`

>But we are not done yet, the flag needs to be in following format nland{...}
>Let's try ROT13
>Yup! That does the magic.

`nland{n07-0n1y-7h3-m3n54-15-c00k1n9-23c1p35-h323}`

## THI is the Key (crypto)

`:$(:,2g>zf1dly~y+y!&~a5`

#### Solution

> According to the task, it can be assumed, we got a XOR-Cipher with "THI" as the key. Let's try that.
> Im using [Cyberchef](https://gchq.github.io/CyberChef/) for that task:

`nland{3v32y-817-c0un75}`

## Web Path Traversal (web)

> Exploit at http://ctf.informatik.sexy:12903

```js
const fs = require('fs/promises');
const express = require('express');
const app = express();

// in many classic CTF formats there exists a file flag.txt
// which contains the flag you are looking for

app.get('/', async (req, res) => {
  const data = await fs.readFile('./index.html');
  res.end(data);
});
app.get('/blog', async (req, res) => {
  try {
    const name = req.query.name.toLowerCase();
    const data = await fs.readFile('./facts/' + name);
    res.end(data);
  } catch (e) {
    res.end(e.toString());
  }
});

app.listen(3000);
```

#### Solution

> As we see in the code, the query parameter `name` is directly used as input to pull files from the server's local directory `./facts` without any obstructive input sanitization.
> Let's assume the directorys absolute path: `/var/www/html/facts` and let the file name be `flag.txt`. To perform path traversal we can now use `../` to go to the parent directory from our current working directory. With little to no effort we got the flag:
> `{URL}/blog?name=../flag.txt`

`nland{d1d_w1nd15ch_734ch_y0u_7h15?}`

## Buffer Overflow (rev / pwn)

> Exploit at `nc ctf.informatik.sexy 12902`

```c
#include <stdio.h>
#include <string.h>
#include "secrets.h"

// the following are defined in secrets.h:
// #define SECRET_PASSWORD "..."
// #define SECRET_FLAG "..."

int main() {
  char password[16];
  int valid = 0;

  // make sure bytes sent dont get stuck in a buffer
  setvbuf(stdout, NULL, _IONBF, 0);

  printf("Enter password:\n");
  gets(password);

  if(strcmp(password, SECRET_PASSWORD) == 0)
    valid = 1;

  if(valid)
    printf("Welcome back! Here is your flag: %s\n", SECRET_FLAG);
  else
    printf("Wrong!\n");

  return 0;
}
```

#### Solution

> After looking through the code, I noticed that a very unsecure function `gets()` is used.
> [Click here](https://faq.cprogramming.com/cgi-bin/smartfaq.cgi?answer=1049157810&id=1043284351) to find out why exactly this function is considered dangerous to use.
> Let's spam some a's:

```console
nc ctf.informatik.sexy 12902
Enter password:
aaaaaaaaaaaaaaaaaaaaaaaaaaaaa
```

> Nice! We got the flag:

`nflag{0verf10wing_th1s_g3t5}`
