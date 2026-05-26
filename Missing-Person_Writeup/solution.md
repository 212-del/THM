So after getting the content of the room that was actully a zip file

[zip](osint.zip)

After getting the zip file we started investingetting on the zip file with the commands below

The command unzip -l osint.zip helps me to spot what was inside the zip without extracting the file.

With the command unzip -l osint.zip gave me the output that 

Archive:  osint.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
    80858  2026-01-06 15:12   MotoGP.jpg
   335765  2026-01-06 14:38   food.jpg
---------                     -------
   416623                     2 files


This confirms us there are 2 files.

With xxd i checked too with matching header of a standard zip to confirm it is a zip file.

The above magic header in the output of xxd confirms me that its a legitimate one zip.

00000000: 504b 0304 1400 0000 0800 528d 265c df9e  PK........R.&\..
00000010: e0a2 cc39 0100 da3b 0100 0a00 1c00 4d6f  ...9...;......Mo
00000020: 746f 4750 2e6a 7067 5554 0900 030b d95c  toGP.jpgUT.....\
00000030: 6906 0c5d 6975 780b 0001 04f5 0100 0004  i..]iux.........
00000040: 1400 0000 94fc 6540 5c3d d42e 800e ee30  ......e@\=.....0
00000050: b8bb bbbb 0eee ee5a dc29 6e85 e2ee eeee  .......Z.)n.....
00000060: ee2e c5dd 5d5b dca1 7881 2297 bedf 77ee  ....][..x."...w.
00000070: 39f7 e74d 3249 6627 5959 5959 eb49 b267  9..M2If'YYYY.I.g
00000080: cffe 58fb d805 e849 785a 5b00 000a 0a00  ..X....IxZ[.....
00000090: 3a00 0000 0b00 0bb3 0640 7ee6 c03e 3f78  :........@~..>?x


Binwalk was also used by my be to check it was a legitimate zip file or not and the output of it was satifying 
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             Zip archive data, at least v2.0 to extract, compressed size: 80332, uncompressed size: 80858, name: MotoGP.jpg
80400         0x13A10         Zip archive data, at least v2.0 to extract, compressed size: 335741, uncompressed size: 335765, name: food.jpg
416365        0x65A6D         End of Zip archive, footer length: 22

We also checked that is there any comments on hidden in the zip file of the osint and i came to know from the ouput of command 

zipinfo -z osint.zip >>Output is 

Archive:  osint.zip
Zip file size: 416387 bytes, number of entries: 2
-rw-r--r--  3.0 unx    80858 bx defN 26-Jan-06 15:12 MotoGP.jpg
-rw-r--r--  3.0 unx   335765 bx defN 26-Jan-06 14:38 food.jpg
2 files, 416623 bytes uncompressed, 416073 bytes compressed:  0.1%
                                                                       
We Understood that there was no any comments.

And this was all about the exploration of the zip file itself cuz we are in cybersecurity we need to follow the zero trust policy we cant trust on anything normal too.

After it we are now proceeding to the unzipping of the file.

First i made a folder therr with named "file"

Then extracted the content with the commadn 

unzip osint.zip -d file

And it successfullly unzipped into the folder file

Now it was time to open both images files and see what was inside it.

Before opening  anything that comes from internet.

We should check once so i checked it with file *.jpg

And i got the output

```bash
food.jpg:   JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, Exif Standard: [TIFF image data, big-endian, direntries=1], baseline, precision 8, 1360x765, components 3
MotoGP.jpg: JPEG image data, Exif standard: [TIFF image data, big-endian, direntries=1], progressive, precision 8, 960x540, components 3
```

Nothing to worry both are jpg files both are images.

Now we could open the file.

here is the look of our images.

## First one is 

![files](food.jpg)

## Second One is

![files](MotoGP.jpg)

Now we are doing some more research on the files both images files

Look at the image file whose name is MotoGP.jpg. Pertamina is written on the Hoarding sure motoGP and pertamina has a correlation so i asked the chatGPT that

Pertamina Motogp race 2025

It gave me that that circuit name on which the race was conducted and it was answer too of the first quesiton that what was the circuit name of the motoGP race.


