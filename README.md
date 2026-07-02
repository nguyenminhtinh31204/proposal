# 🧾 Serverless AI Invoice Scanner – Giải pháp tự động hóa trích xuất và quản lý hóa đơn bằng AI trên nền tảng AWS

> 🔍 *Một hệ thống thông minh giúp người dùng tải lên hóa đơn dạng ảnh hoặc PDF, tự động trích xuất nội dung bằng Amazon Textract, chuẩn hóa dữ liệu bằng OpenAI API, lưu trữ thông tin có cấu trúc vào Amazon DynamoDB và quản lý hóa đơn thông qua giao diện web React được triển khai bằng AWS Amplify Hosting.*

---

## 📝 Tóm tắt dự án

Trong bối cảnh chuyển đổi số diễn ra mạnh mẽ, việc tự động hóa quy trình xử lý hóa đơn đầu vào đang trở thành nhu cầu thiết yếu của nhiều doanh nghiệp. Hóa đơn thường tồn tại dưới nhiều định dạng như PDF, ảnh chụp, bản scan hoặc tài liệu không có cấu trúc. Điều này gây khó khăn cho việc trích xuất, kiểm tra, lưu trữ và truy xuất dữ liệu.

**Serverless AI Invoice Scanner** được xây dựng nhằm giải quyết bài toán này thông qua một hệ thống xử lý hóa đơn tự động dựa trên trí tuệ nhân tạo và kiến trúc serverless trên AWS. Hệ thống cho phép người dùng tải lên hóa đơn, sử dụng **Amazon Textract** để trích xuất văn bản, sau đó dùng **OpenAI API** để phân tích và chuẩn hóa dữ liệu hóa đơn thành cấu trúc rõ ràng như mã hóa đơn, tên khách hàng, ngày hóa đơn, tổng tiền, loại tiền tệ và các thông tin liên quan.

Dữ liệu sau khi xử lý được lưu vào **Amazon DynamoDB**, cho phép truy xuất nhanh thông qua các API được triển khai bằng **Amazon API Gateway** và **AWS Lambda**. Phần giao diện người dùng được xây dựng bằng **ReactJS**, triển khai bằng **AWS Amplify Hosting** và tích hợp **Amazon Cognito** để hỗ trợ đăng ký, đăng nhập người dùng.

{{% notice info %}}
Trong phiên bản hiện tại của project, hệ thống sử dụng **OpenAI API** thay cho Amazon Bedrock. OpenAI API là dịch vụ bên ngoài AWS, vì vậy API key cần được lưu an toàn ở backend, ví dụ trong Lambda environment variables hoặc AWS Secrets Manager.
{{% /notice %}}

### 🎯 Lợi ích nổi bật

- **Tự động hóa quy trình nhập liệu**: Giảm thời gian đọc và nhập hóa đơn thủ công.
- **Tăng độ chính xác**: Kết hợp OCR của Amazon Textract và khả năng phân tích ngữ nghĩa của OpenAI API.
- **Dễ truy xuất dữ liệu**: Hóa đơn được lưu dưới dạng dữ liệu có cấu trúc trong DynamoDB.
- **Quản lý trực quan**: Frontend hỗ trợ xem danh sách, xem chi tiết, tìm kiếm, gắn tags, đánh dấu starred và export Excel.
- **Bảo mật người dùng**: Tích hợp Amazon Cognito cho đăng ký và đăng nhập.
- **Dễ mở rộng**: Kiến trúc serverless giúp hệ thống tự động scale theo số lượng request.

---

## ❗ 1. Tuyên bố vấn đề

### 🧾 Tình hình hiện tại

Nhiều doanh nghiệp, đặc biệt là doanh nghiệp vừa và nhỏ, vẫn xử lý hóa đơn đầu vào theo cách thủ công hoặc bán tự động. Nhân viên kế toán thường phải mở từng file hóa đơn, đọc các trường quan trọng như số hóa đơn, ngày phát hành, tên khách hàng, tổng tiền, thuế và đơn vị bán hàng, sau đó nhập lại vào phần mềm quản lý.

Quy trình này có một số hạn chế:

- Tốn nhiều thời gian khi số lượng hóa đơn tăng.
- Dễ xảy ra lỗi nhập liệu do con người.
- Khó tìm kiếm dữ liệu khi hóa đơn chỉ được lưu dưới dạng file ảnh hoặc PDF.
- Khó thống kê, đối chiếu và kiểm toán.
- Khó tích hợp với hệ thống quản lý hoặc báo cáo dữ liệu.

