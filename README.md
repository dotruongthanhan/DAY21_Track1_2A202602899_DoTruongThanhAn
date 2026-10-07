# Lab 21 — Phân tích rủi ro AI qua case study thực tế

- Họ và tên: Đỗ Trương Thành Ân
- MSSV / mã học viên: 2A202602899
- Lớp: Track 1 - H201
- Ngành đã chọn: Y tế / symptom checker / health assistant

### 1. Industry Risk Snapshot

| Nội dung | Đánh giá của tôi và lý do |
| --- | --- |
| Những tác hại chính có thể xảy ra | - **Symptom checker**: Chẩn đoán sai bệnh lý, bỏ sót triệu chứng cấp cứu dẫn đến chậm trễ điều trị hoặc điều trị sai hướng.<br>- **Health assistant**: Đưa ra lời khuyên y tế/dinh dưỡng/tâm lý độc hại, phản khoa học, kích động hành vi nguy hiểm.<br>- **Clinical documentation**: AI tạo ảo giác (hallucination), tự bịa phác đồ điều trị, dị ứng thuốc hoặc thông tin bệnh sử; có thiên kiến (bias) chủng tộc/giới tính làm sai lệch hồ sơ y tế. |
| Mức độ high-stakes | - **Symptom checker**: **Cao**. Chẩn đoán sai dẫn đến điều trị sai, lãng phí thời gian, tiền bạc và tiềm ẩn nguy cơ đe dọa trực tiếp đến tính mạng bệnh nhân.<br>- **Health assistant**: **Cao**. Khi không có nhân viên y tế kiểm chứng thời gian thực, người dùng đang khủng hoảng dễ tin tưởng tuyệt đối vào AI và thực hiện các hành vi gây tổn hại nghiêm trọng đến bản thân.<br>- **Clinical documentation**: **Trung bình đến Cao**. Sai sót trong hồ sơ y tế có thể gây ra hậu quả y khoa nghiêm trọng (kê sai thuốc, phẫu thuật sai vị trí), tuy nhiên có sự hiện diện của nhân viên y tế trong quy trình nên rủi ro có thể được giảm thiểu nếu quy trình rà soát được tuân thủ nghiêm ngặt. |
| Dữ liệu nhạy cảm có thể được sử dụng | - **PII (Thông tin định danh cá nhân)**: Họ tên, ngày sinh, số căn cước/bảo hiểm, địa chỉ, số điện thoại, email, thông tin liên hệ khẩn cấp.<br>- **PHI (Thông tin sức khỏe được bảo vệ)**: Triệu chứng lâm sàng, chẩn đoán, tiền sử bệnh án, đơn thuốc đang dùng, kết quả xét nghiệm, chỉ số sinh tồn (nhịp tim, huyết áp, đường huyết, mỡ cơ thể), file ghi âm đối thoại thăm khám giữa bác sĩ và bệnh nhân. |
| Nhu cầu human review | **Rất cao (Bắt buộc)** ở mọi bước:<br>- **Symptom checker & Health assistant**: Cần bác sĩ hoặc chuyên gia tâm lý lâm sàng thẩm định, thiết lập rào chắn an toàn (guardrails) nghiêm ngặt trước khi phản hồi người dùng; có cơ chế chuyển tuyến khẩn cấp cho con người.<br>- **Clinical documentation**: Bác sĩ điều trị bắt buộc phải đọc rà soát từng dòng và ký xác nhận (sign-off) toàn bộ văn bản phiên âm trước khi lưu chính thức vào hệ thống Bệnh án Điện tử (EHR). |

### 2. Case study 1 — OpenAI Whisper trong phiên âm hồ sơ bệnh án y khoa (Clinical Documentation)

#### Brief Case

