# Tài liệu OpenAPI của hệ thống HyperSMS V2.1

## 1 Tổng quan

### 1.1 Giới thiệu thuật ngữ
Danh Từ | Mô tả | Các ghi chú
:----: |:---- |:---
app	| Phía truy cập ứng dụng  |	Phía truy cập ứng dụng HyperSMS OpenAPI
appId |	Mã nhận dạng ứng dụng |	Cung cấp sau khi mở tài khoản
appKey |	Khóa ứng dụng |	Cung cấp sau khi mở tài khoản
appSecret |	Mật khẩu ứng dụng |	Cung cấp sau khi mở tài khoản
token |	mã thông báo máy chủ |	Được tạo sau khi gọi API đăng nhập
callbackApproveUrl |	Địa chỉ nhận trạng thái phê duyệt |	Được cung cấp bởi bên truy cập trước khi mở tài khoản
callbackSendUrl |	Gửi địa chỉ nhận trạng thái	 |Được cung cấp bởi bên truy cập trước khi mở tài khoản


### 1.1 Quy trình mở tài khoản
 
1. Bên truy cập cung cấp 2 địa chỉ nhận lại kết quả trả về, callbackApproveUrl/callbackSendUrl

2. Đăng ký mở tài khoản trong nền tảng HyperSMS và tạo appId / appKey / appSecret và cung cấp cho bên truy cập


### 1.2 Quy trình gọi API
1. Bên truy cập ứng dụng gọi API đăng nhập để lấy mã thông qua appId / appSecret
2.	Bằng cách chỉ định mã thông báo token trong tiêu đề; sau đó gọi các API khác như mong muốn
3.	Giá trị Sign cần được chỉ rõ khi gọi mỗi hàm nghiệp vụ kinh doanh API, và  thủ tục tính toán chi tiết như trong “phụ lục G”.
4.  Nếu gửi yêu cầu theo kiểu Ride, chi tiết xem "2.4.3"(V2.1.safe) để biết định dạng tiêu chuẩn yêu cầu
5.	Nếu dữ liệu nghiệp vụ kinh doanh cần được phê duyệt, trạng thái phê duyệt sẽ được gọi lại thông qua địa chỉ “callbackApproveUrl”
6.	Sau khi tác vụ gửi được xử lý, trạng thái báo cáo gửi DR và  trạng thái nhận tin Retrieve sẽ được gọi lại thông qua địa chỉ “callbackSendUrl”

### 1.3 Cổng truy cập thống nhất:

- Test-ENV: http://client.vn-vtt-test.corporfountain.com/openapi/v2/
- Prod-ENV: https://openapi.viettel.hypersms.vn/v2/

Ghi chú: địa chỉ bên trên có thể thay đổi, vui lòng xác nhận với đầu mối của Viettel.

## 2 Tiêu chuẩn và thông số kỹ thuật 

### 2.1 Giao thức truyền thông

HTTPS/HTTP

### 2.2 Phương thức yêu cầu

Tất cả API đều sử dụng thống nhất phương thức POST [raw] và định dạng dữ liệu là JSON thống nhất

### 2.3 Mã hóa ký tự
Thống nhất với mã hóa bộ ký tự UTF-8

### 2.4 Tiêu chuẩn định dạng API

#### 2.4.1 Định dạng tiêu chuẩn của tiêu đề
``` JSON
{
 "Content-Type":"application/json",
 "appId": 0101010,
 "timestamp": 1621503576000,
 "token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIxMDAwMDE2ODMiLCJpYXQiOjE2MjE0OT"
}
```
Mô tả:

Các tham số |Bắt buộc hay không|Mô tả
:----: |:---- |:---
Content-Type |Tùy chọn |Loại yêu cầu, hiện chỉ hỗ trợ ứng dụng / json
appId |Bắt buộc | Mã nhận dạng ID ứng dụng bên truy cập
token |Bắt buộc | Mã thông báo token sau khi đăng nhập, chuỗi trống nếu chưa đăng nhập
isTokenRide	 |Optional	|yêu cầu theo kiểu Ride(true/false);Mặc định là false (V2.1.safe)
timestamp |Bắt buộc | Dấu thời gian UNIX hiện tại (mili giây)

#### 2.4.2 Định dạng tiêu chuẩn phần thân của yêu cầu
``` JSON
{
 "key1": "Value1",
 "key2": "Value2",
 "timestamp": 1621503576000,
 "sign": "600e5d07f445be432d86d9271fb1df9b1"
}
```
Mô tả:

Tham số |Bắt buộc hay không |Mô tả
:----: |:---- |:---
< key1 > | | Tham khảo các yêu cầu định dạng của các hàng API thực tế 
sign |Bắt buộc | Giá trị Sign của yêu cầu này
timestamp |Bắt buộc | Dấu thời gian UNIX hiện tại (mili giây)

#### 2.4.3  Định dạng tiêu chuẩn phần thân của yêu cầu theo kiểu Ride(V2.3.2.brand)

-Kiểu "Ride" là để thích ứng với tình hình an toàn mạng trong các môi trường khác nhau. Kiểu "Ride" hỗ trợ cả phương thức không mã hóa và mã hóa Base64 để gửi yêu cầu
-Nếu gửi yêu cầu theo kiểu "Ride", header phải chỉ định isTokenRide = true

##### 2.4.3.1 Gửi yêu cầu theo phương thức không mã hóa với văn bản rõ
``` JSON
{
 "data" : "{\"key1\":\"Value1\",\"key2\":\"Value2\"\"timestamp\":1621503576000,\"isTokenRide\":false\"sign\":\"600e5d07f445be432d86d9271fb1df9b1\"}"
}
```
Mô tả:

Tham số |Bắt buộc hay không |Mô tả               
:----:		 |:----	    |:---
data  |Required	| Chuỗi JSON (Request Body) văn bản rõ không mã hóa

##### 2.4.3.2 Gửi yêu cầu theo phương thúc mã hóa Base64
``` JSON
{
 "data" : "eyJrZXkxIjoiVmFsdWUxIiwia2V5MiI6IlZhbHVlMiIidGltZXN0YW1wIjoxNjIxNTAzNTc2MDAwLCJpc1Rva2VuUmlkZSI6ZmFsc2Uic2lnbiI6IjYwMGU1ZDA3ZjQ0NWJlNDMyZDg2ZDkyNzFmYjFkZjliMSJ9"
}
```
Mô tả:

Tham số |Bắt buộc hay không |Mô tả                 
:----:		 |:----	    |:---
data  |Required	|  Chuỗi JSON (Request Body) sau khi mã hóa Base64. Chuỗi JSON được mã hóa UTF-8, không cần escape "\" 