➡️ **Nhu cầu đặt ra** là xây dựng một hệ thống có thể tiếp nhận hóa đơn đa định dạng, tự động trích xuất nội dung, chuẩn hóa dữ liệu bằng AI, lưu trữ tập trung và hỗ trợ truy xuất nhanh.

### ⚠️ Những thách thức chính

1. **Định dạng hóa đơn đa dạng**  
   Hóa đơn có thể là PDF, ảnh chụp, bản scan hoặc hình ảnh có chất lượng khác nhau.

2. **Dữ liệu không có cấu trúc**  
   Nội dung hóa đơn thường không theo cùng một mẫu cố định, gây khó khăn cho việc trích xuất thông tin.

3. **Sai sót khi nhập liệu thủ công**  
   Con người có thể nhập sai số tiền, ngày tháng, mã hóa đơn hoặc tên khách hàng.

4. **Khó tìm kiếm và phân loại**  
   Nếu chỉ lưu hóa đơn dưới dạng file, việc tìm theo khách hàng, ngày, số tiền hoặc trạng thái xử lý sẽ khó khăn.

5. **Yêu cầu bảo mật dữ liệu**  
   Hóa đơn có thể chứa thông tin tài chính hoặc thông tin doanh nghiệp quan trọng, cần được bảo vệ khi lưu trữ và truyền tải.

6. **Nhu cầu mở rộng hệ thống**  
   Khi số lượng người dùng hoặc số lượng hóa đơn tăng, hệ thống cần có khả năng mở rộng mà không cần quản lý server thủ công.

### 👥 Tác động đến các bên liên quan

| Bên liên quan | Tác động |
|---|---|
| **Kế toán – Tài chính** | Giảm công việc nhập liệu lặp lại, tiết kiệm thời gian xử lý hóa đơn. |
| **Quản lý doanh nghiệp** | Có dữ liệu hóa đơn tập trung, dễ kiểm tra và theo dõi. |
| **Kiểm toán nội bộ** | Dễ truy xuất hóa đơn và dữ liệu liên quan khi cần đối chiếu. |
| **Bộ phận CNTT** | Có hệ thống serverless dễ triển khai, dễ bảo trì, không cần vận hành máy chủ. |
| **Người dùng cuối** | Có giao diện trực quan để upload, xem, tìm kiếm và quản lý hóa đơn. |

### 💥 Hệ quả nếu không giải quyết

- Tăng chi phí vận hành do phụ thuộc nhiều vào nhập liệu thủ công.
- Dễ phát sinh sai lệch dữ liệu kế toán.
- Khó tìm kiếm hóa đơn cũ khi cần đối chiếu.
- Chậm trễ trong quy trình kiểm tra và phê duyệt.
- Khó mở rộng khi số lượng hóa đơn tăng cao.
- Cản trở quá trình chuyển đổi số của doanh nghiệp.

---

## 🏗️ 2. Kiến trúc giải pháp

### 🧩 Tổng quan kiến trúc

Hệ thống **Serverless AI Invoice Scanner** được xây dựng theo kiến trúc serverless và event-driven trên AWS. Luồng xử lý chính gồm:

```txt
React Frontend
    ↓
Amazon Cognito
    ↓
Amazon API Gateway
    ↓
UploadInvoiceFileFunction
    ↓
Amazon S3 /uploads
    ↓
S3 Event Trigger
    ↓
ProcessInvoiceFunction
    ↓
Amazon Textract
    ↓
OpenAI API
    ↓
Amazon DynamoDB
    ↓
InvoiceManagementFunction
    ↓
API Gateway
    ↓
React Frontend
```

Kiến trúc này giúp tách biệt rõ các chức năng:

- Frontend dùng để tương tác với người dùng.
- API Gateway đóng vai trò điểm vào của backend.
- Lambda xử lý nghiệp vụ.
- S3 lưu file hóa đơn gốc.
- Textract trích xuất văn bản.
- OpenAI API chuẩn hóa dữ liệu.
- DynamoDB lưu dữ liệu hóa đơn.
- CloudWatch hỗ trợ theo dõi log và debug.

### 🎯 Mục tiêu thiết kế kiến trúc

