Game crash on level 5

15:31

```bash
3. What is the MD5 hash of the malware file?
```
I can't use the "find a packet" option in Wireshark to grep for "password/pass/"

$ tshark -r capture.pcapng -Y http --hexdump all | grep -A 2 "pass"
0290  62 79 70 61 73 73 65 73 2b 74 68 65 2b 70 72 6f   bypasses+the+pro
02a0  62 6c 65 6d 2b 74 65 6d 70 6f 72 61 72 69 6c 79   blem+temporarily
02b0  2e 2b 41 6c 73 6f 25 32 43 2b 6d 61 6b 65 2b 73   .+Also%2C+make+s
--
0580  65 65 2b 69 66 2b 69 74 2b 62 79 70 61 73 73 65   ee+if+it+bypasse
0590  73 2b 74 68 65 2b 70 72 6f 62 6c 65 6d 2b 74 65   s+the+problem+te
05a0  6d 70 6f 72 61 72 69 6c 79 2e 2b 41 6c 73 6f 25   mporarily.+Also%
--
28c0  20 41 72 63 68 69 76 65 20 70 61 73 73 77 6f 72    Archive passwor
28d0  64 20 69 73 20 65 6c 64 6f 72 69 61 69 73 6d 79   d is eldoriaismy
28e0  6c 69 66 65 3c 62 72 3e 0a 3c 62 72 3e 0a 4c 6f   life<br>.<br>.Lo

$ sha256sum Eldoria_Balance_Issue_Report.pdf.exe 
5236676355a82362102e0d2ae5274a89cd9198e23bcf0a83609aea15fa111604  Eldoria_Balance_Issue_Report.pdf.exe