#### 2.4.4 Định dạng tiêu chuẩn phần thân của bản tin phản hồi
``` JSON
{
 "code": "100",
 "msg": "api.response.code.success",
 "msgArgs": null,
 "data": {
    "code": "123123123"
  }
}
```
Mô tả:

Tham số |Bắt buộc hay không |Mô tả
:----: |:---- |:---
code |Bắt buộc |Trạng thái trả về
msg |Bắt buộc | Tin nhắn trả về
msgArgs | Tùy chọn | Mô tả mở rộng tin nhắn trả về
data |Bắt buộc | Giá trị trả lại của bản tin Trả về (ghi chú: các API khác nhau trả về loại giá trị khác nhau)


## 3. Hàm API liên quan tới xác thực

### 3.1 Hàm API đăng nhập
- **Mô tả API:** Bên truy cập nhận được mã thông báo token toàn cầu thông qua API đăng nhập
- **Địa chỉ API:** app/login


#### 3.1.1 Các tham số của yêu cầu
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;appId |string | Bắt buộc | Access party application ID
&emsp;appSecret |string | Bắt buộc | Access party password


Ví dụ về 1 yêu cầu:

``` JSON
{
  "appId":"18520322032",
  "appSecret":"acb000000"
}

```


#### 3.1.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc | Mã bản tin trả về, xem “phụ lục A” để thấy chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;
&emsp;expire |long |Bắt buộc | Thời gian hết hạn (tính bằng giây)
&emsp;token |string |Bắt buộc | Mã token truy cập

Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "expire": 604800,
    "Token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIxMDAwMDE2ODMiLCJpYXQiOjE2MjE1NjQ0OTAsImV4cCI6MTYyMjE2OTI5MH0.O1sqK5jk-wI2DQNOsunZ5cU0x9369cTKxItmfPRHA57Kk5ynhSoWnV5YyysX65l4LqsUKITEBUWnZ78h-UdrJA"
  }
}
```


## 4. Hàm API liên quan tới Brandname

### 4.1 Hàm API tạo thương hiệu mới
- **Mô tả API:** Thông tin thương hiệu mới
- **Địa chỉ API:** app/brandname/add

#### 4.1.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc | Dấu thời gian UNIX hiện tại (mili giây)
&emsp;brandName |string | Bắt buộc | Tên thương hiệu
&emsp;description |string | Tùy chọn | Mô tả
&emsp;certified |string | Tùy chọn | Chứng chỉ URL


Ví dụ về 1 yêu cầu:

``` JSON
{
  "brandName":"Google",
  "description":" Mô tả ",
  "certified":"http://www.google.cn/aaaqeqeqw.gif",
  "timestamp": 1621503576000,
  "sign": "600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 4.1.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc | Mã trả về , xem “phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;
&emsp;code |string |Bắt buộc | Mã thương hiệu


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "code": "1212300"
  }
}
```

### 4.2 Hàm API truy vấn thương hiệu phân trang
- **Mô tả API:** Truy vấn thông tin thương hiệu, hỗ trợ phân trang 
- **Địa chỉ API:** app/brandname/query

#### 4.2.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, xem “phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc | Dấu thời gian UNIX hiện tại (mili giây)
&emsp;brandName |string | Tùy chọn | Tên thương hiệu
&emsp;code |string | Tùy chọn |  Mã thương hiệu
&emsp;currPage |int | Bắt buộc | Số trang hiện tại, giá trị mặc định là 1
&emsp;pageSize |int | Bắt buộc | Số lượng mục trên mỗi trang, giá trị mặc định là 10


Ví dụ một yêu cầu:

``` JSON
{
 "brandName":"",
 "code":"",
 "currPage": 1,
 "pageSize": 1,
 "timestamp": 1621503576000,
 "sign": "600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 4.2.2 Kết quả trả về

Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc | Mã trả về, xem “phụ lục A”
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;
&emsp;totalCount |int |Bắt buộc | Số tổng
&emsp;totalPage |int |Bắt buộc | Tổng số trang
&emsp;list |array |Tùy chọn | Kết quả truy vấn
&emsp;&emsp;code |string |Bắt buộc | Mã thương hiệu
&emsp;&emsp;brandName |string | Bắt buộc | Tên thương hiệu
&emsp;&emsp;certified |string | Tùy chọn | Chứng chỉ URL
&emsp;&emsp;createTime |long | Tùy chọn | Tạo dấu thời gian (mili giây)
&emsp;&emsp;status |int | Bắt buộc | Trạng thái dữ liệu, xem phụ lục C


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "totalCount": 100,
    "totalPage": 2,
    "currPage": 1,
    "pageSize": 1,
    "list": [
       {
          "brandName":"bName",
          "code":"XXX13123",
          "brandName": "http://www.google.cn/aaaqeqeqw.gif",
          "createTime": 1621503576000,
          "status": 1
       }
    ]
  }
}
```

### 4.3 Hàm API truy vấn chi tiết thương hiệu
- **Mô tả API:** Truy vấn chi tiết thương hiệu, bao gồm thông tin phê duyệt mới nhất, v.v.
- **Địa chỉ API:** app/brandname/info

#### 4.3.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, xem “phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc | Dấu thời gian UNIX hiện tại (mili giây)
&emsp;code |string | Bắt buộc | Mã thương hiệu


Ví dụ về 1 yêu cầu:

``` JSON
{
   "code":"001aaab",
  "timestamp": 1621503576000,
  "sign": "600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 4.3.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc | Mã trả về, xem “phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;
&emsp;code |string |Bắt buộc | Mã thương hiệu
&emsp;brandName |string | Bắt buộc | Tên thương hiệu
&emsp;certified |string | Tùy chọn | Chứng chỉ URL
&emsp;createTime |long | Tùy chọn | Tạo dấu thời gian (mili giây)
&emsp;status |int | Bắt buộc | Trạng thái dữ liệu, xem “phụ lục D” để biết chi tiết
&emsp;approve |object | Tùy chọn | thông tin Phê duyệt 
&emsp;&emsp;status |int | Tùy chọn | Trạng thái phê duyệt, Xem “Phụ lục E” để biết chi tiết
&emsp;&emsp;opinion |string | Tùy chọn | ý kiến Phê duyệt 


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
      "brandName":"bName",
      "code":"XXX13123",
      "brandName": "http://www.google.cn/aaaqeqeqw.gif",
      "createTime": 1621503576000,
      "status": 1,
      "approve": {
         "status": 0,
         "opinion": "pass"
      }
  }
}
```

### 4.4  Hàm API vô hiệu hóa Thương hiệu
- **Mô tả API:** Việc sắp xếp dữ liệu thương hiệu khi ngoại tuyến không hợp lệ
- **Địa chỉ API:** app/brandname/invalid

#### 4.4.1 Các tham số của yêu cầu 
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc | Dấu thời gian UNIX hiện tại (mili giây)
&emsp;code |string | Bắt buộc | Mã thương hiệu


Ví dụ về 1 yêu cầu:

``` JSON
{
  "code":"001aaab",
  "timestamp": 1621503576000,
  "sign": "600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 4.4.2 Giá trị trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": null
}

