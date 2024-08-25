---
layout: post
title: Stderr Challenge "2023-07-12-p" (Write Up)
---

The goal of these challenges is to teach the basics of **Linux**, the given concepts, puzzle solving and learn about command line functionality

we are provided a file (linked)


checking it's the correct file `md5sum file_name`
yields an output of : **99a4be7a21be1d07ebcbf9c5dff68a0f**


Any tools used for this challenge are linked at the bottom so as not to spoil the challenge if you're following along
or attempting it for yourself.



### Identify the file
we don't know what the file is or contains, so it shouldn't be printed to the terminal *ideally*

calling `file` on the filename in our case `file stderr2` tells us the file contains:
> ASCII text

it there **should** be safe to print to the terminal and returns a string that looks to be base64 encoded (mix of upper/lowercase letters and beings or ends with =)
to decode we cat the file and pipe it into `base64 -d` and pipe that output to `file -` or stdin
```bash
cat stderr2 | base64 -d | file -
```
which yields:
> /dev/stdin: XZ compressed data, checksum CRC64


ok, so we know it's XZ compressed data so let's decompress with xz-utils and pipe it to stdin to see if it's safe:
```bash
cat stderr2 | base64 -d |xz -cd | file -
```

>/dev/stdin: CSV ASCII text

this shows the decompresed file is CSV ASCII text and again should be safe to print to terminal so we can remove `file -`


```bash
cat stderr2 | base64 -d |xz -cd 
```

>user1:425Nlj6WTQSRc:1001:1001:User 1,,,:/home/user1:/bin/bash <br/>
>user2:69X7QtYbzmBjs:1002:1002:User 2,,,:/home/user2:/bin/bash

ok so we have some user information, the only thing that looks out of place or of interest is going to be those passwords,
so lets use John to crack the hashed passwords (sometimes these commands don't work grouped (apparently) so you may need
to crack them one at time)

```bash
john file/with/user1_&_user2
```
>Hello            (user1)     
>World            (user2)  



### SUCCESS!!! Thanks John.




### Tools:
[John the Ripper](https://github.com/openwall/john) for password cracking
