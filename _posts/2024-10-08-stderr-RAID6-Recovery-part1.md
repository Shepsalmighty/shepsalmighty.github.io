# RAID 6

``` file file_name.tar.gz``` shows us 
> gzip compressed data, from Unix, original size modulo 2^32 3563520

unpacking the tar file
``` tar -zxf file_name.tar.gz``` (-z gzunzip, -x extrat, -f file)

so we now have a directory, cd-ing in shows 4 files sda-sdb-sdc-sdd
``` ls -la```
shows 2 "full size" files (sda/sdd) and 2 much smaller ones and running file on them all shows they're data.

let's hexdump them and have a look, we'll use head to limit the output to the terminal

```bash
 hd sdb | head -5
```
```bash
00000000  41 20 52 41 49 44 2d 35  20 77 69 74 68 20 4e 20  |A RAID-5 with N |
00000010  6e 65 20 70 61 72 69 74  79 20 62 6c 6f 63 6b 20  |ne parity block |
00000020  00 04 00 49 07 0d 52 0b  1c 0a 4d 4b 74 01 16 00  |...I..R...MKt...|
00000030  89 f0 b9 29 a5 b2 84 a7  60 9c 97 8c e6 b9 80 60  |...)....`......`|
00000040  69 6e 20 74 68 65 20 73  74 72 69 70 65 2e 0a 0a  |in the stripe...|
```
order of blocks seems to be sdb-sdc-sda-sdd

we saw something like this in the previous challenge so i'm going to assume this is another RAID-5 system with some data to recover

next need to figure out stripe size (seems to be 16 bytes i.e. 1 full line of data before moving to next drive)

NEXT TIME ON DRAGON BALL Z - FIND P AND Q SYNDROMES IN SDA-D 
qiv raid6-read* to read all pages in Downloads dir 


let's read what can be read from the existing data in the order sdb-sdc-sda-sdd:

NOTE: the full, finished python program/project can be found at the following link, but i always hated write ups that don't show you the iteratvie process
that went on to get to the finished project, so i'm including mine
https://github.com/Shepsalmighty/raid_stderr_challenges

```python
def read_raid6():
    paths = ["/home/sheps/PycharmProjects/stderr_challenges/RAID6/part_1/sda",
             "/home/sheps/PycharmProjects/stderr_challenges/RAID6/part_1/sdb",
             "/home/sheps/PycharmProjects/stderr_challenges/RAID6/part_1/sdc",
             "/home/sheps/PycharmProjects/stderr_challenges/RAID6/part_1/sdd"]
    with (open(paths[0], "rb") as sda_file,
          open(paths[1], "rb") as sdb_file,
          open(paths[2], "rb") as sdc_file,
          open(paths[3], "rb") as sdd_file):

        count = 0
        while (len(sda := sda_file.read(16)) > 0 and
               len(sdb := sdb_file.read(16)) > 0 and
               len(sdc := sdc_file.read(16)) > 0 and
               len(sdd := sdd_file.read(16)) > 0):

            if count % 4 == 0:
                print((sdb + sdc).decode("utf-8"), end="")
            elif count % 4 == 1:
                print((sda + sdb).decode("utf-8"), end="")
            elif count % 4 == 2:
                print((sdd + sda).decode("utf-8"), end="")
            else:
                print((sdc + sdd).decode("utf-8"), end="")
            count += 1

read_raid6()
```

this prints all the readable data that's remaining, it's an explanation of RAID6 and how to recover, it also includes a link:
https://mirrors.edge.kernel.org/pub/linux/kernel/people/hpa/raid6.pdf

So now we know it's a failed RAID6 (if it was a RAID5 we were cooked from the start because we had 2 failed drives, so no XOR)

then we need to find which blocks are the P and Q for their respective byte blocks

