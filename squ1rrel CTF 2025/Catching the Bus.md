---
title: Catching the Bus

---

# Catching the Bus

## Description
> I’m waiting at the gym for my friend’s bus. Problem is, I forgot to ask her for the bus number she’s riding on. Could you find the bus based on our text conversation?
>
>Flag Format: Alphanumeric (no spaces). squ1rrel{REGISTRATIONNUMBER.OperatorName.FleetNumber}

Download file [here](https://drive.google.com/file/d/11G8i4u4fTJorGy6HfuoySJ4b-duBKoHd/view?usp=sharing)

##

Có 3 thông tin cần tìm:
1. [Registration number (~license number)]()
2. [Operator](https://)
3. Fleet number

## Phân tích ảnh chụp tin nhắn
* Trong description:"I'm waiting at **the gym** for my friend's bus"
Trong ảnh tin nhắn:
![](http://note.bksec.vn/pad/uploads/fb317407-1922-45db-86c2-3599c75f26f6.png)


-> Rachel là người đưa ra description, tin nhắn được chụp màn hình từ điện thoại của "friend" 

---
* Bus stop được nhắc đến trong ảnh (=bus stop mà Rachel đang đợi "friend, nhắc đến trong description):
![](http://note.bksec.vn/pad/uploads/98eff52e-2736-47d1-9242-aa8bb9a42e04.png)

![](http://note.bksec.vn/pad/uploads/75587c77-9c31-4de7-ad75-41d49b07cf8b.png)

**Bus stop này chỉ có 3 tuyến bus đi qua:**
![](http://note.bksec.vn/pad/uploads/3a1bd36b-0ac9-4c49-b913-6cd5a1192b08.png)

---
* Từ 2 tin nhắn đầu tiên:
![](http://note.bksec.vn/pad/uploads/ae36f465-1b5a-47c1-8760-f1e9808579da.png)

-> "friend" vừa giúp Rachel mua 1 đôi giày **Nike**, tại 1 **factory store** ở gần 1 quán bán gà rán (khả năng là 1 **fast food** store)

---
* Từ tin nhắn:
![](http://note.bksec.vn/pad/uploads/6858bc6b-00cd-4917-ba93-9760c2e44edb.png)

-> Bus mà "friend" đang đi có license plate chứa tên viết tắt của đại học mà 2 người từng học

Tin nhắn tiếp theo chứa hint về đại học đó:
![](http://note.bksec.vn/pad/uploads/2ef8658b-5a34-40a1-baa0-6492cd5a9101.png)

Đại học này ở gần Provo River, có thể tra bằng [googlep map](https://www.google.com/maps/search/college+or+university/@40.2519979,-111.6603561,15.17z/data=!4m8!2m7!3m6!1scollege+or+university!2sProvo+River,+Utah,+USA!3s0x874d9a86c225b48f:0x3e9d750ccc30098a!4m2!1d-111.4474167!2d40.5006865?entry=ttu&g_ep=EgoyMDI1MDQwOC4wIKXMDSoASAFQAw%3D%3D)

## Thought process
Chỉ có 3 tuyến bus đi qua destination bus stop, nên có thể check thử luôn.

Tuyến đầu tiên - `22x`:
![](http://note.bksec.vn/pad/uploads/67297e8f-6552-41a9-8960-1bd866c43c0f.png)

Tuyến này có điểm đầu là [Rotherham Interchange](https://maps.app.goo.gl/YJwwzaTo6kxo2jeF7)

![](http://note.bksec.vn/pad/uploads/c7e27f78-ed62-4cab-96da-67358c0ab2c3.png)

Tra thử fast food store và Nike factory store gần điểm này:

![](http://note.bksec.vn/pad/uploads/2655f855-aeee-41b3-ab24-abaff8872fb6.png)
![](http://note.bksec.vn/pad/uploads/a7bea74b-8d83-43bb-954f-1cc2d297e8e7.png)

Fast food store thì có thể phổ biến, nhưng 1 Nike factory store ở gần vậy thì có lẽ không phải chỉ là trùng hợp. 
Quyết định khai thác sâu hơn từ tuyến bus 22x này. 

Tra cứu thêm với từ khóa "[UK bus 22x operator](https://duckduckgo.com/?t=ffab&q=uk+bus+line+22x+operator&ia=web)" thì được kết quả: `Stagecoach (Yorkshire)`

Tiếp theo là việc tìm Registration number và Fleet number. Các UK bus database thường có thông tin về Registration number đi kèm với Fleet number.
Clue duy nhất là ta có ở đây là tên viết tắt của 1 đại học nào đó gần Provo river.

Tuy nhiên, số lượng database thì rất nhiều, và đại học gần Provo River cũng nhiều. Ở đây, nên dùng sự trợ giúp của 1 AI với các prompt như "List universities/colleges near Provo River and their name abbreviations", "best databases to search for UK bus registration numbers and fleet numbers of operator Stagecoach": [grok](https://grok.com/share/c2hhcmQtMg%3D%3D_26cd4df8-4722-4472-8ab0-eb9f24e8367b)

Kết quả đưa ra 2 tên đại học và 1 database nên dùng:
* đại học: UVU và BYU (thử với UVU và thấy rằng BYU khả quan hơn)
* database: [ukbuses.co.uk](https://www.ukbuses.co.uk/)

Trong web trên có database của [Stagecoach Yorkshire](https://www.ukbuses.co.uk/fleet/stagecoachyorkshire.pdf), đã thế tra với "BYU" chỉ cho ra 1 kết quả duy nhất: 

![](http://note.bksec.vn/pad/uploads/a85ecef0-331f-4f85-aca7-718e1ac4074c.png)

Tuy nhiên `squ1rrel{YN62BYU.StagecoachYorkshire.12176}` lại không phải flag.

Sau khi đọc discord của giải này:

![](http://note.bksec.vn/pad/uploads/51be7069-9a45-47cc-8fcc-c265358e0ec4.png)

Vậy nên mở ticket
![](http://note.bksec.vn/pad/uploads/a98c111c-9f07-4c43-87a2-460b5e8c5946.png)

`squ1rrel{YN62BYU.StageCoach.12176}` vẫn không phải flag.

Sometimes a little brute-forcing is necessary.
Using this list:

![](http://note.bksec.vn/pad/uploads/b025aa88-d713-421f-839e-5412e6b167da.png)

Và trong [stagecoacheastmidlands database](https://www.ukbuses.co.uk/fleet/stagecoacheastmidlands.pdf)
![](http://note.bksec.vn/pad/uploads/9a0c563b-13f6-441c-88dc-0d03cb751256.png)
(1 kết quả duy nhất)

Bây giờ thử lại:
`squ1rrel{SF10BYU.StageCoach.24183}`
Đây là flag đúng.

#
*9r3y*







