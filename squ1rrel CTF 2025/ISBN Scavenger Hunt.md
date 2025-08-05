---
title: ISBN Scavenger Hunt

---

# ISBN Scavenger Hunt - osint

## Description
> We’re trying to restore an old book collection, but all we can find about this one is this old letter. Can you help us find the book name and its author?
>
>flag format: squ1rrel{BookName_AuthorFullName}

Download file [here](https://drive.google.com/file/d/18A4CLFf8LiTeQYAMO98mmxRikogx2QLP/view?usp=drive_link)

## Thought process
Nội dung bức thư:
> My Dearest R.S.,
>
>Across the entire fabric of space-time, I have found many an intriguing phenomenon, and for so many of them, a frustrating lack of documentation. Unfortunately, on a recent expedition to the Plutonian rocklands with my trusty companion, I sustained a serious injury, and it seems like that last adventure shall have to be my last. 
>
>There is nothing quite like a brush with death that makes one rethink his entire life goals. The injury cauterized off a part of my soul that I daresay I will never get back, but I am distracting myself by putting together a chronicle of the universe. 
>
>I hope I shall have the opportunity to show this encyclopedia, as I am calling it, to you in person. But you know me, I am never one to tempt fate. So herein I shall leave you with a **secret code** for contigencies: begin with the **first code found in the fantastical kingdom from which all books originate**; then revisit the **gardens in dear old Trenton**; and finally find the **park in South Gate**. 
>
>My darling — I cannot wait to share with you the secrets of our mysterious universe, with its millions of whirling worlds and its vast, almost unimaginable distances. 
>
>— R.K.

Để ý đề bài có nhắc đến [ISBN](https://en.wikipedia.org/wiki/ISBN), nên có những keypoints rút ra được từ bức thư như sau:
* Tác giả của quyển sách có tên viết tắt là R.K.
* "secret code" nhắc đến trong thư khả năng cao nói đến ISBN.
* Những thông tin được bôi đen trong thư, khi kết hợp lại, có thể tạo thành 1 ISBN hoàn chỉnh -> tra được quyển sách cần tìm tại các database như [isbnsearch.org](https://isbnsearch.org/)
* Nội dung của quyển sách có vẻ là 1 enclyopedia - bách khoa toàn thư hoặc những nội dung "chứa rất nhiều thông tin" tương tự.

## Solution

1. *first code found in the fantastical kingdom from which all books originate*

Sau khi tìm hiểu sơ qua về ISBN thì có thể nhận thấy rằng, ISBN của các quyển sách ngày nay đa số đều bắt đầu với 1 số tiền tố: `978`


Tiếp theo là 2 thông tin về địa điểm. 
1 địa điểm địa lí có thể cho ta nhiều loại "code" như GPS coordinates, postal code...
Đầu tiên, khi làm osint, tốt nhất cứ nên tra thử google map của 2 địa điểm được nhắc đến trong thư.

2. *gardens in dear old Trenton*

![](http://note.bksec.vn/pad/uploads/7659d47f-efd6-4b72-824f-899f56fe3e0e.png)

Có thể thấy Google map show ngay 1 loại code cho địa điểm này: `NJ 08610`

3. *park in South Gate* 
![](http://note.bksec.vn/pad/uploads/71a04c4d-4cab-45c8-bcd9-90593ac7ddba.png)

`CA 90280`

Vì mã ISBN chỉ gồm toàn số nên ta sẽ thử chỉ lấy phần số của 2 mã địa điểm trên, ghép lại theo đúng thứ tự:

`978-08610-90280`
![](http://note.bksec.vn/pad/uploads/0e0c8a98-3a24-40eb-92c5-83a033cb1e27.png)

Tựa đề của quyển sách có vẻ rất hợp với miêu tả trong bức thư. 

Search thêm với từ khóa "[The Question and Answer Book of Space author R.K](https://duckduckgo.com/?q=Question+and+Answer+Book+of+Space+author+R.K&t=ffab&ia=web)":
![](http://note.bksec.vn/pad/uploads/6a35c75b-449d-43be-b30f-72e6ab78adfd.png)

Vậy flag là (lưu ý format theo đúng như đề bài gợi ý)`squ1rrel{TheQuestionAndAnswerBookOfSpace_RobinKerrod}`

#
*9r3y*

