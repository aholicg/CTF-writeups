#Description

#Solution

This is my first time working with bitlocker, so i started out trying to just mount the disk image:

`$ sudo mount -o loop,rw bitlocker-1.dd mnt/bit1
mount: /home/grey/ctf/mnt/bit1: unknown filesystem type 'BitLocker'.
       dmesg(1) may have more information after failed mount system call.`

After some searching, it seemed i need to do some decryption on the disk image first, as described. 

The tools needed are bitlocker2john, hashcat/john, dislocker.

`$ bitlocker2john -i bitlocker-1.dd > hash`

The hash would contain 4 hashes, we should only use the hash $1$ (Recovery Password fast attack) to proceed.

After editing the hash file:

`john --format=bitlocker --wordlist=~/ctf/rockyou.txt hash
jacqueline (?)` -> here's our password

`$ sudo losetup -Pf bitlocker-1.dd
$ losetup -l | grep bitlocker-1.dd 
/dev/loop0         0      0         0  0 /home/grey/ctf/bitlocker-1.dd   0     512`

`$ mkdir bit1`

`$ $ sudo dislocker -V bitlocker-1.dd -u"jacqueline" -- bit1/`

`$ sudo mount -o loop -t ntfs-3g bit1/dislocker-file mnt/bit1/`

`$ ls mnt/bit1/
'$RECYCLE.BIN'  'System Volume Information'   flag.txt`

`$ cat mnt/bit1/flag.txt 
picoCTF{us3_b3tt3r_p4ssw0rd5_pl5!_3242adb1}`