```python
def read_raid6():
    paths = ["/home/sheps/PycharmProjects/stderr_challenges/RAID6/part_1/sda",
             "/home/sheps/PycharmProjects/stderr_challenges/RAID6/part_1/sdb",
             "/home/sheps/PycharmProjects/stderr_challenges/RAID6/part_1/sdc",
             "/home/sheps/PycharmProjects/stderr_challenges/RAID6/part_1/sdd"]
    with (open(paths[0], "rb") as sda_file,
          open(paths[1], "rb") as sdb_file,
          open(paths[2], "rb") as sdc_file,
          open(paths[3], "rb") as sdd_file):

        count = 0
        while (len(sda := sda_file.read(16)) > 0 and
               len(sdb := sdb_file.read(16)) > 0 and
               len(sdc := sdc_file.read(16)) > 0 and
               len(sdd := sdd_file.read(16)) > 0):



            if count % 4 == 0:
                #get P syndrome
                P = []
                Q = []
                for a, b in zip(sdb, sdc):
                    P.append((int(a) ^ int(b)))
                    a = 2**0 * a
                    b = 2**1 * b
                    #hex 285 == 0x11D but 285 is easier for me to read
                    if a >255:
                        a = a^285
                    if b >255:
                        b = b^285
                    Q.append(int(a)^int(b))

                    #TODO - add Qsyndrome check as above--> Q.append()
                if sda == bytes(P):
                    print(" -- sda is the P syndrome in case 0")
                if sda == bytes(Q):
                    print(" -- sda is the Q syndrome in case 0")

                if sdd == bytes(P):
                    print(" -- sdd is the P syndrome in case 0")
                if sdd == bytes(Q):
                    print(" -- sdd is the Q syndrome in case 0")

                if len(sdb) == 0:
                    # P_Q_syndrome.P_syndrome(sdb, sdc)

                    pass #recover
                if len(sdc) == 0:
                    pass #recover
                print((sdb + sdc).decode("utf-8"), end="")
            elif count % 4 == 1:
                # get P syndrome
                P = []
                Q = []
                for a, b in zip(sda, sdb):
                    P.append((int(a) ^ int(b)))
                    a = 2 ** 0 * a
                    b = 2 ** 1 * b
                    # hex 285 == 0x11D but 285 is easier for me to read
                    if a > 255:
                        a = a ^ 285
                    if b > 255:
                        b = b ^ 285
                    Q.append(int(a) ^ int(b))
                    # TODO - add Qsyndrome check as above--> Q.append()
                if sdc == bytes(P):
                    print(" -- sdc is the P syndrome in case 1")
                if sdc == bytes(Q):
                    print(" -- sdc is the Q syndrome in case 1")
                if sdd == bytes(P):
                    print(" -- sdd is the P syndrome in case 1")
                if sdd == bytes(Q):
                    print(" -- sdd is the Q syndrome in case 1")
                if len(sda) == 0:
                    pass  # recover
                if len(sdb) == 0:
                    pass  # recover
                print((sda + sdb).decode("utf-8"), end="")
            elif count % 4 == 2:
                # get P syndrome
                P = []
                Q = []
                for a, b in zip(sdd, sda):
                    P.append((int(a) ^ int(b)))
                    a = 2 ** 0 * a
                    b = 2 ** 1 * b
                    # hex 285 == 0x11D but 285 is easier for me to read
                    if a > 255:
                        a = a ^ 285
                    if b > 255:
                        b = b ^ 285
                    Q.append(int(a) ^ int(b))
                    # TODO - add Qsyndrome check as above--> Q.append()
                if sdb == bytes(P):
                    print(" -- sdb is the P syndrome in case 2")
                if sdb == bytes(Q):
                    print(" -- sdb is the Q syndrome in case 2")
                if sdc == bytes(P):
                    print(" -- sdc is the P syndrome in case 2")
                if sdc == bytes(Q):
                    print(" -- sdc is the Q syndrome in case 2")
                if len(sdd) == 0:
                    pass  # recover
                if len(sda) == 0:
                    pass  # recover
                print((sdd + sda).decode("utf-8"), end="")
            else:
                # get P syndrome
                P = []
                Q = []
                for a, b in zip(sdc, sdd):
                    P.append((int(a) ^ int(b)))
                    a = 2 ** 0 * a
                    b = 2 ** 1 * b
                    # hex 285 == 0x11D but 285 is easier for me to read
                    if a > 255:
                        a = a ^ 285
                    if b > 255:
                        b = b ^ 285
                    Q.append(int(a) ^ int(b))
                    # TODO - add Qsyndrome check as above--> Q.append()
                if sda == bytes(P):
                    print(" -- sda is the P syndrome in case 3")
                if sda == bytes(Q):
                    print(" -- sda is the Q syndrome in case 3")
                if sdb == bytes(P):
                    print(" -- sdb is the P syndrome in case 3")
                if sdb == bytes(Q):
                    print(" -- sdb is the Q syndrome in case 3")
                if len(sdc) == 0:
                    pass  # recover
                if len(sdd) == 0:
                    pass  # recover
                print((sdc + sdd).decode("utf-8"), end="")
            count += 1

read_raid6()
```