- Tự động hóa quy trình xử lý hóa đơn từ upload đến lưu trữ.
- Không cần quản lý server vật lý hoặc máy chủ EC2.
- Tách riêng frontend, API, xử lý AI và lưu trữ dữ liệu.
- Dễ mở rộng theo số lượng request và số lượng hóa đơn.
- Dễ kiểm thử bằng Postman, AWS Console và frontend React.
- Có khả năng bổ sung các chức năng nâng cao như tags, starred, tìm kiếm và export Excel.

---

## 🔄 3. Luồng xử lý dữ liệu

### 3.1. Luồng đăng nhập người dùng

1. Người dùng truy cập frontend được deploy bằng **AWS Amplify Hosting**.
2. Frontend sử dụng **Amazon Cognito User Pool** để hỗ trợ đăng ký và đăng nhập.
3. Sau khi đăng nhập thành công, người dùng có thể sử dụng các chức năng của hệ thống như upload hóa đơn, xem danh sách và tìm kiếm hóa đơn.

{{% notice info %}}
Trong project hiện tại, Cognito được dùng chủ yếu cho frontend authentication. API Gateway chỉ được bảo vệ bằng Cognito nếu có cấu hình Cognito Authorizer riêng.
{{% /notice %}}

### 3.2. Luồng upload hóa đơn

1. Người dùng chọn file hóa đơn từ giao diện React.
2. Frontend chuyển file sang Base64.
3. Frontend gửi request đến API Gateway:

```txt
POST /uploads
```

4. API Gateway chuyển request đến Lambda:

```txt
UploadInvoiceFileFunction
```

5. Lambda decode nội dung Base64 và upload file vào Amazon S3, thường trong thư mục:

```txt
uploads/
```

6. Sau khi file được lưu vào S3, S3 Event Notification kích hoạt Lambda xử lý hóa đơn.

### 3.3. Luồng xử lý hóa đơn bằng AI

1. **ProcessInvoiceFunction** được kích hoạt khi có file mới trong S3.
2. Lambda đọc file từ S3.
3. Lambda gọi **Amazon Textract** để trích xuất văn bản từ hóa đơn.
4. Kết quả OCR được gửi đến **OpenAI API** để phân tích và chuẩn hóa.
5. Dữ liệu sau khi chuẩn hóa được lưu vào DynamoDB table:

```txt
InvoiceData
```

Dữ liệu có thể bao gồm:

| Trường dữ liệu | Mô tả |
|---|---|
| `InvoiceId` | Mã định danh duy nhất của hóa đơn. |
| `CustomerName` | Tên khách hàng hoặc đơn vị liên quan. |
| `InvoiceNumber` | Số hóa đơn. |
| `InvoiceDate` | Ngày phát hành hóa đơn. |
| `TotalAmount` | Tổng tiền trên hóa đơn. |
| `Currency` | Loại tiền tệ như `VND`, `USD`, `EUR`. |
| `Tags` | Danh sách nhãn người dùng gắn cho hóa đơn. |
| `Starred` | Trạng thái đánh dấu hóa đơn quan trọng. |
| `ProcessStatus` | Trạng thái xử lý, ví dụ `SUCCESS` hoặc `FAILED`. |
| `ExtractedData` | Dữ liệu hóa đơn đã được trích xuất và chuẩn hóa. |

### 3.4. Luồng truy xuất và quản lý hóa đơn

Người dùng có thể gọi các API thông qua frontend hoặc Postman:

```txt
GET /invoice
GET /invoice/{id}
GET /invoice?name=<customer_name>
PATCH /invoice/tags/{id}
PATCH /invoice/starred/{id}
```

Các request này được API Gateway chuyển đến:

```txt
InvoiceManagementFunction
```

Lambda này truy vấn hoặc cập nhật dữ liệu trong DynamoDB, sau đó trả kết quả về frontend.

---

## 🧱 4. Các khối chức năng chính

### 4.1. Frontend Layer

Frontend được xây dựng bằng **ReactJS** và deploy bằng **AWS Amplify Hosting**. Giao diện hỗ trợ:

- Đăng ký và đăng nhập bằng Cognito.
- Upload hóa đơn bằng nút chọn file hoặc drag-and-drop.
- Xem danh sách hóa đơn đã xử lý.
- Xem chi tiết hóa đơn.
- Tìm kiếm hóa đơn theo ID hoặc tên khách hàng.
- Lọc theo tags.
- Lọc theo ngày.
- Sắp xếp theo ngày hoặc tổng tiền.
- Gắn tags cho hóa đơn.
- Đánh dấu hóa đơn quan trọng bằng starred.
- Export dữ liệu hóa đơn ra file Excel.
- Hiển thị trạng thái kết nối Cognito, API upload và API invoice.

