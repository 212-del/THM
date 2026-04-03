So my approach is to always open the IP in the browser first, after all the basic setup mentioned in the Instruction.txt file.

So I opened http://ip and saw a corridor.

![Insert_Image1](./Insert_image1.png)

There were a total of 13 doors in the corridor. All were clickable, and after clicking each door, they led me to different endpoints.

All endpoints visually looked similar to the picture below.

![insert_image2](./Insert_image2.png)

The interesting part came when I analysed those paths. They all followed this format:

http://ip/c4ca4238a0b923820dcc509a6f75849b

You can view all of them via the source code.

![insert_image3](./Insert_image3.png)

To decode strings like c4ca4238a0b923820dcc509a6f75849b, first identify what type of encoding it is. You can search online or use my repo https://github.com/212-del/Cybersecurity-calculator to identify it easily.

After identifying the hash type, look up the decoding technique that corresponds to that encoding. Search online for the term "\<The encoding type you discovered\> encoding decoder" or simply go to https://crackstation.net/, paste the hash, and you will get the decoded value.

After decoding all the strings, you can connect the clues between each endpoint. Since the room description mentions that it involves IDOR vulnerabilities and that "This could help you uncover website locations you were not expected to access," guess the endpoint, try different combinations, and you will find the flag.

The flag will be right in front of you.

Kudos to the Room Maker!!