- Tổ chức / sản phẩm AI: OpenAI — Whisper (công cụ nhận dạng và phiên âm giọng nói tự động dựa trên mô hình ngôn ngữ lớn - ASR, được tích hợp vào các phần mềm ghi chép bệnh án y khoa tại các bệnh viện).
- Thời gian, địa điểm / bối cảnh: Công bố tháng 10/2024 tại Hoa Kỳ; bối cảnh các bệnh viện và cơ sở y tế trên toàn nước Mỹ đẩy mạnh ứng dụng AI phiên âm nhằm giảm bớt gánh nặng hành chính ghi chép bệnh án cho bác sĩ.
- AI được dùng để làm gì: Lắng nghe và tự động chuyển đổi các cuộc hội thoại, thăm khám lâm sàng giữa bác sĩ và bệnh nhân thành văn bản để lưu trữ vào hồ sơ bệnh án điện tử (medical consultation transcription / clinical documentation).
- Vấn đề hoặc sự kiện đáng chú ý: Whisper thường xuyên gặp hiện tượng ảo giác AI ("AI hallucinations"), tự động bịa đặt các đoạn văn bản hoàn toàn không tồn tại trong file ghi âm gốc. Các nội dung bịa đặt bao gồm bình luận phân biệt chủng tộc (racial commentary), ngôn từ bạo lực (violent rhetoric), và đặc biệt nguy hiểm là tự bịa ra các phương pháp điều trị y tế không có thật (imagined medical treatments). Dù Microsoft đã công khai cảnh báo công cụ này không được thiết kế cho các trường hợp sử dụng rủi ro cao (high-risk use cases), nhiều cơ sở y tế vẫn ồ ạt triển khai.
- Số liệu có nguồn:
  + Tỷ lệ lỗi ảo giác: Nghiên cứu của một chuyên gia tại Đại học Michigan (University of Michigan) chỉ ra rằng ảo giác xuất hiện trong 8 trên 10 (8/10, tức 80%) bản ghi âm mà ông khảo sát.
  + Quy mô triển khai: Theo điều tra của hãng tin AP News, hơn 30.000 bác sĩ lâm sàng (clinicians) và 40 hệ thống y tế lớn tại Mỹ (như Mankato Clinic ở Minnesota, Bệnh viện Nhi đồng Los Angeles - Children’s Hospital Los Angeles) đã đưa công cụ ứng dụng Whisper vào vận hành thực tế.
- Nguồn: "OpenAI’s Whisper Experiencing ‘AI Hallucinations’ Despite High-Risk Applications" — Tác giả: Will McCurdy — Đơn vị: PCMag UK (dẫn nguồn điều tra từ AP News) — Ngày công bố: 27/10/2024 — URL: https://uk.pcmag.com/ai/155065/openais-whisper-experiencing-ai-hallucinations-despite-high-risk-applications
- Phân biệt bằng chứng và nhận định:
  + **Bằng chứng (Nguồn xác nhận)**: Whisper tự tạo ra câu từ phân biệt chủng tộc, bạo lực và phương pháp điều trị y tế tưởng tượng; nghiên cứu của ĐH Michigan ghi nhận tỷ lệ ảo giác 8/10 bản ghi; hơn 30.000 bác sĩ và 40 hệ thống y tế đang sử dụng công cụ dựa trên Whisper; Microsoft đã cảnh báo không dùng cho ứng dụng rủi ro cao; các chuyên gia (GS. Alondra Nelson - Princeton, cựu kỹ sư OpenAI William Saunders) cảnh báo nguy cơ chẩn đoán sai lầm nghiêm trọng.
  + **Nhận định / Suy luận của tôi**: Bài viết chưa ghi nhận ca tử vong cụ thể nào trên thực tế trực tiếp do lỗi phiên âm của Whisper. Tuy nhiên, tôi suy luận rằng với áp lực khám chữa bệnh quá tải, các bác sĩ rất dễ mắc hội chứng thiên vị tự động hóa (automation bias), dẫn đến việc không đọc soát kỹ văn bản dài và khiến phác đồ điều trị ảo của AI lọt vào hồ sơ y tế thật, tạo ra nguy cơ đe dọa sinh mạng bệnh nhân.

#### Harm Map Worksheet

