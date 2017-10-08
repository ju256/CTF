[writeup by @ju256]

*CTF:* Kaspersky Industrial CTF Quals 2017

*Team:* Netcat.us

# Security home cameras

## Crypto - 300 points

We're given an encrypted png file. The only thing the description tells us is that the image is from a smart home surveillance camera.

Therefore, the first thing we did, due to the little information, was to open the encrypted image in hexeditor and compare it to a normal png file.

![img1](https://user-images.githubusercontent.com/32434661/31316346-b00f81f8-ac2b-11e7-9776-67f03969bab5.png)

It is interesting to note that for example *0D 0A 1A 1A* (these second four bytes are always the same in png images) is turned in *F2 F5 E5 F5* and *00* is turned in *FF*. 

Having that said, we thought that the image was probably encrypted with a substitution cipher or single-byte xor. 

The next step was just messing around with the encrypted image to find the way it is encrypted. For example, *0D* xored with *F2* or *0A* xored with *F5* is 255. So we just have to xor each byte from the encrypted image with 255 to get a valid png image.

```py
f=open("secret_encrypted.png","rb").read().encode('hex')

parts = [f[i:i+2] for i in range(0, len(f), 2)]

out=""
for part in parts:
  nbyte=hex(int(part,16)^255)
  nbyte=nbyte.replace("0x","")
  if len(nbyte)==1:
    nbyte="0"+nbyte
  out+=nbyte
  
open("decrypted.png","wb").write(out.decode('hex'))
```
<img src="decrypted.png"/>

Bingo! A simple but fun task in the end.
```bash
FLAG: KLCTF{it_was_just_atbash_encryption}
``` 