```



## 5. Hàm API liên quan tới Mẫu Template 

### 5.1 Hàm API tạo Mẫu template
- **Mô tả API:** Thông tin mẫu template mới
- **Địa chỉ API:** app/template/add

#### 5.1.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;type |int | Bắt buộc |Loại sản phẩm; xem “Phụ lục B” để biết thêm chi tiết
&emsp;brandNameCode |string | Bắt buộc | Mã thương hiệu
&emsp;productItemCode |string | Bắt buộc | Các mục thuộc về sản phẩm; xem “Phụ lục C” để biết chi tiết
&emsp;title |string | Bắt buộc | Tiêu đề mẫu
&emsp;content |string | Bắt buộc | Nội dung mẫu (hỗ trợ tối đa 5 định dạng {0} tham số động)



Ví dụ về 1 yêu cầu:

- **Mẫu template bình thường**

``` JSON
{
  "type":2,
  "brandNameCode":"001aaab",
  "productItemCode":"SMS_AD_GROUP_1",
  "title":"temp title",
  "content":"Hi, My name is HAHA",
  "timestamp": 1621503576000,
  "sign": "600e5d07f445be432d86d9271fb1df9b1"
}

```

- **Mẫu template tham số động**

``` JSON
{
  "type":2,
  "brandNameCode":"001aaab",
  "productItemCode":"SMS_AD_GROUP_1",
  "title":"temp title",
  "content":"Hi, My name is {0}",
  "timestamp": 1621503576000,
  "sign": "600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 5.1.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;
&emsp;code |string |Bắt buộc |Mã mẫu template


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "code": "1212300"
  }
}
```


### 5.2 Hàm API chỉnh sửa Mẫu template
- **Mô tả API:** Chỉnh sửa thông tin mẫu template
- **Địa chỉ API:** app/template/edit

#### 5.2.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;code |string |Bắt buộc |Mã mẫu template
&emsp;type |int | Tùy chọn |Loại sản phẩm; xem “Phụ lục B” để biết thêm chi tiết
&emsp;brandNameCode |string | Tùy chọn | Mã thương hiệu
&emsp;productItemCode |string | Tùy chọn | Các mục thuộc về sản phẩm; xem “Phụ lục C” để biết chi tiết
&emsp;title |string | Tùy chọn | Tiêu đề của Mẫu template
&emsp;content |string | Tùy chọn | Nội dung mẫu (hỗ trợ tối đa 5 định dạng {chỉ số} thông số động)


Ví dụ về 1 yêu cầu:

``` JSON
{
  "code":"1212300",
  "title":"New Temp title",
  "timestamp": 1621503576000,
  "sign": "600e5d07f445be432d86d9271fb1df9b1"
}

```


#### 5.2.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;
&emsp;code |string |Bắt buộc |Mã mẫu template


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "code": "1212300"
  }
}
```


### 5.3 Hàm API truy vấn Mẫu template phân trang 
- **Mô tả API:** truy vấn Dữ liệu mẫu theo các điều kiện cụ thể, hỗ trợ phân trang
- **Địa chỉ API:** app/template/query

#### 5.3.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;code |string |Tùy chọn |Mã mẫu template
&emsp;type |int | Tùy chọn |Loại sản phẩm; xem “Phụ lục B” để biết thêm chi tiết
&emsp;brandNameCode |string | Tùy chọn | Mã thương hiệu
&emsp;productItemCode |string | Tùy chọn | Các mục thuộc về sản phẩm; xem “Phụ lục C” để biết chi tiết
&emsp;title |string | Tùy chọn | Tiêu đề của Mẫu template (support fuzzy check)
&emsp;currPage |int | Bắt buộc | Số trang hiện tại, giá trị mặc định là 1
&emsp;pageSize |int | Bắt buộc | Số lượng mục trên mỗi trang, giá trị mặc định là 10


Ví dụ về 1 yêu cầu:

``` JSON
{
  "brandNameCode":"",
  "code":"",
  "currPage": 1,
  "pageSize": 1,
  "timestamp": 1621503576000,
  "sign": "600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 5.3.2 Kết quả trả về

Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc | Mã trả về, Xem: “Phụ lục A”
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;
&emsp;totalCount |int |Bắt buộc |Số tổng
&emsp;totalPage |int |Bắt buộc |Tổng số trang
&emsp;list |array |Tùy chọn | Kết quả truy vấn
&emsp;&emsp;code |string |Bắt buộc |Mã mẫu template
&emsp;&emsp;type |int | Bắt buộc | Loại mẫu; xem “Phụ lục B” để biết thêm chi tiết
&emsp;&emsp;brandNameCode |string | Bắt buộc | Mã thương hiệu
&emsp;&emsp;productItemCode |string | Tùy chọn | Các mục thuộc về sản phẩm; xem “Phụ lục C” để biết chi tiết
&emsp;&emsp;title |string | Tùy chọn | Tiêu đề của Mẫu template
&emsp;&emsp;createTime |long | Tùy chọn | Tạo dấu thời gian (mili giây)
&emsp;&emsp;status |int | Bắt buộc | Trạng thái dữ liệu, Xem “Phụ lục D” để biết chi tiết


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "totalCount": 100,
    "totalPage": 2,
    "currPage": 1,
    "pageSize": 1,
    "list": [
       {
          "type":2,
          "brandNameCode":"001aaab",
          "productItemCode":"SMS_AD_GROUP_1",
          "title":"temp title",
          "createTime": 1621503576000,
          "status": 1
       }
    ]
  }
}
```


### 5.4 Hàm API truy vấn chi tiết Mẫu template
- **Mô tả API:** Truy vấn chi tiết mẫu theo mã mẫu, bao gồm thông tin phê duyệt
- **Địa chỉ API:** app/template/info

#### 5.4.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;code |string |Bắt buộc |Mã mẫu template


