# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 14h00–18h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.7, 1.2 và 1.8 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Hà Nội."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi? Ở mức nào phản hồi bắt đầu
kém mạch lạc?** (2–3 câu)
Khi temperature tăng từ 0.0 lên 1.8, phản hồi chuyển từ tính nhất quán, xác định cao và an toàn khi temperature 0.0, sang đa dạng khi temperature 0.7 và phong phú từ ngữ hơn ở 1.2; khi temperature ở mưc 1.8 trở đi thì câu trả lời trở nên ngẫu nhiên, lặp từ, bịa đặt thông tin hoặc mất mạch logic. Phản hồi bắt đầu kém mạch lạc và xuất hiện các lỗi cấu trúc/ngữ pháp rõ rệt từ mức temperature 1.2 – 1.8 trở đi do mô hình chọn các token có xác suất thấp.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho trợ lý soạn thảo hợp đồng pháp lý,
và bao nhiêu cho trợ lý viết slogan quảng cáo? Giải thích khác biệt.**
- Khi soạn thảo hợp đồng pháp lý: Tôi sẽ đặt `temperature = 0.0` hoặc tối đa `0.2`. Lý do là văn bản pháp lý đòi hỏi tính chính exact tuyệt đối, chuẩn mực, nhất quán và không được phép tự do sáng tạo hay suy đoán sai lệch, vì ở trong môi trường này chỉ 1 chút sai có thể gây ra các vấn đề nghiêm trọng.
- Còn viết slogan quảng cáo: Thì tôi đặt `temperature = 0.8` – `1.0`. Vì nhiệm vụ này cần sự độc đáo, bất ngờ và đa dạng ngôn từ; mức temperature cao giúp mô hình kết hợp các từ ngữ theo những cách mới lạ để tạo điểm nhấn thương hiệu.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 20.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 2 lần,
mỗi lần trung bình ~500 token đầu ra.

**Ước tính chi phí mỗi ngày của model lớn so với model nhỏ cho workload này
(dựa trên bảng giá trong template). Nêu một trường hợp model lớn xứng đáng
với chi phí và một trường hợp model nhỏ là lựa chọn đúng:**
**Ước tính chi phí mỗi ngày:**
- Tổng số request/ngày: 20.000 x 2 = 40.000 requests.
- Tổng output tokens/ngày: 40.000 x 500 = 20.000.000 tokens = 20.000K tokens.
- Chi phí với Model lớn (gemini-3.5-flash) ($0.010 / 1K output): 20.000 x $0.010 = $200.00 / ngày (~$6.000 / tháng).
- Chi phí với Model nhỏ (gemini-3.5-flash-lite) ($0.0006 / 1K output): 20.000 x $0.0006 = $12.00 / ngày (~$360 / tháng).

**Lựa chọn phù hợp:**
- **Model lớn xứng đáng:** Bài toán lập luận logic phức tạp, tư vấn y tế/pháp lý, phân tích dữ liệu tài chính hoặc sinh mã nguồn phần mềm phức tạp - nơi sai sót nhỏ có thể gây thiệt hại lớn.
- **Model nhỏ là lựa chọn đúng:** Tác vụ đơn giản, tần suất cao như phân loại cảm xúc (sentiment analysis), tóm tắt văn bản ngắn, trích xuất dữ liệu định dạng JSON đơn giản, hoặc chatbot trả lời các câu hỏi FAQ cố định.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích máy học (machine learning) là gì?"** nhưng hai system prompt
khác nhau:
- "Bạn là một nhà thơ, trả lời mọi thứ bằng hình ảnh ví von, tránh thuật ngữ."
- "Bạn là kỹ sư phần mềm senior, trả lời chính xác, có ví dụ code khi phù hợp."

