# BÀI 3: ĐỘC HIỂU, DÒ LỖI & TÁI CẤU TRÚC MÃ NGUỒN

## 1. Tại sao cách tiếp cận "giao phó hoàn toàn cho AI tìm lỗi" thất bại?
*   AI không tự hiểu được "Business Rules" (Luật nghiệp vụ) ngầm định nếu không được cung cấp rõ ràng. Về mặt cú pháp, câu lệnh `order.getTotalAmount() > 500000` là hoàn toàn đúng và không gây ra lỗi biên dịch (compile error).
*   Nếu không có luật nghiệp vụ để đối chiếu, AI sẽ giả định logic hiện tại của mã nguồn là đúng ý đồ của lập trình viên và chỉ tập trung vào việc làm sạch cú pháp (refactoring cú pháp) như chuyển đổi `if-else` sang toán tử ba ngôi để tối giản dòng code, vô tình giữ lại lỗi logic nghiêm trọng này.

## 2. Prompt nâng cao cung cấp Luật nghiệp vụ
```markdown
Vai trò: Bạn là một Senior QA và Java Developer.
Ngữ cảnh: Hệ thống TechShop đang gặp khiếu nại của khách hàng về tính năng kiểm tra điều kiện miễn phí vận chuyển (Freeship).

Luật nghiệp vụ (Business Rule): "Đơn hàng từ 500.000 VNĐ trở lên (tức là >= 500000) mới được freeship".

Đoạn code hiện tại:
public boolean checkFreeship(Order order) {
    if (order.getTotalAmount() > 500000) {
        return true;
    } else {
        return false;
    }
}

Nhiệm vụ:
1. Đối chiếu đoạn code hiện tại với Luật nghiệp vụ ở trên và chỉ ra lỗi logic (nêu rõ tại sao khách hàng khiếu nại).
2. Tái cấu trúc (Refactor) lại phương thức trên để sửa lỗi logic đó, đồng thời tối giản hóa code, loại bỏ các khối if-else không cần thiết theo tiêu chuẩn Clean Code.
3. Trả về mã nguồn Java mới trong code block.
```

## 3. Kết quả phân tích lỗi và mã nguồn Refactor từ AI
*   **Phân tích lỗi của AI:** Đoạn code gốc sử dụng toán tử lớn hơn (`>`), dẫn đến việc các đơn hàng có giá trị chính xác bằng `500.000` VNĐ bị đánh giá là không đủ điều kiện freeship (trả về `false`), vi phạm luật nghiệp vụ là "từ 500.000 VNĐ trở lên (>=)".
*   **Mã nguồn sau khi Refactor:**
```java
/**
 * Checks if the order is eligible for free shipping based on the business rules.
 * Eligible orders must have a total amount of 500,000 VND or more.
 *
 * @param order the order to inspect
 * @return true if the order is eligible for free shipping, false otherwise
 */
public boolean checkFreeship(Order order) {
    return order != null && order.getTotalAmount() >= 500000;
}
```