Ví dụ về 1 yêu cầu:

``` JSON
{
  "code":"001aaab",
  "timestamp": 1621503576000,
  "sign": "600e5d07f445be432d86d9271fb1df9b1"
}

```


#### 5.4.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;
&emsp;code |string |Bắt buộc |Mã mẫu template
&emsp;type |int | Bắt buộc |Template type; see "Appendix B" for details
&emsp;brandNameCode |string | Bắt buộc | Mã thương hiệu
&emsp;productItemCode |string | Bắt buộc | Các mục thuộc về sản phẩm; xem “Phụ lục C” để biết chi tiết
&emsp;title |string | Bắt buộc | Tiêu đề của Mẫu template
&emsp;content |string | Bắt buộc | Template content
&emsp;createTime |long | Bắt buộc | Tạo dấu thời gian (mili giây)
&emsp;status |int | Bắt buộc | Trạng thái dữ liệu, Xem “Phụ lục D” để biết chi tiết
&emsp;approve |object | Tùy chọn | Thông tin phê duyệt
&emsp;&emsp;status |int | Bắt buộc | Trạng thái phê duyệt, Xem “Phụ lục E” để biết chi tiết
&emsp;&emsp;opinion |string | Tùy chọn | Ý kiến phê duyệt


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
      "type":2,
      "brandNameCode":"001aaab",
      "productItemCode":"SMS_AD_GROUP_1",
      "title":"temp title",
      "createTime": 1621503576000,
      "status": 1,
      "content":"Hi, My name is HAHA"
      "approve": {
         "status": 0,
         "opinion": "pass"
      }
  }
}
```

### 5.5 Hàm API vô hiệu hóa Mẫu template
- **Mô tả API:**  Dữ liệu mẫu ngoại tuyến và không hợp lệ
- **Địa chỉ API:** app/template/invalid

#### 5.3.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;code |string | Bắt buộc | Mã mẫu template


Ví dụ về 1 yêu cầu:

``` JSON
{
  "code":"001aaab",
  "timestamp": 1621503576000,
  "sign": "600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 5.3.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": null
}

```



## 6. Hàm API liên quan tới Gửi

### 6.1 Hàm API Gửi
- **Mô tả API:** Gửi tới một số
- **Địa chỉ API:** app/send

#### 6.1.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;type |int | Bắt buộc |Loại sản phẩm; xem “Phụ lục B” để biết thêm chi tiết
&emsp;templateCode |string | Tùy chọn | Mã mẫu HyperSMS [phiên bản V2.0 hiện không được hỗ trợ]
&emsp;smsTemplateCode |string | Bắt buộc | Mã mẫu SMS template
&emsp;subId |string | Bắt buộc | Gửi tới số
&emsp;param |array | Tùy chọn | Bộ các giá trị tham số động. Lưu ý: Thứ tự phải tương ứng với tham số động (chỉ mục) trong mẫu template
&emsp;encodeType |int | Tùy chọn | Tùy chọn (0: plaintext, 1: mã hóa toán tử bởi Viettel) Giá trị mặc định là 0


Ví dụ về 1 yêu cầu:

- **Normal Template**

``` JSON
{
  "type":2,
  "smsTemplateCode":"001aaab",
  "subId":"091313123123",
  "encodeType": 0,
  "timestamp": 1621503576000,
  "sign": "600e5d07f445be432d86d9271fb1df9b1"
}

```

- **Dynamic parameter template**

``` JSON
{
  "type":2,
  "smsTemplateCode":"001aaab",
  "subId":"091313123123",
  "param": ["0","2"]
  "encodeType": 0,
  "timestamp": 1621503576000,
  "sign": "600e5d07f445be432d86d9271fb1df9b1"
}

```


#### 6.1.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;
&emsp;code |string |Bắt buộc |Send code
&emsp;status |int |Tùy chọn |Send status (1: normal; 0: failed)
&emsp;msg |string |Tùy chọn |Send details


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "code": "160139858512181547",
    "status": 1
    "msg": null
  }
}
```


### 6.2  Hàm API gửi nhiều số 
- **Mô tả API:** Gửi tới nhiều số (hiện chỉ hỗ trợ 500 số)
- **Địa chỉ API:** app/send/batch

#### 6.2.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;type |int | Bắt buộc |Loại sản phẩm; xem “Phụ lục B” để biết thêm chi tiết
&emsp;templateCode |string | Tùy chọn | Mã mẫu HyperSMS [phiên bản V2.0 hiện không được hỗ trợ]
&emsp;smsTemplateCode |string | Bắt buộc | Mã mẫu SMS template
&emsp;subList |array | Bắt buộc | Gửi nhiều số
&emsp;&emsp;subId |string | Bắt buộc | Gửi tới số
&emsp;&emsp;param |array | Tùy chọn | Bộ các giá trị tham số động. Lưu ý: Thứ tự phải tương ứng với tham số động (chỉ mục) trong mẫu template
&emsp;&emsp;encodeType |int | Tùy chọn | Tùy chọn (0: plaintext, 1: mã hóa toán tử bởi Viettel) Giá trị mặc định là 0


Ví dụ về 1 yêu cầu:

- **Normal Template**

``` JSON
{
  "type":2,
  "smsTemplateCode":"001aaab",
  "subList":[
    {
      "subId":"091313123121"
    },
    {
      "subId":"091313123122"
    }
  ],
  "timestamp": 1621503576000,
  "sign": "600e5d07f445be432d86d9271fb1df9b1"
}

```

- **Dynamic parameter template**

``` JSON
{
  "type":2,
  "smsTemplateCode":"001aaab",
  "subList":[
    {
       "subId":"091313123121",
       "param": ["0","2"]
    },
    {
      "subId":"091313123122",
      "param": ["0","2"]
    }
  ],
  "timestamp": 1621503576000,
  "sign": "600e5d07f445be432d86d9271fb1df9b1"
}

```


