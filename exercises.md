# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng đánh dấu bằng câu trả lời thật
(chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
Khi temperature thấp, phản hồi thường ổn định, ngắn gọn và bám sát fact hơn. Khi temperature tăng, câu trả lời đa dạng hơn về cách diễn đạt và có xu hướng “bay” hơn, nhưng độ nhất quán giảm. Ở mức rất cao, model dễ thêm chi tiết sáng tạo hoặc lạc nhẹ khỏi trọng tâm.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
Tôi sẽ đặt temperature khoảng 0.2–0.3. Mức này giúp chatbot trả lời ổn định, ít bịa và giữ giọng điệu nhất quán, rất phù hợp cho hỗ trợ khách hàng nơi độ chính xác quan trọng hơn sự sáng tạo.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
Với output token, GPT-4o đắt hơn GPT-4o-mini khoảng 16.7 lần (0.010 so với 0.0006 USD/1K token). GPT-4o xứng đáng khi cần suy luận khó, chất lượng diễn đạt cao hoặc xử lý tình huống nhạy cảm; còn mini phù hợp cho FAQ, phân loại, tóm tắt nhanh, và các tác vụ cần rẻ và nhanh.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
Phản hồi theo persona “giáo viên tiểu học” thường ngắn hơn, dùng từ đơn giản và ví dụ gần gũi, còn persona “chuyên gia tài chính” sẽ dài hơn, nhiều thuật ngữ kỹ thuật và giải thích theo lớp nghĩa sâu hơn. System prompt hoạt động như một chỉ dẫn ưu tiên, nên nó định hướng giọng điệu, mức chi tiết và kiểu ví dụ mà model chọn. Nói cách khác, cùng một câu hỏi nhưng cách trả lời thay đổi rõ rệt theo vai trò được gán trước.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
Với một đoạn tiếng Việt khoảng 100 từ, số token theo tiktoken thường cao hơn ước lượng thô `số từ / 0.75` khoảng 20–30%, tùy đoạn cụ thể. Tiếng Việt hay tốn nhiều token hơn vì có dấu, nhiều âm tiết tách rời bằng khoảng trắng, và tokenizer không luôn coi mỗi “từ” tiếng Việt là một đơn vị duy nhất như cách người học thường đếm. Vì vậy ước lượng bằng số từ chỉ cho cảm giác gần đúng, không thay được đếm token thật.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
Streaming quan trọng nhất khi câu trả lời dài hoặc khi người dùng cần cảm giác phản hồi ngay, ví dụ chatbot, trợ lý hội thoại, hoặc tác vụ sinh văn bản nhiều đoạn. Non-streaming phù hợp hơn khi đầu ra ngắn, khi cần xử lý xong toàn bộ mới hiển thị, hoặc khi muốn code đơn giản hơn và không cần cập nhật từng token theo thời gian thực.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
Exponential backoff giúp giảm áp lực lên hệ thống đang quá tải vì mỗi lần retry sẽ chờ lâu hơn, tránh bắn dồn dập các request thất bại vào cùng một thời điểm. Nếu hàng nghìn client cùng retry với delay cố định, chúng rất dễ “đồng bộ” thành một đợt sóng mới, làm tình trạng nghẽn nặng hơn và kéo dài sự cố. Backoff tăng dần giúp các lần thử lại phân tán hơn và tăng xác suất thành công.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
Tôi chọn persona “trợ giảng thân thiện, trả lời ngắn gọn bằng tiếng Việt, ưu tiên rõ ràng và thực tế”. System prompt có thể viết: “Bạn là trợ giảng thân thiện của khóa AI. Hãy trả lời ngắn gọn, chính xác, bằng tiếng Việt và nếu thiếu thông tin thì hỏi lại một câu ngắn.” Cụm “trả lời ngắn gọn” giúp giảm lan man, còn “bằng tiếng Việt” giữ đầu ra đồng nhất và dễ dùng cho người học trong lớp.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
Hạn chế lớn nhất là history chỉ giữ 3 lượt gần nhất nên trợ lý dễ quên ngữ cảnh xa hơn. Tôi sẽ cải thiện bằng cách thêm cơ chế tóm tắt ngữ cảnh: sau mỗi vài lượt, gộp các trao đổi cũ thành một bản summary ngắn và lưu summary đó cùng history hiện tại. Triển khai có thể dùng một file JSON hoặc SQLite để lưu summary giữa các phiên, rồi chèn summary này vào system prompt khi khởi tạo cuộc trò chuyện mới.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
