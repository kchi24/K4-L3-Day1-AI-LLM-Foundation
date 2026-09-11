# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Qua bốn phản hồi khi tăng dần temperature từ 0.0 đến 1.5, tôi nhận thấy rõ quy luật đánh đổi giữa tính chính xác xác định và tính sáng tạo bất định: ở mức 0.0, phản hồi có tính xác định cao (deterministic), câu trả lời trực diện, chuẩn xác và thường lặp lại các sự thật quen thuộc (như Việt Nam xuất khẩu cà phê Robusta đứng thứ 2 thế giới hay hang Sơn Đoòng lớn nhất thế giới). Khi tăng lên 0.5 - 1.0, câu trả lời trở nên tự nhiên, phong phú và giàu biểu cảm hơn về vốn từ vựng; còn ở mức 1.5, phân phối xác suất bị san phẳng khiến model chọn nhiều token ít phổ biến, cách diễn đạt bay bổng, bất ngờ nhưng bắt đầu có hiện tượng lan man, thiếu mạch lạc và dễ gặp nguy cơ bịa đặt thông tin (hallucination).

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ đặt temperature ở mức thấp, từ 0.0 đến 0.2 (tối đa 0.3). Chatbot hỗ trợ khách hàng đòi hỏi tính nhất quán, câu trả lời chuẩn xác, trung thực và phải bám sát tuyệt đối theo thông tin tài liệu sản phẩm hoặc chính sách của công ty. Nhiệt độ thấp giúp giảm thiểu tối đa hiện tượng "ảo giác" (hallucination) — tránh tình trạng chatbot bịa ra các chương trình khuyến mãi, sai lệch bảng giá hoặc hứa hẹn sai về chính sách bảo hành, hoàn tiền gây rủi ro pháp lý và thiệt hại tài chính cho doanh nghiệp.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> - Ước tính chi phí: Với đơn giá output token là $0.010 / 1K token ($10 / 1M token) đối với GPT-4o và $0.0006 / 1K token ($0.60 / 1M token) đối với GPT-4o-mini, GPT-4o đắt gấp khoảng 16.7 lần (0.010 / 0.0006 ≈ 16.67 lần) so với GPT-4o-mini. Cho khối lượng công việc 10.000 người dùng × 3 lần × 350 token = 10,5 triệu token/ngày, chi phí output của GPT-4o là ~$105/ngày trong khi GPT-4o-mini chỉ tốn ~$6.3/ngày (chênh lệch gần 3.000 USD mỗi tháng).
> - Trường hợp GPT-4o xứng đáng chi phí: Các tác vụ phức tạp đòi hỏi suy luận logic đa bước (multi-step reasoning), phân tích báo cáo tài chính/hồ sơ pháp lý chuyên sâu, debug các đoạn mã nguồn phức tạp hoặc ra quyết định nghiệp vụ nhạy cảm mà các mô hình nhỏ dễ đưa ra kết luận sai lệch.
> - Trường hợp nên dùng GPT-4o-mini: Các tác vụ lặp đi lặp lại với lưu lượng truy cập lớn như chatbot trả lời câu hỏi thường gặp (FAQ), phân loại ý định người dùng (intent classification), trích xuất thông tin thực thể cơ bản, dịch thuật thông thường hoặc tóm tắt ngắn các đoạn văn bản.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> - Khi đóng vai giáo viên tiểu học, phản hồi ngắn gọn, văn phong ấm áp, sử dụng từ ngữ đơn giản thường ngày và đưa ra ví dụ trực quan gần gũi như "cuốn sổ ghi chép chung của cả lớp mà ai cũng có một bản, không một bạn nào có thể tự ý tẩy xóa hay gian lận".
> - Khi đóng vai chuyên gia tài chính, phản hồi dài và trang trọng hơn hẳn, sử dụng hàng loạt thuật ngữ chuyên ngành chuyên sâu như "sổ cái phân tán (distributed ledger)", "cơ chế đồng thuận (consensus mechanism)", "bằng chứng công việc (Proof-of-Work)", "tính bất biến (immutability)" và các thuật toán mã hóa mật mã học.
> - System prompt đóng vai trò thiết lập khung ngữ cảnh và định hình persona gốc cho toàn bộ phiên hội thoại, tác động trực tiếp đến phong cách ngôn ngữ (tone of voice), độ dài câu trả lời, mức độ trừu tượng của kiến thức và việc lựa chọn góc nhìn phù hợp với đối tượng tiếp nhận trước khi mô hình xử lý yêu cầu của người dùng.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> - Với đoạn văn tiếng Việt khoảng 100 từ, công thức ước lượng thô cho ra khoảng 133 token (100 / 0.75), trong khi `tiktoken` (sử dụng encoding `o200k_base` hoặc `cl100k_base`) đếm thực tế thường dao động từ 180 đến 210 token, tức chênh lệch thực tế cao hơn ước lượng từ 35% đến 55%.
> - Tiếng Việt tốn nhiều token hơn tiếng Anh cùng độ dài vì các thuật toán phân tách token (như Byte-Pair Encoding - BPE) được huấn luyện chủ yếu trên kho ngữ liệu tiếng Anh, nơi hầu hết các từ phổ biến đều tương ứng với đúng 1 token duy nhất. Ngược lại, tiếng Việt có thanh điệu (sắc, huyền, hỏi, ngã, nặng) cùng hệ thống nguyên âm có dấu kết hợp Unicode đa byte, khiến tokenizer không thể ánh xạ một từ tiếng Việt nguyên vẹn vào từ điển mà phải bẻ nhỏ thành 2 đến 3 subword tokens hoặc byte tokens riêng biệt, dẫn đến số lượng token thực tế bị tăng cao rõ rệt.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất trong các giao diện tương tác người dùng trực tiếp theo thời gian thực như chatbot hội thoại, trợ lý ảo cá nhân hoặc ứng dụng sinh nội dung bài viết dài, nơi chỉ số Time to First Token (TTFT - thời gian phản hồi token đầu tiên) đóng vai trò quyết định; người dùng có thể theo dõi câu trả lời xuất hiện tức thì sau vài trăm mili-giây thay vì cảm thấy sốt ruột khi phải chờ đợi màn hình tải trong 5–15 giây. Ngược lại, non-streaming lại phù hợp hơn đối với các tác vụ xử lý tác vụ nền (background batch processing), các đường ống gọi API giữa các hệ thống (system-to-system integration) cần dữ liệu trả về nguyên vẹn dạng JSON/cấu trúc để phân tích dữ liệu, hoặc trong các trường hợp bắt buộc phải chạy qua lớp lọc an toàn/kiểm duyệt toàn bộ nội dung (content moderation) trước khi hiển thị đến người dùng cuối.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> So với delay cố định, exponential backoff (thời gian chờ tăng dần theo cấp số nhân: 0.1s, 0.2s, 0.4s, 0.8s...) có lợi thế vượt trội là giúp giãn cách tự nhiên tần suất các yêu cầu gửi lại, làm giảm áp lực truy cập dồn dập và tạo ra khoảng thời gian phục hồi cần thiết cho server khi hệ thống đang bị nghẽn mạch hoặc quá tải tạm thời. Nếu hàng nghìn client cùng retry với một mức delay cố định (ví dụ luôn chờ đúng 1 giây), tất cả client sẽ đồng loạt gửi lại request vào cùng một thời điểm sau sự cố ban đầu, gây ra hiện tượng "cơn bão retry" (retry storm hay thundering herd problem), khiến server vốn đang kiệt quệ tài nguyên lại tiếp tục hứng chịu đợt bùng nổ lưu lượng mới, dẫn đến tình trạng sập toàn bộ hệ thống (cascading failure) và không thể tự phục hồi.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> - Persona lựa chọn: Trợ giảng lập trình và trí tuệ nhân tạo (AI Teaching Assistant).
> - System prompt: "Bạn là trợ giảng môn Lập trình và Trí tuệ Nhân tạo tại VinUni, luôn nhiệt huyết, kiên nhẫn và giải thích mạch lạc. Hãy trả lời bằng tiếng Việt chuẩn mực, đi thẳng vào trọng tâm vấn đề, đưa ra ví dụ code minh họa súc tích khi cần. Luôn giới hạn câu trả lời ngắn gọn trong khoảng 3-5 câu hoặc một đoạn súc tích, tuyệt đối tránh trả lời lan man dài dòng."
> - Giải thích lựa chọn từ ngữ:
>   1. "giới hạn câu trả lời ngắn gọn trong khoảng 3-5 câu, tuyệt đối tránh trả lời lan man dài dòng": Lựa chọn này giúp kiểm soát chặt chẽ số lượng output token (vừa giảm chi phí vận hành API, vừa tối ưu tốc độ phản hồi), đồng thời giúp người học dễ dàng nắm bắt ngay các ý cốt lõi mà không bị quá tải thông tin.
>   2. "trả lời bằng tiếng Việt chuẩn mực": Đảm bảo mô hình luôn duy trì ngôn ngữ giao tiếp thân thiện và thống nhất với sinh viên, dịch đúng các thuật ngữ chuyên ngành công nghệ và không bị chuyển đổi ngôn ngữ ngẫu nhiên sang tiếng Anh khi gặp các đoạn code hoặc prompt có chứa từ ngữ kỹ thuật tiếng Anh.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> - Hạn chế lớn nhất: Trợ lý hiện tại chỉ lưu trữ bộ nhớ ngữ cảnh ngắn hạn bằng cửa sổ trượt tĩnh 3 lượt gần nhất (sliding window tối đa 6 tin nhắn). Khi cuộc hội thoại kéo dài vượt quá 3 lượt hỏi đáp, toàn bộ các ngữ cảnh ban đầu (như mục tiêu bài toán, các biến số hay ràng buộc mà người dùng đã thiết lập ở các câu đầu) đều bị xóa sạch, khiến trợ lý mất tính liên tục trong các phiên thảo luận chuyên sâu.
> - Đề xuất cải thiện: Triển khai cơ chế Tóm tắt bộ nhớ hội thoại tự động (Conversation Memory Summarization).
> - Cách triển khai: Khi số lượng tin nhắn trong `history` đạt đến giới hạn (ví dụ quá 6 messages), thay vì chỉ cắt bỏ trực tiếp các tin nhắn cũ nhất, ta tạo một lời gọi API ngầm (sử dụng model nhỏ và tiết kiệm như GPT-4o-mini) với prompt yêu cầu tóm tắt cô đọng những thông tin trọng yếu của các lượt hội thoại đã qua thành một biến `conversation_summary`. Đoạn tóm tắt này sau đó được lưu trữ và nhúng trực tiếp vào đầu danh sách messages (ngay sau system prompt), kết hợp với 2-3 lượt tin nhắn mới nhất để gửi lên mô hình. Bằng cách này, trợ lý vừa duy trì được ngữ cảnh xuyên suốt cuộc trò chuyện, vừa giữ cho chi phí token input luôn ở mức tối ưu.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