**Hai phản hồi khác nhau như thế nào (giọng văn, độ dài, mức kỹ thuật)?
Từ đó rút ra system prompt điều khiển được những khía cạnh nào của phản hồi?**
(3–4 câu)
Phản hồi của nhà thơ mang giọng văn giàu hình ảnh ví von như việc huấn luyện một đứa trẻ hay trồng cây, hoàn toàn tránh thuật ngữ kỹ thuật. Còn kỹ sư phần mềm senior trả lời trực diện, đúng cấu trúc hệ thống , dùng thuật ngữ chuẩn xác và có kèm ví dụ code bằng Python. Qua đó, System Prompt điều khiển được:  Persona & giọng văn, định dạng & Cấu trúc đầu ra , độ sâu kỹ thuật & đối tượng hướng tới, và ranh giới quy tắc .

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~150 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Nếu dùng ước lượng thô để dự
toán ngân sách API cho ứng dụng tiếng Việt, bạn sẽ dự toán thiếu hay thừa —
và vì sao?**
Với một đoạn văn tiếng Việt ~150 từ, `tiktoken` đếm ra khoảng 250 - 320 tokens, trong khi công thức `150 / 0.75` chỉ tính ra 200 tokens (chênh lệch 25% - 60%). Nếu dùng công thức ước lượng thô `số từ / 0.75`, bạn sẽ dự toàn thiếu ngân sách nghiêm trọng. Nguyên nhân là tokenizer (BPE cl100k_base) được thiết kế tối ưu cho tiếng Anh; đối với tiếng Việt, các từ có dấu và âm tiết thường bị phân tách thành nhiều subword/byte token nhỏ, khiến mỗi từ tiếng Việt tốn trung bình từ 1.5 đến 2.2 tokens thay vì 1.33 tokens như tiếng Anh.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Xét ba ứng dụng: (a) chatbot văn bản, (b) trợ lý giọng nói đọc to phản hồi,
(c) pipeline dịch tài liệu chạy ngầm ban đêm. Ứng dụng nào hưởng lợi nhiều
nhất từ streaming, ứng dụng nào không cần — và tại sao?** (1 đoạn văn)
Ứng dụng hưởng lợi nhiều nhất là chatbot văn bản và trợ lý giọng nói. Với chatbot văn bản, streaming giảm thời gian chờ token đầu tiên (TTFT) xuống gần như tức thì, mang lại cảm giác phản hồi thời gian thực. Với trợ lý giọng nói, streaming cho phép bộ tổng hợp giọng nói (TTS) đọc từng câu ngay khi vừa sinh ra mà không phải chờ cả đoạn văn dài. Ngược lại,  pipeline dịch tài liệu chạy ngầm ban đêm hoàn toàn không cần streaming vì đây là tác vụ xử lý lô (batch job) không có người dùng tương tác trực tiếp; việc dùng streaming chỉ làm tăng độ phức tạp xử lý mà không mang lại giá trị UX.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**Khi API quá tải và hàng nghìn client cùng retry, exponential backoff giúp
gì so với delay cố định? Tra cứu thêm: kỹ thuật "jitter" (thêm độ trễ ngẫu
nhiên) giải quyết vấn đề gì còn sót lại?**
- Exponential backoff So với delay cố định, việc tăng thời gian chờ theo cấp số nhân ($base \times 2^{attempt}$) làm dãn rộng khoảng cách giữa các lần thử lại, giúp hệ thống phái server có đủ khoảng thời gian trống để hạ tải, xử lý hàng chờ và phục hồi. Nếu dùng delay cố định, hàng nghìn client sẽ liên tục dội sóng request cùng lúc (thundering herd), khiến server vĩnh viễn không thể thoát khỏi trạng thái nghẽn.
-Jitter (độ trễ ngẫu nhiên) Giải quyết vấn đề đồng bộ hóa retry (retry synchronization). Dù đã có backoff cấp số nhân, những client gặp lỗi cùng thời điểm vẫn sẽ dội request retry chính xác cùng lúc ở các mốc $t, 2t, 4t...$. Jitter thêm một khoảng nhiễu ngẫu nhiên vào thời gian chờ, phân tán các đợt retry đều ra theo thời gian để san phẳng đỉnh lưu lượng đột biến.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Viết lại system prompt bạn dùng cho trợ lý của mình. Chỉ ra 2 chỗ trong
prompt mà nếu xóa đi, hành vi trợ lý sẽ thay đổi rõ rệt — và mô tả thay đổi
đó:**
System Prompt:" Bạn là trợ giảng AI thân thiện của khóa học K4 LLM API. Hãy trả lời ngắn gọn, đi thẳng vào vấn đề bằng tiếng Việt. Luôn cung cấp ví dụ mã nguồn minh họa khi giải thích các khái niệm kỹ thuật và tuyệt đối không tự bịa đặt thông tin." *
- **Chỗ 1 — "trả lời ngắn gọn, đi thẳng vào vấn đề bằng tiếng Việt":** Nếu xóa đi, trợ lý sẽ phản hồi dông dài, đưa ra nhiều thông tin lề lối và có thể trả lời bằng tiếng Anh nếu câu hỏi chứa nhiều thuật ngữ chuyên ngành.
- **Chỗ 2 — "Luôn cung cấp ví dụ mã nguồn minh họa":** Nếu xóa đi, khi được hỏi về các khái niệm (như exponential backoff hay tiktoken), trợ lý chỉ giải thích thuần lý thuyết suông bằng văn bản mà không đưa ra snippet code Python thực tế để minh họa.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn giữ history 4 lượt cuối. Hãy mô tả một tình huống hội thoại
cụ thể mà giới hạn này khiến trợ lý trả lời sai/mất ngữ cảnh, và đề xuất một
cách khắc phục (ví dụ: tóm tắt các lượt cũ, tăng giới hạn có chọn lọc...):**
- Tình huống mất ngữ cảnh: Ở lượt 1, người dùng nói *"Tôi đang xây dựng ứng dụng với framework FastHTML và Python 3.13"*. Sau đó, người dùng hỏi đáp tiếp 4 lượt khác về cú pháp Python, hàm async, và cách đọc file. Đến lượt thứ 6, câu nói ở lượt 1 đã bị cắt khỏi history (chỉ giữ 4 lượt = 8 messages gần nhất). Khi người dùng hỏi *"Hãy viết giúp tôi hàm xử lý route nhận dữ liệu từ form"*, trợ lý bị mất ngữ cảnh và mặc định tạo code với FastAPI/Flask thay vì FastHTML.
- **Cách khắc phục:** 
  1. Conversation Summarization: Khi history vượt quá 4 lượt, dùng một model nhỏ tóm tắt lại các điểm chính (tech stack, yêu cầu chính của user) thành một đoạn tóm tắt ngắn và chèn đoạn tóm tắt này vào System Prompt.
  2. System Invariant / State Pinning: Cho phép lưu trữ riêng các thông tin cấu hình ban đầu (như ngôn ngữ, framework, vai trò) vào một biến trạng thái cố định và luôn gửi kèm trong prompt thay vì phụ thuộc vào sliding window của history.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên GitHub cá nhân và nộp link repo vào vlearn (theo hướng dẫn README)