time to recover our data, we'll start with the case where we have only 1 missing data block, and recover using our P syndrome

in this example the P syndrome is known (from the above work) so we check that the P data block exists, if it does, we check which of the relevant 2 blocks is missing data and recover from the P block and existing data block (this got moved to a helper function later, just including the logic here for ease).

Next if we're missing both data blocks, things get a bit trickier, we need to use Galois feild maths to recover our D2 data block, and then from there we can recover D1 using D2 ^ P_block (all code in github repo including functions called here but not shown)

```python
def recover_all(block1, block2, P_block, Q_block):
    #P_recovery
    if len(block1) > 0 and len(block2) > 0:
        return block1, block2
    if len(block1) == 0 and len(block2) > 0 and len(P_block) > 0:
        recovered_data = []
        for a, b in zip(P_block, block2):
            recovered_data.append(int(a) ^ int(b))
        block1 = bytes(recovered_data)
        return block1, block2
    if len(block1) > 0 and len(block2) == 0 and len(P_block) > 0:
        recovered_data = []
        for a, b in zip(P_block, block1):
            recovered_data.append(int(a) ^ int(b))
        block2 = bytes(recovered_data)
        return block1, block2
    #Q_recovery
    if len(block1) == 0 and len(P_block) == 0:
        recovered_data = []
        for a, b in zip(Q_block, block2):
            recovered_data.append(int(a) ^ (2* int(b)))
        return bytes(recovered_data), block2
        # return(Q_block ^ (2* block2))
    if len(block2) == 0 and len(P_block) == 0:
        recovered_data = []
        for a, b in zip(Q_block, block1):
            recovered_data.append((int(a) ^ int(b)) //2)
        return block1, bytes(recovered_data)
    if len(block1) == 0 and len(block2) == 0:
        recovered_block2 = bytearray()
        recovered_block1 = bytearray()
        for a, b in zip(P_block, Q_block):
            for n in range(0, 256):
                n_xor_2n = Q_syndrome(bytes((n,)), bytes((n,)))
                if n_xor_2n[0] == a ^ b:
                    recovered_block2.append(n)
                    recovered_block1.append(a ^ n)
                    break
            else:
                print("PANIC!!!!")
        return recovered_block1, recovered_block2
```
			
### SUCCESS!!!

we finished part 1 of the challenge, we recovered our lost data, and it turns out there's another part, so rather than printing to stdout, we'll append to a new file stderr_raid6_recovered

reading the file, there are the instructions, a long (what seems to be) base64 encoded second stage of the puzzle and some trash (which we can look at later) at the end. We'll filter the instructions and trash out using awk and put the base64 into another file 

```bash
awk 'NR>=105 && NR<=46092 { print }' < stderr_raid6_recovered > stderr_raid6_base64
```
then

```bash
cat stderr_raid6_base64 | base64 -d | file -

/dev/stdin: gzip compressed data, from Unix
```
next step 
```bash
cat stderr_raid6_base64 | base64 -d | gunzip | file -
/dev/stdin: POSIX tar archive (GNU)
```
so let's gunzip it:
```bash
cat stderr_raid6_base64 | base64 -d | gunzip | tar -vtf -
```

this gives us a rather worrying:
```bash
-rw-r--r-- root/root      1615 2024-04-20 06:32 part_2/README
-rw-r--r-- root/root       512 2024-04-20 06:37 part_2/sectors/sda.sector000
-rw-r--r-- root/root       512 2024-04-20 06:37 part_2/sectors/sdc.sector000
-rw-r--r-- root/root       512 2024-04-20 06:37 part_2/sectors/sdd.sector000
-rw-r--r-- root/root       512 2024-04-20 06:37 part_2/sectors/sde.sector000
-rw-r--r-- root/root       512 2024-04-20 06:37 part_2/sectors/sdg.sector000
-rw-r--r-- root/root       512 2024-04-20 06:37 part_2/sectors/sdh.sector000
-rw-r--r-- root/root       512 2024-04-20 06:37 part_2/sectors/sda.sector001
```

Of course there's a part 2... Thanks stderr...

Coming soon