### 4.2. Authentication Layer

Amazon Cognito cung cấp chức năng:

- User Pool.
- Sign up.
- Sign in.
- Sign out.
- Quản lý session người dùng.
- Tích hợp với frontend thông qua `aws-amplify` và `@aws-amplify/ui-react`.

### 4.3. API Gateway Layer

API Gateway cung cấp REST API cho frontend và Postman.

Các API chính:

| Method | Route | Chức năng |
|---|---|---|
| `POST` | `/uploads` | Upload file hóa đơn. |
| `GET` | `/invoice` | Lấy danh sách hóa đơn. |
| `GET` | `/invoice/{id}` | Lấy chi tiết hóa đơn theo ID. |
| `GET` | `/invoice?name=<customer_name>` | Tìm kiếm hóa đơn theo tên khách hàng. |
| `PATCH` | `/invoice/tags/{id}` | Cập nhật tags của hóa đơn. |
| `PATCH` | `/invoice/starred/{id}` | Cập nhật trạng thái starred. |

### 4.4. Lambda Layer

Hệ thống sử dụng ba Lambda functions chính:

| Lambda Function | Vai trò |
|---|---|
| `UploadInvoiceFileFunction` | Nhận file Base64 từ frontend, decode và upload vào S3. |
| `ProcessInvoiceFunction` | Xử lý file mới từ S3, gọi Textract, gọi OpenAI API và lưu dữ liệu vào DynamoDB. |
| `InvoiceManagementFunction` | Xử lý các API lấy danh sách, xem chi tiết, tìm kiếm, cập nhật tags và starred. |

### 4.5. AI Processing Layer

AI Processing Layer gồm hai thành phần:

| Thành phần | Vai trò |
|---|---|
| Amazon Textract | OCR và trích xuất văn bản từ hóa đơn. |
| OpenAI API | Phân tích nội dung OCR và chuẩn hóa thành JSON có cấu trúc. |

Ví dụ dữ liệu sau chuẩn hóa:

```json
{
  "InvoiceNumber": "INV-001",
  "CustomerName": "John Smith",
  "InvoiceDate": "2025-06-20",
  "TotalAmount": 125.50,
  "Currency": "USD"
}
```

### 4.6. Storage Layer

Hệ thống sử dụng hai loại lưu trữ:

| Dịch vụ | Mục đích |
|---|---|
| Amazon S3 | Lưu file hóa đơn gốc. |
| Amazon DynamoDB | Lưu dữ liệu hóa đơn đã xử lý. |

DynamoDB table chính:

```txt
InvoiceData
```

Các index có thể sử dụng:

```txt
CustomerName-index
StarredInvoicesIndex
```

### 4.7. Monitoring Layer

Amazon CloudWatch được sử dụng để:

- Theo dõi log của Lambda.
- Debug lỗi upload.
- Debug lỗi Textract.
- Debug lỗi OpenAI API.
- Debug lỗi DynamoDB.
- Kiểm tra lỗi CORS hoặc API Gateway.
- Theo dõi trạng thái xử lý hóa đơn.

---

## 🧰 5. Các dịch vụ sử dụng

| Dịch vụ | Vai trò trong project |
|---|---|
| **AWS Amplify Hosting** | Deploy frontend React từ GitHub. |
| **Amazon Cognito** | Xác thực người dùng cho frontend. |
| **Amazon API Gateway** | Cung cấp các REST API endpoint. |
| **AWS Lambda** | Xử lý backend logic theo mô hình serverless. |
| **Amazon S3** | Lưu trữ file hóa đơn gốc trong thư mục `uploads/`. |
| **Amazon Textract** | Trích xuất văn bản và dữ liệu từ file hóa đơn. |
| **OpenAI API** | Chuẩn hóa dữ liệu OCR thành JSON có cấu trúc. |
| **Amazon DynamoDB** | Lưu dữ liệu hóa đơn đã xử lý. |
| **Amazon CloudWatch** | Ghi log và hỗ trợ giám sát hệ thống. |
| **AWS IAM** | Cấp quyền cho Lambda truy cập S3, Textract, DynamoDB và CloudWatch. |
| **AWS Secrets Manager** *(optional)* | Lưu OpenAI API key an toàn. |
| **Route 53** *(optional)* | Cấu hình custom domain nếu cần. |