#### 6.2.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;
&emsp;sendList |array |Bắt buộc |&nbsp;
&emsp;&emsp;code |string |Bắt buộc |Send code
&emsp;&emsp;status |int |Tùy chọn |Send status (1: normal; 0: failed)
&emsp;&emsp;msg |string |Tùy chọn |Send details


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "sendList":[
        {
          "code": "160139858512181547",
          "status": 1,
          "msg": null
        },
        {
          "code": "160139858512181548",
          "status": 1,
          "msg": null
      }
    ]
  }
}
```

## 7. Hàm API liên quan tới Chiến dịch

### 7.1 Hàm API tạo Chiến dịch
- **Mô tả API:** Tạo thông tin nhiệm vụ chiến dịch và các quy tắc gửi
- **Địa chỉ API:** app/campaign/add

#### 7.1.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;type |int | Bắt buộc |Loại sản phẩm; xem “Phụ lục B” để biết thêm chi tiết
&emsp;templateCode |string | Tùy chọn | Mã mẫu HyperSMS [phiên bản V2.0 hiện không được hỗ trợ]
&emsp;smsTemplateCode |string | Bắt buộc | Mã mẫu SMS template
&emsp;name |string | Bắt buộc | Tên chiến dịch
&emsp;description |string | Tùy chọn | Mô tả chiến dịch
&emsp;property |object | Tùy chọn | Thuộc tính chiến dịch
&emsp;&emsp;startTime |string | Bắt buộc | Thời gian bắt đầu chiến dịch (định dạng yyyy-MM-dd HH: mm: ss)
&emsp;&emsp;endTime |string | Bắt buộc | Thời gian kết thúc chiến dịch (định dạng yyyy-MM-dd HH: mm: ss)
&emsp;&emsp;sendPeriod |array | Tùy chọn | Giới hạn tập hợp các khoảng thời gian gửi
&emsp;&emsp;&emsp;start |string | Bắt buộc | Khoảng thời gian bắt đầu (định dạng HH: mm: ss)
&emsp;&emsp;&emsp;end |string | Bắt buộc | Khoảng thời gian kết thúc (định dạng HH: mm: ss)
&emsp;&emsp;subList |array | Bắt buộc |&nbsp; Lên đến 500 số
&emsp;&emsp;&emsp;subId |string | Bắt buộc | Gửi tới số
&emsp;&emsp;&emsp;encodeType |int | Tùy chọn | Tùy chọn (0: plaintext, 1: mã hóa toán tử bởi Viettel) Giá trị mặc định là 0


Ví dụ về 1 yêu cầu:

``` JSON
{
    "type":2,
    "smsTemplateCode":"001aaab",
    "name":"Campaign Test",
    "description":"description ",
    "property":{
        "startTime": "2021-05-21 12:12:12",
        "endTime": "2021-05-22 12:12:12",
        "sendPeriod":[
            {
                "start":"12:12:12",
                "end":"15:12:12"
            }
        ],
        "subList":[
            {
                "code":"160139858512181547",
                "status":1,
                "msg":null
            },
            {
                "code":"160139858512181548",
                "status":1,
                "msg":null
            }
        ]
    },
    "timestamp":1621503576000,
    "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 7.1.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;
&emsp;code |string |Bắt buộc | Mã chiến dịch


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "code": "1212300"
  }
}
```



### 7.2 Hàm API chỉnh sửa Chiến dịch
- **Mô tả API:** Chỉnh sửa nhiệm vụ chiến dịch và quy tắc gửi
- **Địa chỉ API:** app/campaign/edit

#### 7.2.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;code |string |Bắt buộc |Mã chiến dịch
&emsp;type |int | Tùy chọn |Loại sản phẩm; xem “Phụ lục B” để biết thêm chi tiết
&emsp;templateCode |string | Tùy chọn | Mã mẫu HyperSMS [phiên bản V2.0 hiện không được hỗ trợ]
&emsp;smsTemplateCode |string | Tùy chọn | Mã mẫu SMS template
&emsp;name |string | Tùy chọn | Tên chiến dịch
&emsp;description |string | Tùy chọn | Mô tả chiến dịch
&emsp;property |object | Tùy chọn | Thuộc tính chiến dịch
&emsp;&emsp;startTime |string | Tùy chọn | Thời gian bắt đầu chiến dịch (định dạng yyyy-MM-dd HH: mm: ss)
&emsp;&emsp;endTime |string | Tùy chọn | Thời gian kết thúc chiến dịch (định dạng yyyy-MM-dd HH: mm: ss)
&emsp;&emsp;sendPeriod |array | Tùy chọn | Giới hạn tập hợp các khoảng thời gian gửi
&emsp;&emsp;&emsp;start |string | Tùy chọn | Khoảng thời gian bắt đầu (định dạng HH: mm: ss)
&emsp;&emsp;&emsp;end |string | Tùy chọn | Khoảng thời gian kết thúc (định dạng HH: mm: ss)
&emsp;&emsp;subList |array | Tùy chọn |&nbsp;  Lên đến 500 số
&emsp;&emsp;&emsp;subId |string | Tùy chọn | Gửi tới số
&emsp;&emsp;&emsp;encodeType |int | Tùy chọn | Tùy chọn (0: plaintext, 1: mã hóa toán tử bởi Viettel) Giá trị mặc định là 0


Ví dụ về 1 yêu cầu:

``` JSON
{
    "code":"1212300",
    "type":2,
    "smsTemplateCode":"001aaab",
    "name":"Campaign Test",
    "description":"description ",
    "property":{
        "startTime": "2021-05-21 12:12:12",
        "endTime": "2021-05-22 12:12:12",
        "sendPeriod":[
            {
                "start":"12:12:12",
                "end":"15:12:12"
            }
        ],
        "subList":[
            {
                "code":"160139858512181547",
                "status":1,
                "msg":null
            },
            {
                "code":"160139858512181548",
                "status":1,
                "msg":null
            }
        ]
    },
    "timestamp":1621503576000,
    "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 7.2.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;
&emsp;code |string |Bắt buộc |Mã chiến dịch


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "code": "1212300"
  }
}
```

### 7.3 Hàm API truy vấn Chiến dịch phân trang
- **Mô tả API:** Truy vấn dữ liệu hoạt động theo các điều kiện cụ thể, hỗ trợ phân trang
- **Địa chỉ API:** app/campaign/query

#### 7.3.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;code |string |Tùy chọn |Mã chiến dịch
&emsp;type |int | Tùy chọn |Loại sản phẩm; xem “Phụ lục B” để biết thêm chi tiết
&emsp;templateCode |string | Tùy chọn | Mã mẫu HyperSMS [phiên bản V2.0 hiện không được hỗ trợ]
&emsp;smsTemplateCode |string | Tùy chọn | Mã mẫu SMS template
&emsp;name |string | Tùy chọn | Tên chiến dịch
&emsp;currPage |int | Bắt buộc | Số trang hiện tại, giá trị mặc định là 1
&emsp;pageSize |int | Bắt buộc | Số lượng mục trên mỗi trang, giá trị mặc định là 10


Ví dụ về 1 yêu cầu:

``` JSON
{
  "smsTemplateCode":"",
  "code":"",
  "currPage": 1,
  "pageSize": 1,
  "timestamp":1621503576000,
  "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 7.3.2 Kết quả trả về

Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Response code See: "Appendix A"
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;
&emsp;totalCount |int |Bắt buộc |Số tổng
&emsp;totalPage |int |Bắt buộc |Tổng số trang
&emsp;list |array |Bắt buộc |Kết quả truy vấn
&emsp;&emsp;code |string |Bắt buộc |Mã chiến dịch
&emsp;&emsp;type |int | Bắt buộc |Loại sản phẩm; xem “Phụ lục B” để biết thêm chi tiết
&emsp;&emsp;templateCode |string | Tùy chọn | Mã mẫu HyperSMS [phiên bản V2.0 hiện không được hỗ trợ]
&emsp;&emsp;smsTemplateCode |string | Tùy chọn | Mã mẫu SMS template
&emsp;&emsp;name |string | Bắt buộc | Tên chiến dịch
&emsp;&emsp;description |string | Tùy chọn | Mô tả chiến dịch
&emsp;&emsp;createTime |long | Bắt buộc | Tạo dấu thời gian (mili giây)
&emsp;&emsp;status |int | Bắt buộc | Trạng thái dữ liệu, Xem “Phụ lục D” để biết chi tiết


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
    "totalCount": 100,
    "totalPage": 2,
    "currPage": 1,
    "pageSize": 1,
    "list": [
       {
          "code": "1212300",
          "type": 2,
          "smsTemplateCode": "001aaab",
          "name": "Campaign Test",
          "description": "description ",
          "createTime": 1621503576000,
          "status": 1
       }
    ]
  }
}
```


### 7.4 Hàm API truy vấn chi tiết Chiến dịch 
- **Mô tả API:** Truy vấn chi tiết chiến dịch theo mã chiến dịch, bao gồm thông tin sự kiện cơ bản, thông tin quy tắc, mục tiêu gửi và thông tin phê duyệt
- **Địa chỉ API:** app/campaign/info

#### 7.4.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;code |string |Bắt buộc |Mã chiến dịch


Ví dụ yêu cầu:

``` JSON
{
    "code":"001aaab",
    "timestamp":1621503576000,
    "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```


#### 7.4.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;
&emsp;code |string |Bắt buộc |Mx chiến dịch
&emsp;type |int | Bắt buộc |Loại sản phẩm; xem “Phụ lục B” để biết thêm chi tiết
&emsp;templateCode |string | Tùy chọn | Mã mẫu HyperSMS [phiên bản V2.0 hiện không được hỗ trợ]
&emsp;smsTemplateCode |string | Tùy chọn | Mã mẫu SMS template
&emsp;name |string | Bắt buộc | Tên chiến dịch
&emsp;description |string | Tùy chọn | Mô tả chiến dịch
&emsp;createTime |long | Bắt buộc | Tạo dấu thời gian (mili giây)
&emsp;status |int | Bắt buộc | Trạng tháu dữ liệu chiến dịch, xem "Phụ lục D" để biết chi tiết
&emsp;property |object | Tùy chọn | Thuộc tính chiến dịch
&emsp;&emsp;startTime |string | Tùy chọn | Thời gian bắt đầu chiến dịch (định dạng yyyy-MM-dd HH: mm: ss)
&emsp;&emsp;endTime |string | Tùy chọn | Thời gian kết thúc chiến dịch (định dạng yyyy-MM-dd HH: mm: ss)
&emsp;&emsp;sendPeriod |array | Tùy chọn | Giới hạn tập hợp các khoảng thời gian gửi
&emsp;&emsp;&emsp;start |string | Tùy chọn | Khoảng thời gian bắt đầu (định dạng HH: mm: ss)
&emsp;&emsp;&emsp;end |string | Tùy chọn | Khoảng thời gian kết thúc (định dạng HH: mm: ss)
&emsp;approve |object | Tùy chọn | Thông tin phê duyệt
&emsp;&emsp;status |int | Bắt buộc | Trạng thái phê duyệt, Xem “Phụ lục E” để biết chi tiết
&emsp;&emsp;opinion |string | Tùy chọn | Ý kiến phê duyệt
&emsp;sendList |array |Bắt buộc | Thông tin số gửi tới
&emsp;&emsp;code |string |Bắt buộc | Mã gửi
&emsp;&emsp;status |int |Tùy chọn | Trạng thái gửi (1: bình thường; 0: không thành công)
&emsp;&emsp;msg |string |Tùy chọn | Chi tiết gửi

Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": {
      "code": "1212300",
      "type": 2,
      "smsTemplateCode": "001aaab",
      "name": "Campaign Test",
      "description": "description ",
      "createTime": 1621503576000,
      "status": 1
      "property": {
         "startTime": "2021-05-21 12:12:12",
         "endTime": "2021-05-22 12:12:12",
         "sendPeriod":[
            {
              "start": "12:12:12",
              "end": "15:12:12"
            }
         ]
       },
      "approve": {
         "status": 0,
         "opinion": "pass"
      },
      "sendList":[
         {
           "code": "160139858512181547",
           "status": 1,
           "msg": null
         },
         {
           "code": "160139858512181548",
           "status": 1,
           "msg": null
         }
      ]
  }
}
```

### 7.5 Hàm API vô hiệu hóa Chiến dịch
- **Mô tả API:** Chiến dịch không hợp lệ (Lưu ý: Các chiến dịch đang diễn ra không thể bị vô hiệu)
- **Địa chỉ API:** app/campaign/invalid

#### 7.5.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;code |string | Bắt buộc | Mã chiến dịch


Ví dụ về 1 yêu cầu:

``` JSON
{
    "code":"001aaab",
    "timestamp":1621503576000,
    "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 7.5.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": null
}

