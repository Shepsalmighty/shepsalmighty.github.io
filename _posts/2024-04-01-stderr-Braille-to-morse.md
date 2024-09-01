#Stderr Challenge 2024-04-01
   
   
md5sum to check it's the correct challenge: e39805000ce638be1fee9d2d7e7d50d6


```bash
file stderr-2024-04-01 | base64 -d | file -
```
told us it was gzip compressed data so to decompress we'll use gzip

```bash
cat stderr-2024-04-01 | base64 -d | gunzip | file - 
```
> /dev/stdin: Unicode text, UTF-8 text, with very long lines (624) 

so we can cat this and see what we're working with

```bash
cat stderr-2024-04-01
```

⠙⠕⠞⠀⠙⠁⠎⠓⠀⠀⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠙⠕⠞⠀⠀⠀⠙⠕⠞⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠀⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠀⠀⠀
⠙⠕⠞⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠀⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠀⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠀⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠀⠀⠀⠀
⠙⠕⠞⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠀⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠀⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠀⠀⠙⠕⠞⠀⠀⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠀⠀⠀⠀
⠙⠕⠞⠀⠙⠕⠞⠀⠀⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠀⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠙⠕⠞⠀⠀⠀⠙⠕⠞⠀⠙⠕⠞⠀⠀⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠀⠀⠀⠀
⠙⠕⠞⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠙⠕⠞⠀⠀⠀⠙⠕⠞⠀⠙⠕⠞⠀⠀⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠀⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠙⠕⠞⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠀⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠀⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠀⠀⠙⠁⠎⠓⠀⠀⠀
⠙⠕⠞⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠀⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠀⠀⠙⠁⠎⠓⠀⠀⠀⠀⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠀⠀
⠙⠕⠞⠀⠙⠕⠞⠀⠙⠕⠞⠀⠀⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠀⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠀⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠀⠀⠀⠀
⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠀⠀⠙⠕⠞⠀⠙⠕⠞⠀⠙⠕⠞⠀⠀⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠀⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠀⠀
⠙⠕⠞⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠀⠀⠀⠀⠙⠕⠞⠀⠙⠁⠎⠓⠀⠙⠕⠞⠀⠙⠕⠞⠀⠀⠀⠙⠕⠞⠀⠙⠕⠞⠀⠀⠀⠙⠁⠎⠓⠀⠙⠁⠎⠓⠀⠀⠀⠙⠕⠞⠀⠙⠁⠎⠓


now i don't know about you but that was pretty scary for me to see for a minute there i was quite worried but, after minute to calm down and think it looks suspiciously like Braille... It's Braille... Of course it's Braille, it's stderr messing with us. 

So I'll be honest at this point i found a Braille translator online, copy and pasted the text into it and got this:
> dot dash   dot dash dot dot   dot dot dash dot   dot dash     dot dash dash dot   dot dash   dot dash dash dot   dot dash     dot dash dot   dash dash dash   dash dash   '\x000e'h dash   dash dot dot dash   dash   dot dash dot   dash dash dash   dash     dash dash dash   dot dot dot   dash dot dash dot   dot dash   dot dash dot     dash dash da'\xffa0'\x00a0h   dot dot dot   dash dot dash dot   dot dash   dot dash dot     dot dash dot dot   dot dot   dash dash   dot dash


Now i'm no code breaker but i know morse code when i see it, so I went looking for a morse code translator but as you might have already found at least some of them don't work, so i thought it might be fun to try it with a little python script. 

As it turns out white spaces are not all created equal. An ASCII space is not the same as a U+2800 BRAILLE PATTERN BLANK - which caused issues for the translator page i used and my script. So I used the ".replace" python method to remove all the white space and replace it with ascii text white space (an empty string " ")

[more info on Braille here](https://www.fileformat.info/info/unicode/char/2800/index.htm)

```python
from MorseCodePy import decode

def morse(string):

    new_string = string.replace("dot ",".").replace("dash ","_")
    return new_string


print(morse("""dot dash  dot dash dot dot   dot dot dash dot   dot dash     dot dash dash dot   dot dash   dot dash dash dot   dot dash     dot dash dot   dash dash dash   dash dash   dot   dash dash dash     dot dot   dash dot   dash dot dot   dot dot   dot dash     dot dash dot dot   dot dot   dash dash   dot dash          dot dot dash dot   dash dash dash   dash dot dot dash   dash   dot dash dot   dash dash dash   dash     dash dash dash   dot dot dot   dash dot dash dot   dot dash   dot dash dot     dash dash dash   dot dot dot   dash dot dash dot   dot dash   dot dash dot     dot dash dot dot   dot dot   dash dash   dot dash """))

string = decode('''._ ._..  .._.  ._    .__.  ._  .__.  ._    ._.  ___  __  .  ___    ..  _.  _..  ..  ._    ._..  ..  __  ._         .._.  ___  _.._  _  ._.  ___  _    ___  ...  _._.  ._  ._.    ___  ...  _._.  ._  ._.    ._..  ..  __  ._''', language='english', dot='·')
print(string)
```

So now, we have our Braille, we can cheat a little and use a translator site and we get"

>ALFAPAPAROMEOINDIALIMAFOXTROTOSCAROSCARLIMA


a morse code translator gave us a string containing NATO phonetic alphabet (Alpha, Bravo, Charlie etc)

"April Fools!"