{{% notice warning %}}
Route 53 là thành phần tùy chọn. Nếu project không sử dụng custom domain, không cần đưa Route 53 vào phần triển khai bắt buộc.
{{% /notice %}}

---

## 🔐 6. Kiến trúc bảo mật

### 6.1. Xác thực người dùng

- Người dùng đăng nhập thông qua Amazon Cognito.
- Frontend lưu session thông qua thư viện AWS Amplify.
- Các thông tin như User Pool ID, App Client ID và Region được cấu hình trong frontend.

### 6.2. Bảo vệ API

API Gateway có thể được bảo vệ bằng Cognito Authorizer nếu triển khai ở mức production. Trong bản demo hoặc MVP, API có thể chưa bật authorizer để đơn giản hóa kiểm thử.

{{% notice info %}}
Nếu bật Cognito Authorizer, frontend cần gửi Authorization header và API Gateway CORS phải cho phép header `Authorization`.
{{% /notice %}}

### 6.3. Bảo vệ dữ liệu

- S3 nên bật block public access.
- DynamoDB không public trực tiếp ra internet.
- Lambda sử dụng IAM Role với quyền tối thiểu.
- OpenAI API key không được hardcode trong frontend.
- Không log toàn bộ request nếu request chứa token hoặc dữ liệu nhạy cảm.

### 6.4. Bảo vệ vận hành

- CloudWatch dùng để theo dõi lỗi.
- IAM Role chỉ cấp quyền cần thiết.
- Có thể dùng AWS Secrets Manager để quản lý secret.
- Có thể bổ sung CloudTrail hoặc WAF nếu đưa vào môi trường production.

---

## ⚙️ 7. Triển khai kỹ thuật

### 📌 Các giai đoạn triển khai

#### Giai đoạn 1: Chuẩn bị môi trường AWS

- Tạo S3 bucket để lưu hóa đơn.
- Tạo DynamoDB table `InvoiceData`.
- Tạo Cognito User Pool.
- Tạo Lambda functions.
- Cấu hình IAM Roles.
- Cấu hình API Gateway routes.
- Chuẩn bị OpenAI API key ở backend.

#### Giai đoạn 2: Xây dựng upload flow

- Tạo `POST /uploads` trên API Gateway.
- Tạo `UploadInvoiceFileFunction`.
- Frontend chuyển file sang Base64.
- Lambda decode Base64 và upload file vào S3.
- Kiểm thử upload bằng frontend và Postman.

#### Giai đoạn 3: Xây dựng AI processing flow

- Cấu hình S3 Event Notification.
- Tạo `ProcessInvoiceFunction`.
- Lambda gọi Amazon Textract để OCR.
- Lambda gửi kết quả OCR sang OpenAI API.
- Lambda lưu dữ liệu chuẩn hóa vào DynamoDB.
- Ghi log xử lý vào CloudWatch.

#### Giai đoạn 4: Xây dựng invoice management API

- Tạo `InvoiceManagementFunction`.
- Tạo các API routes:
  - `GET /invoice`
  - `GET /invoice/{id}`
  - `GET /invoice?name=<customer_name>`
  - `PATCH /invoice/tags/{id}`
  - `PATCH /invoice/starred/{id}`
- Kiểm thử API bằng Postman.
- Chuẩn hóa dữ liệu trả về cho frontend.

#### Giai đoạn 5: Xây dựng frontend

- Xây dựng React app.
- Tích hợp Cognito bằng `aws-amplify`.
- Tích hợp API Gateway endpoints bằng `.env`.
- Tạo giao diện upload, danh sách, chi tiết, search, tags, starred, sort, filter và export Excel.
- Deploy frontend bằng AWS Amplify Hosting.

#### Giai đoạn 6: Kiểm thử và hoàn thiện

- Kiểm thử upload hóa đơn.
- Kiểm thử lấy danh sách hóa đơn.
- Kiểm thử tìm kiếm theo ID và tên khách hàng.
- Kiểm thử cập nhật tags và starred.
- Kiểm thử CORS.
- Kiểm tra CloudWatch Logs.
- Chỉnh sửa lỗi dữ liệu như `Tags/tags`, `Starred/IsStarred`, `CustomerName` top-level hoặc trong `ExtractedData`.

---

## 🧪 8. Chiến lược kiểm thử

### 8.1. Kiểm thử chức năng