| Trường | Phân tích của tôi |
| --- | --- |
| High-risk moment | Thời điểm bác sĩ kết thúc cuộc thăm khám và hệ thống AI tự động trích xuất văn bản phiên âm (chứa phương pháp điều trị bịa đặt) vào hồ sơ bệnh án điện tử mà bác sĩ không kiểm tra đối chiếu kỹ với âm thanh gốc trước khi ký duyệt. |
| Stakeholder bị ảnh hưởng | - **Trực tiếp**: Bệnh nhân (nguy cơ bị điều trị sai, dùng sai thuốc, sốc thuốc hoặc gặp biến chứng nguy hiểm; bị lưu thông tin sai lệch vào bệnh án suốt đời).<br>- **Gián tiếp**: Bác sĩ và nhân viên y tế (đối mặt rủi ro kiện tụng sơ suất y khoa, mất chứng chỉ hành nghề); Bệnh viện/hệ thống y tế (khủng hoảng danh tiếng, bồi thường thiệt hại); Nhà phát triển AI (OpenAI, Microsoft - rủi ro pháp lý, mất niềm tin thị trường). |
| Failure mode | **AI Hallucination (Ảo giác AI)**: Tự tạo sinh nội dung sai sự thật (phương pháp điều trị tưởng tượng, ngôn từ bạo lực, phân biệt chủng tộc); **Tạo sinh không bám sát ngữ cảnh (Context drift / Unfaithful generation)** do cơ chế dự đoán xác suất chuỗi từ. |
| Layer bắt đầu lỗi | **Model Layer & Safety Layer**: Mô hình ASR nền tảng (Whisper) được thiết kế theo dạng Sequence-to-Sequence tạo sinh từ kế tiếp theo xác suất, nên khi gặp đoạn ngắt quãng hoặc âm thanh không rõ sẽ tự "điền khuyết" theo xác suất ngôn ngữ thay vì giữ đúng âm thanh thực tế. Đồng thời, hệ thống thiếu **Grounding Layer** (không kiểm tra chéo với thuật ngữ y khoa chuẩn) và thiếu **Safety Guardrails** (không có cảnh báo độ bất định hay bộ lọc chặn nội dung y tế ảo giác). |
| Harm xảy ra là gì? | - **Đã xảy ra**: Ghi nhận thực tế nhiều bản ghi bệnh án bị sai lệch nghiêm trọng chứa phương pháp điều trị bịa đặt và từ ngữ bạo lực/phân biệt chủng tộc; gây xói mòn lòng tin vào tính chính xác của hồ sơ bệnh án AI.<br>- **Nguy cơ (tiềm tàng)**: Bác sĩ áp dụng phương pháp điều trị tưởng tượng dẫn đến chẩn đoán sai (misdiagnosis), biến chứng bệnh lý, sốc thuốc hoặc tử vong cho người bệnh. |
| Harm lens | **Physical harm** (nguy cơ thương tật hoặc tử vong cho bệnh nhân do điều trị sai); **Psychological harm** (xúc phạm nhân phẩm do từ ngữ phân biệt chủng tộc/bạo lực); **Legal & Reputational harm** (kiện tụng sơ suất y khoa, khủng hoảng uy tín cơ sở y tế). |
| Severity | **Critical**: Trong môi trường y tế lâm sàng, bất kỳ sai lệch nào về phác đồ điều trị hay tên thuốc đều có thể dẫn đến hậu quả chết người hoặc thương tổn sức khỏe vĩnh viễn. |
| Scale | **Rộng**: Hơn 30.000 bác sĩ lâm sàng và 40 hệ thống y tế lớn tại Mỹ đang triển khai; tiềm ẩn nguy cơ tác động tới hàng trăm nghìn bệnh nhân mỗi ngày. |
| Probability | **High**: Dựa trên thử nghiệm thực tế của ĐH Michigan (8/10 bản ghi âm xuất hiện ảo giác); trong bối cảnh bác sĩ quá tải công việc, khả năng lỗi bị bỏ sót là rất cao. |
| Frequency | **Thường xuyên (Frequent)**: Các chuyên gia và kỹ sư khẳng định tần suất xảy ra ảo giác của Whisper cao bất thường so với mọi công cụ nhận dạng giọng nói khác. |
| Vì sao? | Đánh giá dựa trên số liệu thực nghiệm từ Đại học Michigan (tỷ lệ 80%), báo cáo điều tra từ AP News, cảnh báo chính thức từ cựu kỹ sư OpenAI và giáo sư Đại học Princeton. Mô hình ASR tạo sinh chưa được tinh chỉnh chuyên sâu cho y tế không đáp ứng được yêu cầu về độ chính xác tuyệt đối trong môi trường rủi ro cao. |

### 3. Case study 2 — Chatbot Tessa của NEDA trong tư vấn rối loạn ăn uống (Eating Disorder Helpline)

#### Brief Case