```


### 7.6 Hàm API hủy Chiến dịch
- **Mô tả API:** Hủy tác vụ đang hoạt động đang được thực thi
- **Địa chỉ API:** app/campaign/cancel

#### 7.6.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;code |string | Bắt buộc | Mã chiến dịch


Ví dụ về 1 yêu cầu:

``` JSON
{
    "code":"001aaab",
    "timestamp":1621503576000,
    "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 7.6.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": null
}

```


### 7.7 Hàm API tạm dừng nhiệm vụ của Chiến dịch 
- **Mô tả API:** Tạm dừng nhiệm vụ Chiến dịch đang được thực thi
- **Địa chỉ API:** app/campaign/pause

#### 7.7.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;code |string | Bắt buộc | Mã chiến dịch


Ví dụ về 1 yêu cầu:

``` JSON
{
    "code":"001aaab",
    "timestamp":1621503576000,
    "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 7.7.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": null
}

```



### 7.8 Hàm API tiếp tục nhiệm vụ Chiến dịch
- **Mô tả API:** Tiếp tục các nhiệm vụ chiến dịch đang bị tạm ngưng
- **Địa chỉ API:** app/campaign/resume

#### 7.8.1 Các tham số của yêu cầu
  
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;code |string | Bắt buộc | Mã chiến dịch


Ví dụ về 1 yêu cầu:

``` JSON
{
    "code":"001aaab",
    "timestamp":1621503576000,
    "sign":"600e5d07f445be432d86d9271fb1df9b1"
}