| Chức năng | Kết quả mong đợi |
|---|---|
| Upload hóa đơn | File được lưu vào S3. |
| S3 trigger | Lambda xử lý được kích hoạt. |
| Textract OCR | Trích xuất được văn bản từ hóa đơn. |
| OpenAI normalization | Trả về JSON hóa đơn có cấu trúc. |
| Lưu DynamoDB | Item được lưu vào table `InvoiceData`. |
| GET `/invoice` | Trả về danh sách hóa đơn. |
| GET `/invoice/{id}` | Trả về chi tiết hóa đơn. |
| GET `/invoice?name=` | Tìm được hóa đơn theo khách hàng. |
| PATCH tags | Cập nhật tags thành công. |
| PATCH starred | Cập nhật trạng thái starred thành công. |

### 8.2. Kiểm thử bảo mật

- Kiểm tra CORS.
- Kiểm tra việc không lộ OpenAI API key ở frontend.
- Kiểm tra IAM Role của Lambda.
- Kiểm tra S3 bucket không public.
- Kiểm tra Cognito login/logout.
- Nếu dùng Cognito Authorizer, kiểm tra request có và không có token.

### 8.3. Kiểm thử lỗi

| Trường hợp lỗi | Cách xử lý |
|---|---|
| File không hợp lệ | Frontend hiển thị thông báo lỗi. |
| API Gateway sai route | Kiểm tra lỗi `Missing Authentication Token`. |
| Lambda thiếu biến môi trường | Kiểm tra CloudWatch Logs. |
| DynamoDB không có item | Trả về lỗi 404 phù hợp. |
| Textract lỗi | Ghi `ProcessStatus = FAILED`. |
| OpenAI API lỗi | Ghi log và trả trạng thái xử lý thất bại. |

---

## 🗺️ 9. Lộ trình và cột mốc

### 📆 Lộ trình dự án

| Tuần | Giai đoạn | Mục tiêu |
|---|---|---|
| Tuần 1 | Thiết kế kiến trúc và setup AWS | Tạo S3, DynamoDB, Lambda, Cognito, API Gateway. |
| Tuần 2 | Upload flow | Frontend upload file qua API Gateway đến S3. |
| Tuần 3 | AI processing flow | S3 trigger Lambda, Textract OCR, OpenAI API normalization. |
| Tuần 4 | Invoice management API | GET/PATCH APIs, DynamoDB query/update. |
| Tuần 5 | Frontend hoàn chỉnh | Upload, list, detail, search, tags, starred, export Excel. |
| Tuần 6 | Kiểm thử và deploy | Postman test, CloudWatch debug, deploy Amplify Hosting. |

### 📌 Cột mốc quan trọng

| STT | Cột mốc | Kết quả đầu ra |
|---|---|---|
| 1 | Hoàn thiện sơ đồ kiến trúc | Sơ đồ đúng luồng AWS + OpenAI API. |
| 2 | Upload hóa đơn thành công | File được lưu trong S3 `uploads/`. |
| 3 | Xử lý hóa đơn thành công | Textract + OpenAI trả dữ liệu có cấu trúc. |
| 4 | Lưu DynamoDB thành công | Item xuất hiện trong `InvoiceData`. |
| 5 | API truy xuất hoạt động | GET và PATCH hoạt động bằng Postman. |
| 6 | Frontend tích hợp hoàn chỉnh | Người dùng có thể quản lý hóa đơn trên web. |
| 7 | Deploy Amplify Hosting | Có URL public để truy cập frontend. |
| 8 | Hoàn thiện tài liệu | Có hướng dẫn triển khai, kiểm thử và cleanup. |

---

## 💰 10. Ước tính ngân sách

### 📦 Chi phí hạ tầng AWS

| Dịch vụ | Ước tính chi phí/tháng | Ghi chú |
|---|---:|---|
| Amazon S3 | ~$0.23 | Lưu file hóa đơn dung lượng nhỏ. |
| AWS Lambda | ~$1 – $3 | Tùy số lượt gọi và thời gian chạy. |
| Amazon Textract | ~$4 – $6 | Tùy số trang xử lý mỗi tháng. |
| Amazon DynamoDB | ~$1 – $3 | Dùng On-Demand cho MVP. |
| API Gateway | ~$1 – $3 | Phụ thuộc số request. |
| Amazon Cognito | ~$0 – $1 | Phù hợp MVP với số lượng user nhỏ. |
| Amplify Hosting | ~$1 – $3 | Hosting frontend và bandwidth thấp. |
| CloudWatch Logs | ~$0 – $2 | Phụ thuộc dung lượng log. |
| Secrets Manager *(optional)* | ~$0.40/secret/tháng | Nếu dùng để lưu OpenAI API key. |
| Route 53 *(optional)* | ~$0.50/tháng + domain | Chỉ dùng nếu có custom domain. |

