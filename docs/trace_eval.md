# 📊 BÁO CÁO THU HOẠCH NGHIỆM THU BÀI LAB 3 (BƯỚC 3 — SUBMISSION ARTIFACT)

> **Họ và Tên Học viên:** Phùng Đình Triển  
> **Mã Sinh Viên / Mã Học viên:** 2A202602837  
> **Chủ đề Lựa chọn:** Trợ lý Học vụ & Tra cứu Lịch thi VinUni

---

## 1. BẢNG CHẤM ĐIỂM AGENTIC FIT SCORING MATRIX (ĐÁNH GIÁ CHỦ ĐỀ)

| Tiêu chí Đánh giá | Mức độ (1 - 5) | Giải trình chi tiết lý do chọn điểm |
| :--- | :---: | :--- |
| **1. Multi-step Reasoning** | 4 / 5 | Bài toán cần suy luận nối tiếp: đầu tiên tra cứu điểm GPA của sinh viên, sau đó dựa vào kết quả GPA để quyết định có cần đề xuất đặt lịch tư vấn với cố vấn học tập hay không. Đây là chuỗi 2 bước suy luận phụ thuộc nhau, không phải truy vấn 1 bước đơn giản. |
| **2. Tool Interaction** | 5 / 5 | Hệ thống bắt buộc phải kết nối tới 2 công cụ bên ngoài qua MCP Server: (1) tool tra cứu điểm/lịch thi từ cơ sở dữ liệu học vụ, (2) tool đặt lịch hẹn với cố vấn. Không thể trả lời chỉ bằng kiến thức nội tại của LLM. |
| **3. Dynamic Decision** | 4 / 5 | Sau khi quan sát kết quả tra cứu (ví dụ GPA thấp hơn ngưỡng, hoặc lịch thi bị trùng), Agent phải tự quyết định bước tiếp theo (có cần gọi tool đặt lịch tư vấn hay không) thay vì đi theo kịch bản cố định. |
| **4. Long Horizon Goal** | 3 / 5 | Trong phạm vi 1 phiên hội thoại, Agent cần giữ mục tiêu xuyên suốt (hỗ trợ sinh viên hoàn tất một yêu cầu học vụ) qua 2-3 lượt gọi tool, nhưng chưa đòi hỏi ghi nhớ trạng thái dài hạn qua nhiều phiên khác nhau. |
| **TỔNG ĐIỂM AGENTIC FIT** | **16 / 20** | *Tổng điểm > 12/20: Bài toán rất phù hợp triển khai Agentic System (ReAct Agent), vì có đủ tính đa bước, cần công cụ ngoài và ra quyết định linh hoạt dựa trên quan sát.* |

---

## 2. TRÍCH XUẤT KẾT QUẢ WATERFALL TRACE LOG (SAU KHI CHẠY TEST SUITE TRÊN API THẬT)

> ⚠️ **YÊU CẦU NGHIỆM THU:** Mở tệp `.env` điền `GEMINI_API_KEY` (hoặc `OPENAI_API_KEY`) để kết nối LLM thật trước khi thực thi `python src/app.py --all`. Bài nộp chỉ dùng Mock Offline Provider sẽ không đạt điểm nghiệm thực tế.

Dán 1 đoạn trích xuất log tiêu biểu từ file `docs/trace_waterfall.json` sinh ra từ phản hồi LLM API thật:

```json
[
  {
    "step": 1,
    "query": "Hãy tra cứu thông tin học vụ của sinh viên SV2026001.",
    "action_type": "TOOL_EXECUTION",
    "tool_name": "academic_query",
    "arguments": { "student_id": "SV2026001" },
    "observation": {
      "status": "SUCCESS",
      "student_id": "SV2026001",
      "data": {
        "full_name": "Nguyễn Văn An",
        "class": "AI-K4",
        "gpa": 3.85,
        "email": "an.nv@vinuni.edu.vn",
        "status": "Đang học",
        "advisor": "PGS.TS Nguyễn Văn A"
      }
    },
    "latency_ms": 890.4
  }
]
```
**Nhận xét bổ sung (Edge Case - TC05):** Khi tra cứu mã sinh viên không tồn tại (`SV9999999`), MCP Server trả về đúng trạng thái `NOT_FOUND` và Agent phản hồi chính xác: *"Không tìm thấy dữ liệu sinh viên có mã 'SV9999999'"* mà không bịa đặt thông tin — đáp ứng đúng kỳ vọng xử lý Edge Case.

**Hạn chế phát hiện được (TC03, TC04):** Ở 2 test case yêu cầu suy luận đa bước (đặt lịch hẹn, và suy luận GPA thấp → tự động đặt lịch), Agent hiện tại mới dừng lại sau khi gọi tool `academic_query` mà chưa tự động tiếp tục gọi tool `schedule_appointment` ở bước kế tiếp như kỳ vọng ban đầu. Nguyên nhân có thể do vòng lặp ReAct trong `run_react_agent()` kết thúc (`break`) ngay sau 1 lần Tool Execution + Final Answer, thay vì tiếp tục đưa Observation trở lại cho LLM để LLM tự quyết định có cần gọi thêm tool thứ 2 hay không.

---

## 3. TỔNG KẾT KẾT QUẢ NGHIỆM THU & NỘP BÀI

- [x] Đã điền API Key thật trong `.env` và xác nhận Agent chạy mượt mà trên LLM API thật (Gemini API - model `gemini-3.6-flash`).
- **Tổng số Test Cases đã chạy thành công:** 5 / 5 test cases (100% thực thi không lỗi crash).
- **Số lượt gọi Tool qua MCP Server chính xác:** 4 lượt (TC02, TC03, TC04, TC05 đều gọi thành công tool `academic_query`; TC01 không cần gọi tool theo đúng thiết kế; riêng TC03/TC04 chưa gọi thêm tool `schedule_appointment` như kỳ vọng đầy đủ).
- **Kết quả đẩy Repo nộp bài:** [x] Đã Commit và Push mã nguồn thành công lên GitHub cá nhân.

---

> ✅ **HOÀN TẤT NỘP BÀI:** Sao chép đường link GitHub Repository cá nhân của bạn và dán vào ô nộp bài trên hệ thống LMS VLearn để hoàn tất Bài Lab 3!
