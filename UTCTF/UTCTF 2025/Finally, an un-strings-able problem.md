---
title: 'Finally, an un-strings-able problem'

---

#  Finally, an un-strings-able problem - forensics

## Description
> I inherited this really cursed disk image recently. All the files seem to be corrupted and I can't even read some of them. What the heck is going on?
https://cdn.utctf.live/disk.img

## Hướng làm ban đầu
Đầu tiên, xem thử đây là disk image của file system nào:
```bash
$ file disk.img 
disk.img: Linux rev 1.0 ext4 filesystem data, UUID=981eb2d5-0400-4c7d-986e-e9c3860666d3 (needs journal recovery) (extents) (64bit) (large files) (huge files)
```
Ext4 file system của linux, thử mount luôn:
```bash
$ sudo mount -o loop -t ext4 disk.img mnt/for3/ && cd mnt/for3/
[grey@void for3]$ ls
1Pe4S76zWpxA8mgI.txt  OOVH70vCevC3FSZq.txt  fGMkb1gANtjLb5Qz.txt
3zkhN7A0Vqe0HgNC.txt  PtRcxoHyWhhS6z9q.txt  kY4FOlfPt3qGR17K.txt
5UhgLeVLJWuEnc4W.txt  R6qgmnljCORHERFH.txt  kcVtPjZM85b4B3V6.txt
6ReSGoJsRnFqhg7r.txt  S5dyoQrp6a8grHOD.txt  lost+found
B7HIyq5CKwGavwaW.txt  Uljik5BaQPOeMjfc.txt  lysJisb6wMlPG9WX.txt
GP4decBqHC6UL66s.txt  Vp7XQiTt4ad9IDfB.txt  pU2aTHrPpCjthwwi.txt
GeDYq3hoIx7oijhO.txt  W5xw54rLYvyj7qRM.txt  t7WeFKhvlS3e1Yet.txt
HN5vsOJkU4004pEl.txt  Z0VC73hFMVt2AcO9.txt  u70m5b8l2T3vnZHZ.txt
HsxVbSgKw3d7tvFi.txt  bG0d9OBnfwVP2NS8.txt  uAkL5PqfK2I7K4PE.txt
KEY19YZmg8L92D1H.txt  cDnYAOBE4mvBnh0C.txt  x9f1QlloTkYd5sfU.txt
LuVzAMVXkpJYAxRM.txt  eiUFiXPDk6oee8sL.txt  y7Dsjz7CmkvpTA1H.txt
```
Thấy rằng tên của những file trên giống như được encode bởi base64 hoặc base62. Còn nội dung bên trong các file thì sao? 
```bash
$ head 1Pe4S76zWpxA8mgI.txt 
XGOCyaINZ3GEm2m1oDIJliWfZS6BaG4xzJEN6biTXO3WRHD93HlElIEm6ClOKSOrUJHIxgSFzZxIz6HjYy3WfvAGlBB9dAgE5RhlQedyicbncGgPOoPJ90HoG95gRviWbe3Ta2Ywv6sPokYqnLYQjO2Nd0bvf32SCgw0VKoPaGMMk8ylZxZlDhxmviDQxmnUHO0kShvTYXvHrgwImI6aCjOTvPecgAq2JqmL4OcVQ4ZFWc0ujdBkX7g3QuRT5YrD4a86B1YwZORhzawWvUCoN3iicvkTRvvmRPkFQEeBs28T2tfgtkOKghaGM68SXetizU3yQLc16nQdHvy7w3rY9LyVIBBDx66IkUTQvbuknuzAsVnd1RuvBEZxLyHxxh6d4uoYiV5nFw5rVlC9PKOApaaenWxRa52zzWEEfs3lbhp9ZFDyaAjHp4fqXaKP4PDC1ZHywRFXHdULQjKCzNNe2pN63tpOLnwsesRYhn1gvQvg0pfpdTQbl1FJ1VD3CecO2ihYSeBiHVBZ0BiR621sTN5DIyR3fOZcLO4cqw2on3LxbpaKZJBF0QJJg1QTSDgi2ReFjKhq6HfAN2n1uvitFjt7R2Ud0g1I1G4hStiRPbwoqimaEIwaHyzlFAQjLyjoeK6tU07R1OqVtHiIm6GwgYHxcwFiNbwjlzSJoq6Sc35U4O1iBzVJVoap1xK3vDNuEPBcNhegDwnBV0QJrpBbRjFM5gLTiha5fbcYOReRPbgo0CFforrABHHP5g5eipt3qBzZ04cZL5eHgDaMe4RaxLf2BS956auJuOdkEZU0iiHu97lzJ6VSuwhyFk3JwXA01TRhHuVsYYUrskpjf7iyL3qr11ScdBV79vqzsASfVYLmLYTQWyZKk4oKBlKi316MnX6SsAwdlfsRgEXDnd7DyEU2NRMvstnUt35DpZmPQ67326xQrLZjN4zP8PYuN18HKDYkJLfnngTYD2tJZUcSqF9GWaEvuaCpkgLWj8n3aG8lxwzmYrlPYKak5wFiccBP
```
Rõ ràng là base62. Các file khác cũng có nội dung tương tự. Tuy nhiên, permission để đọc các file này rất random (1 file có tên khá sus là ``KEY19YZmg8L92D1H.txt`` thì không cần root privilege để đọc).

