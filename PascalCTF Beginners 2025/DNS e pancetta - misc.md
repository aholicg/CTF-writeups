---
title: DNS e pancetta - misc

---

# DNS e pancetta - misc

## Description
> I've recently started studying a new cooking book and I think I've found the best recipe ever.
Do you wanna read it? Ask my dear friend DNS!

Download file [here](https://ctf.pascalctf.it/files/dc25b6908b09679ed76d7163d81f0b51/pancetta.pcapng?token=eyJ1c2VyX2lkIjozODQsInRlYW1faWQiOm51bGwsImZpbGVfaWQiOjIzfQ.Z9rrdQ.QrzNiwBJvsn_llH1ogPo_3CnqtI)

## Thought process
Để ý đề bài có nhắc đến "Ask my dear friend ***DNS***".

File được cho là pancetta.pcapng
Đầu tiên xem thử protocol hierarchy của file này trong Wireshark:
![](http://note.bksec.vn/pad/uploads/da0e1d00-9248-4b09-9376-c038d063bf09.png)

Thấy rằng mọi packet ở đây đều là DNS traffic -> dùng Wireshark filter = dns sẽ không giúp ích. 

Nhìn lướt qua cửa sổ packet list thì có vẻ DNS trong packet nào cũng có format ``[hex-encoded].attacker.com`` [1]:

![](http://note.bksec.vn/pad/uploads/7155d7a4-c1ad-4de4-8335-393677ecb5c9.png)

Mà theo đề bài đã gợi ý thì chúng ta nên chú ý vào DNS -> rất có thể chính những DNS này  chứa flag encode theo hex.

Để thuận tiện cho extract riêng phần hex-encoded string trong DNS từ từng packet thì ta chuyển sang dùng tshark:

```bash
$ tshark -r pancetta.pcapng -Y "dns.qry.name" -T fields -e dns.qry.name | awk -F'.' '{print $1}' | awk '!seen[$0]++'
attacker
4c6f72656d206970
73756d20646f6c6f
722073697420616d
65742c20636f6e73
6563746574757220
6164697069736369
...
54467b444e535f62
3334636f6e696e67
5f346c6c5f6f7665
725f7468655f706c
6163657d
```
***Giải thích:***

1. Đầu tiên, đọc file theo argument ``-r``, chọn filter hiển thị DNS (``dns.qry.name`` trong Wireshark) theo argument ``-Y`` và kết hợp ``-T fields -e dns.qry.name`` để chỉ print ra terminal field được chỉ định là ``dns.qry.name``.

2. Tiếp theo, lọc output để tách riêng phần hex-encoded string trong DNS bằng ``awk -F'.' '{print $1}'``.

3. Từ cửa sổ packet list:
![](http://note.bksec.vn/pad/uploads/81dc8e88-fa4d-406f-ac82-39d1e8ee8ab3.png)
-> mỗi ``standard query`` đều đi cùng ``standard query response`` -> khi tiến hành lọc như trên, output sẽ bị lặp, tiếp tục lọc unique bằng ``awk '!seen[$0]++'`` 

Decode đoạn hex nhận được:
![](http://note.bksec.vn/pad/uploads/896a1d0f-add5-483c-b2e9-a60c4db7db04.png)

-> ``pascalCTF{DNS_b34coning_4ll_over_the_place}``
#

[1] Lý do packet nào cũng có query DNS theo format ``[hex-encoded].attacker.com`` là bởi file .pcapng đã ghi lại 1 case về ***DNS Exfiltration***. 
Ở đây, threat actor đăng ký tên miền cho attacker.com, rồi setup nameserver cho tên miền đó, tức mọi query cho [something].attacker.com đều sẽ điều hướng đến server của threat actor thay vì 1 nền tảng [something].attacker.com thật sự.
Từ đó, threat actor có thể gửi thông tin qua dns query đến chính server của mình.

*9r3y*
#