```

#### 7.8.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;
data |object |Bắt buộc |&nbsp;


Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
  "data": null
}

```



## 8. API liên quan tới Callback

### 8.1 API gửi lại trạng thái phê duyệt
- **Mô tả API:** Dữ liệu cần được phê duyệt, khi trạng thái phê duyệt thay đổi, lệnh gọi lại sẽ được nhận thông qua callbackApproveUrl do bên truy cập cung cấp
- **Địa chỉ API:** Được cung cấp do bên truy cập

#### 8.1.1 Các tham số của yêu cầu
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;type |int | Bắt buộc | Callback type 1: Brandname 2: Template 3: Campaign
&emsp;code |string | Bắt buộc | Callback code
&emsp;approve |object | Bắt buộc | Callback review information
&emsp;&emsp;status |int | Bắt buộc | Trạng thái phê duyệt, Xem “Phụ lục E” để biết chi tiết
&emsp;&emsp;opinion |string | Tùy chọn | Ý kiến phê duyệt


Ví dụ về 1 yêu cầu:

``` JSON
{
  "sign": "afsdfasdf2asdfasdf",
  "timestamp": 1621503576000,
  "type": 1,
  "code":"acb000000",
  "approve": {
     "status": 1,
     "opinion": "pass"
  }
}

```


#### 8.1.2 Kết quả trả về

Tên tham số | Loại | Bắt buộc hay không | Mô tả
:---- |:--- |:------ |:---
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg |string |Bắt buộc |&nbsp;

Ví dụ:

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
}
```



### 8.2 API gọi lại trạng thái Gửi và Tải xuống
- **Mô tả API:** Sau khi tác vụ gửi được thực thi, trạng thái gửi và tải xuống sẽ được gọi lại thông qua callbackSendUrl do bên truy cập chỉ định
- **Địa chỉ API:** Được cung cấp do bên truy cập

#### 8.2.1 Các tham số của yêu cầu
Tên tham số |Loại |Bắt buộc hay không |Mô tả
:---- |:--- |:------ |:---
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp |long |Bắt buộc |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;sign |string | Tùy chọn | Chữ ký, Xem “Phụ lục G” để biết chi tiết
timestamp	 |long	|Bắt buộc   |Dấu thời gian UNIX hiện tại (mili giây)
&emsp;type				|int		| Bắt buộc	| Loại call back: 0: DR 1: Truy xuất
&emsp;code			|string		| Bắt buộc | Mã callback
&emsp;status			|int		| Bắt buộc | Trạng thái nhận, Xem “Phụ lục F” để biết thêm chi tiết


Ví dụ về 1 yêu cầu:

``` JSON
{
  "sign": "afsdfasdf2asdfasdf",
  "timestamp": 1621503576000,
  "type": 1,
  "code":"acb000000",
  "status": 1
}

