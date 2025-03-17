#Description

Here's a file that was recovered from a 32-bits system that organized the bytes a weird way. 
We're not even sure what type of file it is. 
Download it [here](https://artifacts.picoctf.net/c_titan/112/challengefile) and see what you can get out of it 

#Solution

The title is "endianness" to we should inspect the hex. That prompted me to think about magic number, so I tried inspecting the file's type
`$ file challengefile`

challengefile: data
`$ exiftool challengefile` 

ExifTool Version Number         : 13.03
File Name                       : challengefile
Directory                       : .
File Size                       : 3.4 kB
File Modification Date/Time     : 2025:03:17 11:14:54+07:00
File Access Date/Time           : 2025:03:17 11:15:05+07:00
File Inode Change Date/Time     : 2025:03:17 11:14:56+07:00
File Permissions                : -rw-r--r--
Warning                         : Processing JPEG-like data after unknown 1-byte header

-> Likely a .jpeg file. Next, inspect the hexdump

`$ xxd challengefile | head`

00000000: e0ff d8ff 464a 1000 0100 4649 0100 0001  ....FJ....FI....

.jpeg magic number is 
`FF D8 FF E0 00 10 4A 46`

-> each byte in the hex is in (?)-endian

Fortunately, xxd can fix this with the command
`$ xxd -e challengefile | xxd -r > fixed`

`$ file fixed`

fixed: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 300x150, components 3
![bilde](https://github.com/user-attachments/assets/57c4d477-3641-44fc-bcf3-f1e1168c85d7)

there goes our flag: 