### 🤖 Chi phí OpenAI API

OpenAI API là dịch vụ bên ngoài AWS. Chi phí phụ thuộc vào:

- Model được sử dụng.
- Số lượng hóa đơn xử lý.
- Độ dài nội dung OCR gửi lên model.
- Số token input và output.

Trong giai đoạn MVP, có thể giới hạn prompt và chỉ gửi phần OCR cần thiết để giảm chi phí.

{{% notice info %}}
Chi phí OpenAI API nên được theo dõi riêng trong OpenAI dashboard vì không hiển thị trong AWS Billing.
{{% /notice %}}

### 👉 Tổng chi phí ước tính

Với quy mô MVP nhỏ, chi phí AWS có thể dao động khoảng:

```txt
~$10 – $30 USD/tháng
```

Chưa bao gồm chi phí OpenAI API và domain tùy chọn.

---

## ⚠️ 11. Đánh giá rủi ro

### 📋 Ma trận rủi ro

| ID | Nguy cơ | Mức độ tác động | Khả năng xảy ra | Mức độ rủi ro |
|---|---|---|---|---|
| R1 | Textract đọc sai hóa đơn mờ hoặc scan lệch | Trung bình | Cao | Cao |
| R2 | OpenAI API chuẩn hóa sai trường dữ liệu | Cao | Trung bình | Cao |
| R3 | OpenAI API key bị lộ | Rất cao | Thấp | Cao |
| R4 | Lambda lỗi do thiếu quyền IAM | Cao | Trung bình | Cao |
| R5 | API Gateway lỗi CORS hoặc sai route | Trung bình | Cao | Cao |
| R6 | DynamoDB lưu sai field như `Tags/tags`, `Starred/IsStarred` | Trung bình | Trung bình | Trung bình |
| R7 | Chi phí tăng do log hoặc request nhiều | Trung bình | Trung bình | Trung bình |
| R8 | Người dùng upload file quá lớn hoặc sai định dạng | Trung bình | Trung bình | Trung bình |

### 🛡️ Giải pháp giảm thiểu

| Rủi ro | Biện pháp |
|---|---|
| R1 | Hướng dẫn upload file rõ nét, hỗ trợ kiểm tra file trước xử lý. |
| R2 | Thiết kế prompt rõ ràng, validate JSON trả về, lưu trạng thái lỗi nếu parsing thất bại. |
| R3 | Không lưu key ở frontend, dùng Lambda env hoặc Secrets Manager. |
| R4 | Cấp IAM Role theo nguyên tắc least privilege. |
| R5 | Cấu hình CORS đầy đủ, kiểm thử bằng Postman và frontend. |
| R6 | Chuẩn hóa field trả về ở Lambda trước khi gửi cho frontend. |
| R7 | Cấu hình log hợp lý, cleanup CloudWatch Logs sau lab. |
| R8 | Giới hạn loại file và dung lượng upload. |

---

## 🎯 12. Kết quả mong đợi

Sau khi hoàn thành project, hệ thống đạt được các kết quả sau:

- Người dùng có thể đăng ký và đăng nhập bằng Amazon Cognito.
- Frontend React được deploy trên AWS Amplify Hosting.
- Người dùng có thể upload hóa đơn từ giao diện web.
- File hóa đơn được lưu vào Amazon S3.
- S3 trigger kích hoạt Lambda xử lý hóa đơn.
- Amazon Textract trích xuất được nội dung hóa đơn.
- OpenAI API chuẩn hóa nội dung OCR thành dữ liệu có cấu trúc.
- Dữ liệu được lưu vào DynamoDB table `InvoiceData`.
- Người dùng có thể xem danh sách và chi tiết hóa đơn.
- Người dùng có thể tìm kiếm theo ID hoặc tên khách hàng.
- Người dùng có thể cập nhật tags và starred.
- Người dùng có thể lọc, sắp xếp và export dữ liệu ra Excel.
- CloudWatch hỗ trợ theo dõi log và debug lỗi.

### 📊 Chỉ số đánh giá thành công