```


#### 8.2.2 Kết quả trả về

Tên tham số						|Loại		|Bắt buộc hay không	|Mô tả 
:----						|:---		|:------	|:---	
code |int |Bắt buộc |Mã trả về, xem “Phụ lục A” để biết chi tiết
msg							|string		|Bắt buộc		|&nbsp;

Ví dụ：

``` JSON
{
  "code": 0,
  "msg": "api.response.code.success",
  "msgArgs": null,
}
```



## Phụ lục

### Phụ lục A Mô tả mã phản hồi

Code	|Msg        |Remarks
:----	|:---                  |:---
0		|api.response.code.success   |Yêu cầu thành công
&emsp;  |&emsp;                    |&emsp;
-1		|api.response.code.fail  |Yêu cầu không thành công
-1		|api.response.code.illegal | Yêu cầu không hợp lệ
-1		|api.response.code.unknownError | Ngoại lệ nội tại máy chủ
-1		|api.response.code.paramSignError | Lỗi chữ ký
&emsp;  |&emsp;                    |&emsp;
100		|api.response.code.paramError    | Lỗi tham số
100		|api.response.code.paramTypeError | Lỗi loại tham số
100		|api.response.code.paramNotEmpty  | Tham số trống
100		|api.response.code.paramTokenInvalid  | Mã thông số không hợp lệ
&emsp;  |&emsp;                    |&emsp;
200		|api.response.code.user.accountNotExist | appId hoặc appSecret không thành công
200		|api.response.code.user.accountHasLocked | Tài khoản bị khóa

### Phụ lục B: Mô tả mã loại sản phẩm

Code		|Ghi chú
:----	|:---
1		| HyperSMS
2		| SMS

Lưu ý: v2.0 chỉ hỗ trợ SMS

### Phụ lục C: Mô tả mã hàng sản phẩm

Code	|Type		|catid  	|Group Name | Ghi chú 
:----	|:--- |:--- |:---|:---
SMS_AD_GROUP_1_EDU	|	Advertisement	|	SMS_EDU_QC	|	Group 1	|	Tuyển dụng, tuyển sinh, giáo dục
SMS_AD_GROUP_2_BDS	|	Advertisement	|	SMS_BDS_QC	|	Group 2	|	Bất động sản
SMS_AD_GROUP_3_COS	|	Advertisement	|	SMS_COS_QC	|	Group 3	|	Hóa mỹ phẩm, làm đẹp
SMS_AD_GROUP_3_GTRI	|	Advertisement	|	SMS_GTRI_QC	|	Group 3	|	Giải trí
SMS_AD_GROUP_3_FSION	|	Advertisement	|	SMS_FSION_QC	|	Group 3	|	Thời trang
SMS_AD_GROUP_3_FOOD	|	Advertisement	|	SMS_FOOD_QC	|	Group 3	|	Thực phẩm, đồ uống, y tế - dược
SMS_AD_GROUP_3_MART	|	Advertisement	|	SMS_MART_QC	|	Group 3	|	Siêu thị, Trung tâm thương mại
SMS_AD_GROUP_3_TRAN	|	Advertisement	|	SMS_TRAN_QC	|	Group 3	|	Vận tải, vận chuyển
SMS_AD_GROUP_3_FIN	|	Advertisement	|	SMS_FIN_QC	|	Group 3	|	Tài chính ngân hàng
SMS_AD_GROUP_3_TMDT	|	Advertisement	|	SMS_TMDT_QC	|	Group 3	|	Thương mại điện tử
SMS_AD_GROUP_3_DL	|	Advertisement	|	SMS_DL_QC	|	Group 3	|	Du lịch
SMS_AD_GROUP_3_ELEC	|	Advertisement	|	SMS_ELEC_QC	|	Group 3	|	Điện, nước, xăng dầu
SMS_AD_GROUP_4_OTHER	|	Advertisement	|	SMS_OTHER_QC	|	Group 4	|	Nhóm thuộc lĩnh vực khác
SMS_CSKH_GROUP_1_FIN	|	Customer Care	|	SMS_FIN_CSKH	|	Group 1	|	Nhắn tin thuộc lĩnh vực chứng khoán, tài chính.
SMS_CSKH_GROUP_2.2_HCHINH	|	Customer Care	|	SMS_HCHINH_CKSH	|	Group 2.2	|	lực lượng vũ trang, hành chính công, đơn vị sự nghiệp, đoàn thể.
SMS_CSKH_GROUP_KCN_INDUS	|	Customer Care	|	SMS_INDUS_CSKH	|	Group KCN	|	doanh nghiệp thuộc khu công nghiệp
SMS_CSKH_GROUP_3.3_OTTIN	|	Customer Care	|	SMS_OTTIN_CSKH	|	Group 3.3	|	lĩnh vực OTT, mạng xã hội quốc tế
SMS_CSKH_GROUP_3.4_OTTL	|	Customer Care	|	SMS_OTTL_CSKH	|	Group 3.4	|	lĩnh vực OTT, mạng xã hội trong nước
SMS_CSKH_GROUP_4_EWP	|	Customer Care	|	SMS_EWP_CSKH	|	Group 4	|	ngành điện, ngành nước, ngành xăng dầu
SMS_CSKH_GROUP_5_TMDT	|	Customer Care	|	SMS_TMDT_CSKH	|	Group 5	|	Thương mại điện tử
SMS_CSKH_GROUP_6_INVOICE	|	Customer Care	|	SMS_INVOICE_CSKKH	|	Group 6	|	Hóa đơn điện tử/ Thanh toán điện tử/ Xác thực điện tử (qua QR code, mã vạch)
SMS_CSKH_GROUP_2.1_EDU	|	Customer Care	|	SMS_EDU_CKSH	|	Group 2.1	|	Lĩnh vực giáo dục
SMS_CSKH_GROUP_1_BHIEM	|	Customer Care	|	SMS_BHIEM_CSKH	|	Group 1	|	Lĩnh vực bảo hiểm
SMS_CSKH_GROUP_2.1_YTE	|	Customer Care	|	SMS_YTE_CSKH	|	Group 2.1	|	Lĩnh vực y tế


### Phụ lục D: Mô tả mã trạng thái dữ liệu
Mã		|Ghi chú  
:----	|:---
0		| invalid
1		| valid

### Appendix E: Approve status code description
Mã		|Ghi chú  
:----	|:---
0		| Từ chối
1		| Đã duyệt
2		| Chờ phê duyệt

### Phụ lục F mô tả về mã trạng thái gửi

Loại |Mã	|Ghi chú 
:---- |:----	|:---
DR |1		| Thành công
DR |2		| Thất bại
DR |3		| Đang xử lý
Retrieve |1		| Thành công
Retrieve |2		| Thất bại
Retrieve |3		| Đang xử lý

### Phụ lục G quy tắc chữ ký

- Trước khi người gọi bắt đầu yêu cầu, hãy tạo cặp khóa-giá trị của các tham số yêu cầu
- Sắp xếp theo khóa tham số (từ điển tăng dần)
- Ghép bộ tham số ở định dạng “& key = value” và thêm appKey
- Cuối cùng nhận giá trị dấu thông qua MD5


Ví dụ Mã(java)：
``` JAVA
public static String createSign(Map<String, String> params, String appKey){
    StringBuilder sb = new StringBuilder();
    Map<String, String> sortParams = new TreeMap<>(params);
    for (Map.Entry<String, String> entry : sortParams.entrySet()) {
        String key = entry.getKey();
        if(StringUtils.isBlank(entry.getValue())){
            continue;
        }
        String value = entry.getValue().trim();
        if (StringUtils.isNotEmpty(value)){
            sb.append("&").append(key).append("=").append(value);
        }
    }
    String stringA = sb.toString().replaceFirst("&","");
    String stringSignTemp = stringA + appKey;
    String signValue = Md5Encrypt.md5(stringSignTemp);
    return signValue;
}

```

Ví dụ Mã(js)：
``` JAVASCRIPT
function(rawData, appKey){
    var rawJSON = JSON.parse(rawData);
    delete rawJSON["sign"]

    rawJSON["timestamp"] = 'set timestamp';
    let arr=[];
    for(var key in rawJSON){
        arr.push(key)
    }
    arr.sort();

    let str='';
    for(var i in arr){
        if(!rawJSON[arr[i]]){
            continue;
        }
        str +=arr[i]+"="+rawJSON[arr[i]]+"&";
    }
    str = str.substr(0, str.length - 1) + appKey;
    //md5
    var sign = CryptoJS.MD5(str);
    rawJSON["sign"] = sign+"";
    return rawJSON;
}
``` 


### Phục lục H quy tắc về ký tự đặt biết và vấn đề chú ý(V2.3.2.brand)
1. Nội dung đầu vào nghiêm cấm sử dụng các từ ngữ vi phạm luật và quy định của quốc gia có liên quan.
2. Các ký tự đặc biệt mà không cần escape có thể sử dụng như sau: ? /n ! # $ % ( ) * + , - . / : ; = @ [ \ ] _ | } { ~ £ ¥ § ¿ ¤ Ä Å Æ Ç É Ñ Ø ø Ü ß Ö à ä å æ è é ì ñ ò ö ù ü Δ Φ Γ Λ Ω Π Ψ Σ Θ Ξ
3. Các ký tự đặc biệt mà cần escape trước có thể sử dụng như sau: &amp;(&amp;amp;) &gt;(&amp;gt;) &quot;(&amp;quot;) &#x27;(&amp;#x27;) &lt;(&amp;lt;)