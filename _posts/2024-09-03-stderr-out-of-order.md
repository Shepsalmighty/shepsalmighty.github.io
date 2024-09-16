#Stderr Out of Order

we're given a file new challenge 

=oQIwEDIF5USMBiTJBiUPJlUFBCWBRlTZNFIhBSZydSdvlFIh8mT


file tells us it's ascii text and it looks like base64 so this'll be nice and easy (famous last words)

``` cat filename | base64 -d | file - ```
ascii safe to print

```cat filename | base64 -d``` = invalid input wtf???

ok so what's wrong? some googling about base64 shows us if it's not long enough padding is used in the form of "=", and it goes at the end... but ours is at the beginning 

a few failed attempts for example using awk
```awk -F= '{print $2 "="}' stderr-2024-09-03 | base64 -d```
which also yeilded invalid input

so if the "=" is in the wrong the place, and moving it isn't the answer, maybe it's back to front?

```cat stderr-2024-09-03 | rev | base64 -d | file -``` ascii text - safe to print
```cat stderr-2024-09-03 | rev | base64 -d ```

SUCCESS!!!
