---
title: ' Volkswagen CAN Checksum'

---

#  Volkswagen CAN Checksum 
## Description
### Challenge
The goal of this challenge is to compute the correct one byte checksum XX for the CAN message with payload XX0f0300. The flag is of the format CTF{XX}.
### Background
VW uses the Autosar CRC8 8H2F checksum. Before computing the CRC, the payload is extended by a "secret" byte based on the Arbitration ID. Part of this challenge is figuring out what this "secret" byte is based on some traffic logged from the car. For some messages the "secret" value depends on the value of the counter, but that is not the case for this message.

Example code to generate the CRC:
```python
import crcmod

crc = crcmod.mkCrcFun(
    poly=0x100 + polynomial,
    initCrc=initial_value
    rev=False,
)

data = payload + secret_byte
checksum = crc(data) ^ 0xff
```
### Logged Messages
The following 15 messages were captured from the car. Note the first byte is the checksum of the message, then second byte contains a counter.
```
0 74000300
1 c1010300
2 31020300
3 84030300
4 fe040300
5 4b050300
6 bb060300
7 0e070300
8 4f080300
9 fa090300
10 0a0a0300
11 bf0b0300
12 c50c0300
13 700d0300
14 800e0300
```
### References
Autosar CRC specification: https://www.autosar.org/fileadmin/standards/R22-11/CP/AUTOSAR_SWS_CRCLibrary.pdf

## Thought process
Việc đầu tiên cần làm là đọc hiểu code snippet "Example code to generate the CRC" mà đề cho:
```py
import crcmod

crc = crcmod.mkCrcFun(
    poly=0x100 + polynomial,
    initCrc=initial_value
    rev=False,
)

data = payload + secret_byte
checksum = crc(data) ^ 0xff
```
Mục đích của đoạn code trên tạo hàm `crc` và dùng nó để tính ra checksum dựa vào payload và secret_byte mà ta tìm được. 
* Payload là 3 byte cuối đoạn logged messages mà đề cho:
```
0 74000300
1 c1010300
2 31020300
3 84030300
4 fe040300
5 4b050300
6 bb060300
7 0e070300
8 4f080300
9 fa090300
10 0a0a0300
11 bf0b0300
12 c50c0300
13 700d0300
14 800e0300
```
* `polynomial` và `initial_value` của Autosar CRC8 8H2F tìm được trong link ở phần References:
![image](https://hackmd.io/_uploads/rktwTG0wgg.png)

Và nhiệm vụ của ta là tìm ra secret_byte dựa vào đoạn log trên.

Vì đề đã nói rõ "the payload is extended by a "secret" byte based on the Arbitration ID ... For some messages the "secret" value depends on the value of the counter, but that is not the case for this message", chứng tỏ ta không thể dùng Arbitration ID để tìm ra secret_byte, mà phải brute force.
```py
import crcmod
# Implement hàm crc từ example
crc = crcmod.mkCrcFun(
    poly=0x100 + 0x2f,
    initCrc=0xff,
    rev=False,
)

# Log (checksum, payload)
messages = [
    ('74', '00 03 00'),
    ('c1', '01 03 00'),
    ('31', '02 03 00'),
    ('84', '03 03 00'),
    ('fe', '04 03 00'),
    ('4b', '05 03 00'),
    ('bb', '06 03 00'),
    ('0e', '07 03 00'),
    ('4f', '08 03 00'),
    ('fa', '09 03 00'),
    ('0a', '0a 03 00'),
    ('bf', '0b 03 00'),
    ('c5', '0c 03 00'),
    ('70', '0d 03 00'),
    ('80', '0e 03 00'),
]

# Bước 1: Bruteforce 
for secret in range(256):
    all_match = True
    for checksum, payload_str in messages:
        payload = bytes.fromhex(payload_str) + bytes([secret])
        # result được XOR với 0xff, theo cách tính ra checksum ở dòng cuối example snippet
        result = crc(payload) ^ 0xff
        # kiểm tra xem checksum tính ra theo bruteforce (result) có trùng với checksum trong log không
        if result != int(checksum, 16):
            all_match = False
            break
    if all_match:
        print(f"Found secret byte: 0x{secret:02X}")
        secret_byte = secret
        break

# Step 2: tính checksum cho payload XX0f0300 đề yêu cầu, dựa trên secret_byte đã tìm được
payload = bytes.fromhex("0f 03 00") + bytes([secret_byte])
checksum = crc(payload) ^ 0xFF

print(f"Final flag: CTF{{{checksum:02x}}}")
```

Kết quả:
![image](https://hackmd.io/_uploads/H1IPkXCDlx.png)