- Tổ chức / sản phẩm AI: Hiệp hội Rối loạn Ăn uống Quốc gia Hoa Kỳ (National Eating Disorders Association - NEDA) — Chatbot AI mang tên "Tessa" (chạy chương trình Body Positive).
- Thời gian, địa điểm / bối cảnh: Cuối tháng 5 đến đầu tháng 6 năm 2023 tại Hoa Kỳ; bối cảnh NEDA quyết định cắt giảm đội ngũ nhân sự con người vận hành đường dây nóng truyền thống để thay thế hoàn toàn bằng chatbot AI tự động.
- AI được dùng để làm gì: Đóng vai trò là trợ lý sức khỏe tinh thần / chatbot tư vấn trực tuyến trên đường dây nóng (helpline) nhằm tiếp nhận, lắng nghe và cung cấp lời khuyên can thiệp tâm lý ban đầu cho những người mắc hoặc có nguy cơ mắc chứng rối loạn ăn uống (eating disorders).
- Vấn đề hoặc sự kiện đáng chú ý:
  + Chatbot Tessa đã đưa ra những lời khuyên nguy hại nghiêm trọng, đi ngược hoàn toàn với nguyên tắc điều trị rối loạn ăn uống: khuyên người bệnh đếm calo (counting calories), cân định kỳ hàng tuần, đo độ dày mỡ cơ thể bằng thước kẹp (measuring body fat with calipers), và đặt mục tiêu giảm từ 1 - 2 pound mỗi tuần. Đối với người mắc chứng rối loạn ăn uống, đây chính là những hành vi kích hoạt (triggers) căn bệnh bùng phát hoặc trở nên tồi tệ hơn.
  + Nhà hoạt động Sharon Maxwell phát hiện và công khai bằng chứng tương tác trên Instagram; sau đó chuyên gia tâm lý Alexis Conason đã kiểm tra độc lập và ghi nhận kết quả sai lệch tương tự.
  + Ban đầu NEDA phủ nhận thông tin, nhưng sau khi ảnh chụp màn hình bằng chứng được công bố rộng rãi, NEDA đã phải xóa bài đính chính và lập tức đình chỉ vô thời hạn hoạt động của chatbot Tessa vào đầu tháng 6/2023.
- Số liệu có nguồn:
  + Quy mô cắt giảm nhân sự: NEDA triển khai chatbot Tessa nhằm thay thế 6 nhân viên toàn thời gian có trả lương và khoảng 200 tình nguyện viên vận hành đường dây nóng.
  + Số lượt cuộc gọi tiếp nhận: Đội ngũ nhân viên con người trước đó đã xử lý gần 70.000 cuộc gọi/yêu cầu trợ giúp mỗi năm (fielded nearly 70,000 calls last year).
  + Khuyến nghị giảm cân nguy hại: Bot hướng dẫn người dùng giảm từ 1 - 2 pound mỗi tuần (tương đương khoảng 0.45 - 0.9 kg/tuần) kèm theo các biện pháp siết cân độc hại.
  + Thử nghiệm lâm sàng trước đó: Nghiên cứu năm 2021 công bố trên Tạp chí Quốc tế về Rối loạn Ăn uống (International Journal of Eating Disorders) theo dõi hơn 700 tình nguyện viên từng cho thấy kết quả khả quan ở phạm vi thử nghiệm hẹp, nhưng hệ thống đã thất bại nặng nề khi đưa ra triển khai mở trên thực tế.
- Nguồn: "NEDA Suspends AI Chatbot for Giving Harmful Eating Disorder Advice" — Tác giả: Ryan Bailey — Đơn vị: Psychiatrist.com (The Journal of Clinical Psychiatry) — Ngày công bố: 05/06/2023 — URL: https://www.psychiatrist.com/news/neda-suspends-ai-chatbot-for-giving-harmful-eating-disorder-advice/
- Phân biệt bằng chứng và nhận định:
  + **Bằng chứng (Nguồn xác nhận)**: Chatbot Tessa đã trực tiếp khuyên người dùng giảm cân, đếm calo và đo mỡ bằng kẹp (chứng minh qua ảnh chụp màn hình của Sharon Maxwell và TS. Alexis Conason); NEDA sa thải nhân viên/tình nguyện viên để thay thế bằng bot; đường dây nóng từng phục vụ 70.000 lượt yêu cầu/năm; NEDA đã gỡ bỏ hoàn toàn chatbot Tessa sau làn sóng phản đối.
  + **Nhận định / Suy luận của tôi**: Nguồn tin chưa thống kê được chính xác số lượng bệnh nhân ẩn danh đã tiếp nhận và làm theo lời khuyên của Tessa trong những ngày bot chạy công khai. Tuy nhiên, tôi suy luận rằng việc NEDA vội vã triển khai AI xuất phát từ áp lực muốn cắt giảm chi phí và né tránh việc nhân viên thành lập công đoàn (dù lãnh đạo NEDA phủ nhận điều này), thể hiện sự thiếu trách nhiệm nghiêm trọng trong việc quản trị rủi ro AI y tế đối với nhóm người yếu thế.

#### Harm Map Worksheet