Sau khi thử decode base62 toàn bộ các file trên, kết luận đạt được là toàn gibberish:(

Để ý khi mount có spawn directory ``lost+found``, đây là directory dùng để lưu trữ các orphan hoặc corrupted file. 
Directory này cũng cần permission để đọc. 
```bash
$ sudo ls -lah lost+found/
Password:
total 20K
drwx------ 2 root root  16K Mar 12 09:08 .
drwxr-xr-x 3 root root 4.0K Mar 12 09:08 ..
```
Không có gì cả. 

#

Theo gợi ý của anh @shynt_ thì nếu ta extract disk image này thay vì mount thì sẽ spawn ra 1 directory và file khá lạ:
```bash
$ 7z x disk.img 
...
$ ls
 1Pe4S76zWpxA8mgI.txt   PtRcxoHyWhhS6z9q.txt   fGMkb1gANtjLb5Qz.txt
 3zkhN7A0Vqe0HgNC.txt   R6qgmnljCORHERFH.txt   kY4FOlfPt3qGR17K.txt
 5UhgLeVLJWuEnc4W.txt   S5dyoQrp6a8grHOD.txt   kcVtPjZM85b4B3V6.txt
 6ReSGoJsRnFqhg7r.txt   Uljik5BaQPOeMjfc.txt   lost+found
 B7HIyq5CKwGavwaW.txt   Vp7XQiTt4ad9IDfB.txt   lysJisb6wMlPG9WX.txt
 GP4decBqHC6UL66s.txt   W5xw54rLYvyj7qRM.txt   pU2aTHrPpCjthwwi.txt
 GeDYq3hoIx7oijhO.txt   Z0VC73hFMVt2AcO9.txt   t7WeFKhvlS3e1Yet.txt
 HN5vsOJkU4004pEl.txt   '[SYS]'                 u70m5b8l2T3vnZHZ.txt
 HsxVbSgKw3d7tvFi.txt   bG0d9OBnfwVP2NS8.txt   uAkL5PqfK2I7K4PE.txt
 KEY19YZmg8L92D1H.txt   cDnYAOBE4mvBnh0C.txt   x9f1QlloTkYd5sfU.txt
 LuVzAMVXkpJYAxRM.txt   disk.img               y7Dsjz7CmkvpTA1H.txt
 OOVH70vCevC3FSZq.txt   eiUFiXPDk6oee8sL.txt
```
```bash
$ ls -a \[SYS\]/
.  ..  Journal
```
Inspect thử file này:
```bash
$ file Journal && ls -lh Journal 
Journal: data
-rw------- 1 grey grey 32M Mar 12 09:08 Journal
```
oops owner là bản thân -> file này spawn ra do 7z, không liên quan tới disk image:(

## Hướng giải
Thật ra, permission của các file trong disk image không ***random*** chút nào, đặc biệt khi kết hợp với modification time của các file:
```bash
$ ls -lahtr
total 152K
--wxr-x-w- 1 root root 1.1K Mar 12 08:23 PtRcxoHyWhhS6z9q.txt
-rwx-w---x 1 root root 1.1K Mar 12 08:24 y7Dsjz7CmkvpTA1H.txt
-r--rw--wx 1 root root 1.1K Mar 12 08:25 R6qgmnljCORHERFH.txt
--wx---rw- 1 root root 1.1K Mar 12 08:26 1Pe4S76zWpxA8mgI.txt
----r-xr-- 1 root root 1.1K Mar 12 08:27 u70m5b8l2T3vnZHZ.txt
-rwx-wxrw- 1 root root 1.1K Mar 12 08:28 x9f1QlloTkYd5sfU.txt
-rw--wx--x 1 root root 1.1K Mar 12 08:29 eiUFiXPDk6oee8sL.txt
-r-xrwx--- 1 root root 1.1K Mar 12 08:30 kY4FOlfPt3qGR17K.txt
--wxr----- 1 root root 1.1K Mar 12 08:31 cDnYAOBE4mvBnh0C.txt
--wx--xr-x 1 root root 1.1K Mar 12 08:32 Uljik5BaQPOeMjfc.txt
-rw--w--wx 1 root root 1.1K Mar 12 08:33 OOVH70vCevC3FSZq.txt
-r-x---r-x 1 root root 1.1K Mar 12 08:34 HsxVbSgKw3d7tvFi.txt
-rwxr-xr-- 1 root root 1.1K Mar 12 08:35 fGMkb1gANtjLb5Qz.txt
-rw---xr-- 1 root root 1.1K Mar 12 08:36 6ReSGoJsRnFqhg7r.txt
----rwx--x 1 root root 1.1K Mar 12 08:37 uAkL5PqfK2I7K4PE.txt
----rw--wx 1 root root 1.1K Mar 12 08:38 lysJisb6wMlPG9WX.txt
--wx-wxr-- 1 root root 1.1K Mar 12 08:39 LuVzAMVXkpJYAxRM.txt
-rwx--xr-- 1 root root 1.1K Mar 12 08:40 GP4decBqHC6UL66s.txt
-rw---x-wx 1 root root 1.1K Mar 12 08:41 S5dyoQrp6a8grHOD.txt
----rw-r-x 1 root root 1.1K Mar 12 08:42 kcVtPjZM85b4B3V6.txt
-rwxr--rw- 1 root root 1.1K Mar 12 08:43 t7WeFKhvlS3e1Yet.txt
-r---wx-wx 1 root root 1.1K Mar 12 08:44 pU2aTHrPpCjthwwi.txt
-r---wx-w- 1 root root 1.1K Mar 12 08:45 Z0VC73hFMVt2AcO9.txt
---xr-xr-- 1 root root 1.1K Mar 12 08:46 bG0d9OBnfwVP2NS8.txt
--wxrw--w- 1 root root 1.1K Mar 12 08:47 Vp7XQiTt4ad9IDfB.txt
-rwx--xr-- 1 root root 1.1K Mar 12 08:48 GeDYq3hoIx7oijhO.txt
-rw---x-wx 1 root root 1.1K Mar 12 08:49 5UhgLeVLJWuEnc4W.txt
-r--rw-r-x 1 root root 1.1K Mar 12 08:50 B7HIyq5CKwGavwaW.txt
-rwxr--rwx 1 root root 1.1K Mar 12 08:51 3zkhN7A0Vqe0HgNC.txt
--w---xr-- 1 root root 1.1K Mar 12 08:52 KEY19YZmg8L92D1H.txt
-rw-rw---x 1 root root 1.1K Mar 12 08:53 HN5vsOJkU4004pEl.txt
-r-xrwxr-x 1 root root 1.1K Mar 12 08:54 W5xw54rLYvyj7qRM.txt
drwx------ 2 root root  16K Mar 12 09:08 lost+found
drwxr-xr-x 3 root root 4.0K Mar 12 09:08 .
drwxr-xr-x 3 grey grey 4.0K Mar 19 15:39 ..
```
Việc permission của các file trông ununiformed như vậy rất sus. Đặc biệt, các file có modification time chênh nhau đúng 1 phút, liên tục (từ đó cũng suy ra được rằng directory ``lost+found`` nằm ngoài pattern) -> rất có thể các file này được sinh ra tự động bởi 1 script.

Nhìn vào pattern permission thì *đoán* rằng bản thân permission của các file chính là 1 loại encryption: expression dạng ``--[ký tự]--[ký tự]-`` làm liên tưởng tới mã nhị phân: ``- -> 0``, ``[ký tự] -> 1``.

Ký tự đầu tiên trong permission ('-' hoặc 'd') thể hiện đó là file hay directory. Ở đây tất cả đều là file nên có thể loại bỏ ký tự đầu tiên khỏi pattern.
Thử chuyển permission của từng file sang binary rồi dịch sang text:
```C
#include <stdio.h>
#include <string.h>
#define max 512

int main(){
    char perm[max];
    fgets(perm,max,stdin);
    for(int i=0;i<strlen(perm);i++){
        if(i%10==0)
            perm[i]=' ';
        else if(perm[i]=='-')
            perm[i]='0';
        else
            perm[i]='1';
    }
    printf("%s",perm);
    
    return 0;
}
```
```bash
$ ./test
--wxr-x-w--rwx-w---x-r--rw--wx--wx---rw-----r-xr---rwx-wxrw--rw--wx--x-r-xrwx-----wxr-------wx--xr-x-rw--w--wx-r-x---r-x-rwxr-xr---rw---xr------rwx--x----rw--wx--wx-wxr---rwx--xr---rw---x-wx----rw-r-x-rwxr--rw--r---wx-wx-r---wx-w----xr-xr----wxrw--w--rwx--xr---rw---x-wx-r--rw-r-x-rwxr--rwx--w---xr---rw-rw---x-r-xrwxr-x
 011101010 111010001 100110011 011000110 000101100 111011110 110011001 101111000 011100000 011001101 110010011 101000101 111101100 110001100 000111001 000110011 011011100 111001100 110001011 000110101 111100110 100011011 100011010 001101100 011110010 111001100 110001011 100110101 111100111 010001100 110110001 101111101
```
Nếu chỉ lấy từng đoạn binary riêng lẻ ở trên rồi decode thì sẽ được gibberish. Ở đây, nên dịch ngược '}' sang binary: ``01111101``
Nhìn vào đoạn cuối của dãy binary thì có vẻ như flag được mã hóa theo từng block 8 ký tự. Tách lại dãy theo block 8 rồi decode:
```python=
binary_string = """
 011101010 111010001 100110011 011000110 000101100 111011110 110011001 101111000 
 011100000 011001101 110010011 101000101 111101100 110001100 000111001 000110011 
 011011100 111001100 110001011 000110101 111100110 100011011 100011010 001101100 
 011110010 111001100 110001011 100110101 111100111 010001100 110110001 101111101
"""

binary_string = "".join(binary_string.split())

bytes_list = [binary_string[i:i+8] for i in range(0, len(binary_string), 8)]

decoded_text = "".join(chr(int(b, 2)) for b in bytes_list if len(b) == 8)

print("Decoded Text:", decoded_text)
```
```bash
$ python test.py 
Decoded Text: utflag{3xp3rt_f0r3ns1c_4n4lys1s_:3c}
```

# 
Một cách đơn giản hơn là ném đoạn binary trên vào cyberchef:
![](http://note.bksec.vn/pad/uploads/c1c1494b-a7e6-4fdd-9e19-c0374dde42dc.png)

## Kết luận
Luôn cần lưu ý inspect modification time và permission khi làm việc với các đối tượng chứa nhiều file.

*9r3y*
#