| Chỉ số | Mục tiêu |
|---|---|
| Upload thành công | ≥ 95% với file hợp lệ. |
| OCR thành công | ≥ 90% với hóa đơn rõ nét. |
| API hoạt động ổn định | GET/PATCH/POST trả đúng response. |
| Dữ liệu lưu đúng cấu trúc | Có `InvoiceId`, `ExtractedData`, `TotalAmount`, `Currency`. |
| Frontend dùng được | Người dùng thao tác được toàn bộ luồng chính. |
| Chi phí MVP | Duy trì trong mức thấp, phù hợp demo/lab. |

---

## 📎 Phụ lục

### A. Thông số kỹ thuật

| Hạng mục | Thông tin kỹ thuật |
|---|---|
| Frontend | ReactJS, AWS Amplify Hosting |
| Authentication | Amazon Cognito User Pool |
| Frontend libraries | `aws-amplify`, `@aws-amplify/ui-react`, `xlsx` |
| Backend | AWS Lambda |
| Lambda runtime | Python với boto3 |
| API | Amazon API Gateway REST API |
| File storage | Amazon S3 |
| OCR | Amazon Textract |
| AI normalization | OpenAI API |
| Database | Amazon DynamoDB |
| Monitoring | Amazon CloudWatch |
| Optional secret storage | AWS Secrets Manager |
| Optional DNS | Amazon Route 53 |
| Architecture | Serverless, event-driven |

### B. API routes

| Method | Route | Lambda |
|---|---|---|
| `POST` | `/uploads` | `UploadInvoiceFileFunction` |
| `GET` | `/invoice` | `InvoiceManagementFunction` |
| `GET` | `/invoice/{id}` | `InvoiceManagementFunction` |
| `GET` | `/invoice?name=<customer_name>` | `InvoiceManagementFunction` |
| `PATCH` | `/invoice/tags/{id}` | `InvoiceManagementFunction` |
| `PATCH` | `/invoice/starred/{id}` | `InvoiceManagementFunction` |

### C. Environment variables frontend

```txt
REACT_APP_AWS_REGION=ap-southeast-1
REACT_APP_USER_POOL_ID=ap-southeast-1_xxxxxxxxx
REACT_APP_USER_POOL_CLIENT_ID=xxxxxxxxxxxxxxxxxxxxxxxxxx
REACT_APP_API_UPLOAD_URL=https://<api-id>.execute-api.ap-southeast-1.amazonaws.com/dev/uploads
REACT_APP_API_INVOICE_URL=https://<api-id>.execute-api.ap-southeast-1.amazonaws.com/dev/invoice
REACT_APP_API_UPDATE_TAGS_URL=https://<api-id>.execute-api.ap-southeast-1.amazonaws.com/dev/invoice/tags
REACT_APP_API_UPDATE_STARRED_URL=https://<api-id>.execute-api.ap-southeast-1.amazonaws.com/dev/invoice/starred
REACT_APP_SEND_AUTH_TOKEN=false
```

### D. DynamoDB table

```txt
Table name: InvoiceData
Primary key: InvoiceId
Optional GSI:
- CustomerName-index
- StarredInvoicesIndex
```

### E. Tài liệu tham khảo

1. Amazon Textract Documentation  
2. AWS Lambda Documentation  
3. Amazon API Gateway Documentation  
4. Amazon DynamoDB Documentation  
5. Amazon Cognito Documentation  
6. AWS Amplify Hosting Documentation  
7. OpenAI API Documentation  
8. AWS CloudWatch Documentation  

---

## ✅ Kết luận

Đề xuất **Serverless AI Invoice Scanner** phù hợp với mục tiêu xây dựng một hệ thống xử lý hóa đơn tự động, dễ triển khai và dễ mở rộng. So với phiên bản proposal ban đầu, bản cập nhật này đã điều chỉnh đúng theo project thực tế:

- Thay Amazon Bedrock bằng OpenAI API.
- Làm rõ vai trò của AWS Amplify Hosting.
- Làm rõ Cognito dùng cho frontend authentication.
- Bổ sung ba Lambda functions chính.
- Bổ sung API routes thực tế.
- Bổ sung tags, starred, search, filter, sort và export Excel.
- Cập nhật luồng xử lý đúng: API Gateway → Upload Lambda → S3 → Processing Lambda → Textract → OpenAI API → DynamoDB.
- Bổ sung cleanup và giám sát qua CloudWatch.