| Trường | Phân tích của tôi |
| --- | --- |
| High-risk moment | Thời điểm người bệnh đang trong cơn khủng hoảng tâm lý rối loạn ăn uống (chán ăn tâm thần, cuồng ăn) tìm đến đường dây nóng khẩn cấp để xin trợ giúp và nhận được lời khuyên ép cân, đếm calo từ chatbot. |
| Stakeholder bị ảnh hưởng | - **Trực tiếp**: Bệnh nhân rối loạn ăn uống (bị kích hoạt lại hành vi nhịn ăn cực đoan, tự ngược đãi bản thân, khủng hoảng tâm lý nặng hơn).<br>- **Gián tiếp**: 6 nhân viên và 200 tình nguyện viên bị sa thải; các chuyên gia trị liệu tâm lý; tổ chức NEDA (mất toàn bộ uy tín nghề nghiệp và niềm tin của cộng đồng). |
| Failure mode | **Goal Misalignment & Domain Failure**: Mô hình áp dụng kiến thức giảm cân phổ thông cho người bình thường vào nhóm đối tượng bệnh nhân tâm thần đặc thù (vốn tối kỵ việc kiểm soát cân nặng và calo); **Off-script Generation / Rule Violation**: Bot phá vỡ các nguyên tắc lâm sàng đã được quy định trong phác đồ điều trị rối loạn ăn uống. |
| Layer bắt đầu lỗi | **Safety Layer & Grounding Layer**: Thiếu hoàn toàn các rào chắn quy tắc (Safety Guardrails) để chặn các chủ đề và từ khóa độc hại đối với người rối loạn ăn uống (như giảm cân, đếm calo, đo mỡ); thiếu cơ chế kiểm chứng lâm sàng tự động (Clinical Grounding). Ngoài ra, tầng **Quản trị triển khai (Deployment Governance)** thất bại hoàn toàn khi loại bỏ sự giám sát của con người (Human-in-the-loop) trong dịch vụ can thiệp sức khỏe tâm thần khẩn cấp. |
| Harm xảy ra là gì? | - **Đã xảy ra**: Người dùng (như Sharon Maxwell) bị tái kích hoạt chấn thương tâm lý; kênh hỗ trợ khẩn cấp phục vụ 70.000 cuộc gọi/năm bị đình chỉ, khiến người bệnh mất chỗ dựa; tổ chức NEDA bị tẩy chay và khủng hoảng uy tín trầm trọng.<br>- **Nguy cơ (tiềm tàng)**: Người bệnh thực hiện theo hướng dẫn của bot dẫn đến suy kiệt cơ thể, rối loạn nhịp tim do nhịn ăn, trầm cảm nặng hoặc tự tử (rối loạn ăn uống là một trong những chứng bệnh tâm thần có tỷ lệ tử vong hàng đầu). |
| Harm lens | **Psychological harm** (sang chấn tâm lý nghiêm trọng, kích động hành vi rối loạn ăn uống); **Physical harm** (suy dinh dưỡng, biến chứng thể chất đe dọa sinh mạng do ép cân); **Reputational & Social harm** (làm suy giảm niềm tin xã hội vào các tổ chức hỗ trợ sức khỏe tinh thần). |
| Severity | **Critical**: Rối loạn ăn uống là căn bệnh có tỷ lệ tử vong rất cao trong các bệnh lý tâm thần; lời khuyên cổ xúy hành vi bệnh lý có thể đẩy bệnh nhân đến bờ vực nguy hiểm tính mạng. |
| Scale | **Lớn**: Đường dây nóng phục vụ gần 70.000 cuộc gọi mỗi năm; tác động trực tiếp lên toàn bộ cộng đồng bệnh nhân rối loạn ăn uống đang tìm kiếm sự hỗ trợ tại Mỹ. |
| Probability | **High**: Lỗi xảy ra ngay từ những tin nhắn đầu tiên khi người dùng tương tác, và được tái hiện dễ dàng bởi cả bệnh nhân lẫn bác sĩ chuyên khoa tâm lý độc lập. |
| Frequency | **Thường xuyên (Frequent)**: Lỗi xảy ra nhất quán trong phiên bản chatbot Tessa chạy chương trình Body Positive cho đến khi bị gỡ bỏ. |
| Vì sao? | Đánh giá dựa trên bằng chứng xác thực từ ảnh chụp màn hình đối thoại thực tế, kiểm chứng độc lập của TS. Alexis Conason, thông báo đình chỉ của NEDA và bài phân tích trên Tạp chí Tâm thần học Lâm sàng (The Journal of Clinical Psychiatry). Việc dùng chatbot AI thiếu kiểm soát để thay thế chuyên gia con người trong chăm sóc tâm lý khẩn cấp là một sai lầm nghiêm trọng về an toàn y tế. |
