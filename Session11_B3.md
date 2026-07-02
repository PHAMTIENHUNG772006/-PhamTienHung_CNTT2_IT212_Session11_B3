# Bài 3: Đọc Hiểu, Dò Lỗi & Tái Cấu Trúc Mã Nguồn

## 1. Giải thích vì sao Prompt cũ thất bại

### Prompt cũ

> "Tìm lỗi và sửa code giúp tôi."

### Lý do thất bại

Prompt trên không cung cấp **Business Rule (luật nghiệp vụ)** nên AI chỉ có thể phân tích mã nguồn ở mức cú pháp và cấu trúc, chứ không thể xác định đâu là lỗi nghiệp vụ.

Trong trường hợp này:

```java
if (order.getTotalAmount() > 500000)
```

Về mặt cú pháp Java:

- Không có lỗi biên dịch.
- Điều kiện hoàn toàn hợp lệ.
- Chương trình vẫn chạy bình thường.

Do AI không biết quy định của hệ thống là:

> **"Đơn hàng có giá trị từ 500.000 VNĐ trở lên (>= 500000) sẽ được miễn phí vận chuyển."**

nên AI không thể kết luận rằng toán tử `>` là sai.

Thay vào đó, AI thường tập trung vào các vấn đề dễ nhận biết hơn như:

- rút gọn `if-else` thành toán tử ba ngôi (`?:`)
- đổi tên biến
- tối ưu cú pháp
- cải thiện định dạng mã nguồn

Những thay đổi này giúp mã nguồn ngắn gọn hơn nhưng **không sửa được lỗi nghiệp vụ**, khiến bug vẫn tồn tại.

**Kết luận:** AI không thể tự suy luận chính xác các quy tắc nghiệp vụ nếu chúng không được cung cấp trong Prompt. Đối với các lỗi logic, việc bổ sung **Business Context** là yếu tố quyết định để AI phát hiện và sửa đúng lỗi.

---

# 2. Prompt nâng cao

```
Bạn là một Senior Java Developer và Software Architect có hơn 15 năm kinh nghiệm phát triển các hệ thống E-commerce.

## Mục tiêu

Phân tích đoạn mã Java dưới đây để tìm lỗi logic nghiệp vụ (Business Logic Bug), sau đó refactor theo tiêu chuẩn Clean Code.

## Ngữ cảnh

Đây là chức năng kiểm tra điều kiện miễn phí vận chuyển (Freeship) của hệ thống TechShop.

## Business Rule

- Đơn hàng có tổng giá trị từ 500.000 VNĐ trở lên (>= 500000) sẽ được miễn phí vận chuyển.
- Đơn hàng dưới 500.000 VNĐ sẽ phải trả phí vận chuyển.

Nếu mã nguồn không tuân thủ quy tắc trên thì hãy chỉ rõ lỗi.

## Yêu cầu

1. Phân tích xem mã nguồn có vi phạm Business Rule hay không.
2. Chỉ rõ dòng gây lỗi.
3. Giải thích nguyên nhân.
4. Sửa đúng toán tử nếu cần.
5. Refactor hàm theo chuẩn Clean Code.
6. Loại bỏ các khối if-else không cần thiết.
7. Không thay đổi nghiệp vụ ngoài lỗi được phát hiện.

## Định dạng đầu ra

1. Phân tích lỗi
2. Giải thích
3. Code Java sau khi Refactor

Đây là đoạn mã:

```java
public boolean isFreeShip(Order order) {
    if (order.getTotalAmount() > 500000) {
        return true;
    } else {
        return false;
    }
}
```

```

---

# 3. Kết quả Refactor của AI

## Phân tích lỗi

Business Rule quy định:

> Đơn hàng có giá trị **từ 500.000 VNĐ trở lên (>= 500000)** sẽ được miễn phí vận chuyển.

Tuy nhiên mã nguồn hiện tại sử dụng:

```java
order.getTotalAmount() > 500000
```

Điều này khiến:

- Đơn hàng **500.001 VNĐ** → Freeship ✅
- Đơn hàng **500.000 VNĐ** → Không Freeship ❌

Đây là lỗi logic nghiệp vụ (Business Logic Bug), không phải lỗi cú pháp.

---

## Giải thích

Sai sót nằm ở toán tử so sánh.

Business Rule yêu cầu:

```
>=
```

trong khi mã nguồn lại sử dụng:

```
>
```

Ngoài ra, khối:

```java
if (...) {
    return true;
} else {
    return false;
}
```

là dư thừa vì điều kiện so sánh đã trả về giá trị boolean.

---

## Code sau khi Refactor

```java
public boolean isFreeShip(Order order) {
    // Orders with a total amount of 500,000 VND or more qualify for free shipping.
    return order.getTotalAmount() >= 500000;
}
```

---

# 4. Kết luận

Prompt nâng cao đã áp dụng đầy đủ **5 thành phần của Prompt Engineering**:

- **Role (Vai trò):** Senior Java Developer và Software Architect.
- **Task (Mục tiêu):** Phân tích lỗi logic, sửa lỗi và Refactor mã nguồn.
- **Context (Ngữ cảnh):** Chức năng kiểm tra điều kiện Freeship của hệ thống TechShop.
- **Constraints (Ràng buộc):** Phải đối chiếu với Business Rule, chỉ sửa đúng lỗi, giữ nguyên nghiệp vụ và loại bỏ `if-else` dư thừa.
- **Output Format (Định dạng):** Trả lời theo ba phần: phân tích lỗi, giải thích và mã nguồn Java sau Refactor.

Nhờ cung cấp đầy đủ ngữ cảnh và luật nghiệp vụ, AI có thể phát hiện chính xác lỗi toán tử (`>` thành `>=`) và đồng thời tái cấu trúc mã nguồn theo nguyên tắc Clean Code.