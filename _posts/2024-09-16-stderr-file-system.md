#STDERR File System

https://github.com/TrshPuppy/stderr-challenges


we downloaded the file from the above link, it's a challenge made for another streamer Trshpuppy, designed to teach some of the basics and utils of Linux. 

to check we have the correct file we run ```md5sum trshpuppy``` and get 
>230dac4d490b353277cd5c006b9d6f79  trshpuppy

run ```file``` on the download to see what kind of thing we downloaded
>trshpuppy: Linux rev 1.0 ext2 filesystem data, UUID=c114a57f-38e5-4743-88ca-3c1183185349, volume name "trshpuppy" >(large files)"


tells us it's a filesystem data and therefore needs to be mounted before we can work with it. Do not cat the file this might crash your system.
to do this we'll need the ```mount``` command more info on mount can be found using either ```man mount``` or ```mount --help```
NOTE - by design this challenge contains a filesystem with errors, the "mount" command in some systems tries to fix these errors but they're needed for us to continue down the rabbit hole. We MUST mount in READ ONLY "-r" or "-o ro" or these clues may be removed
(i never got the errors thrown despite all this but it's best practice for most clues)

"man mount" tells us the standard approach for mounting a file system is "mount file directory" but this has to be done as root ("sudo")


```sudo mount -r trshpuppy(the_filename) mountpoint(the_directory)``` - if you're mounting to a mountpoint not in the current dir the full dir path will be required, to make things easier for me i mounted the filesystem to a dir in the downloads file (i'm lazy)

```lsblk``` lists all block devices (this shows all of our mounting points) to check we successfully mounted our filesystem we can lsblk and pipe in grep to filter out just our mountpoint name (in my case mountpoint)

```lsblk | grep mountpoint```

once mounted the filesystem, we can ```cd``` to that dir and a quick ```ls``` then shows we have 1 file "secret.aes256" and a dir "lost+found".

```file secret.aes256``` tells us we have 
>openssl enc'd data with salted password

Ok so what is lost+found, a quick google says that it's a construct used by fsck when there is damage to a file system (this should also throw an "output error" if you're lucky enough to get them)

So, lets fix lost+found, using fsck.
```man fsck``` tells us all the options. Given that we know the file system was of type ext2 using the -t flag seems like a good idea here 
```fsck -t ext2 lost+found```

###defeat... 
it doesn't work... fsck won't work on a mounted filesystem... So let's unmount using ```umount``` and run fsck on the filesystem. e2fsck is designed for ext type filesystem so we'll use that 
```sudo umount /absolute/path/to/mountpoint```
```e2fsck -vfy trshpuppy``` (flags vyf = -v verbose -f force -y yes to all)

```md5sum trshpuppy``` - checking if the file changed - we get a different readout from at the start. Success!
```sudo mount -r trshpuppy mountpoint```

we now have a recovered file "#13" and we can check the files using "sudo tree -R" 
```file #13``` shows an ascii text - presumably the password for our encrypted secret.aes256 file
>jg2VxRTX

```openssl aes-256-cbc -pbkdf2 -d -pass file:Downloads/mountpoint/lost+found/#13 -in``` ```Downloads/mountpoint/secret.aes256 -out Downloads/decrypted.file -v ```

this gives us 
>decrypted.file

we need to find out what we've got, trusty old file to the rescue 
```file decrypted.file```

>decrypted.file: Netpbm image data, size = 48 x 16, rawbits, bitmap

file tells us it's a pbm image, just to be safe let's not open a pmb file with the suffix ".file" as this may cause a crash (don't ask how i know) so let's change it's name 

```mv decrypted.file decrypted.pbm```

```open decrypted.pbm``` 

#success!!!





