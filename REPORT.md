1. Đầu tiên nhóm em không bắt đầu từ việc chọn model mà bắt đầu từ quyết định dataset phải phục vụ. Dataset của nhóm phục vụ hai quyết định: xác định sinh viên có mặt và phân loại hành vi quan sát được trong lớp. Vì hành vi có yếu tố thời gian nên đơn vị chính của nhóm là Person × Temporal Clip, thay vì chỉ gán một frame đơn lẻ.

Nhóm em cố tình không dùng những khái niệm như 'chăm chú' hay 'thiếu tập trung', vì đó là suy luận về trạng thái bên trong. Annotator chỉ được gán những hành vi có dấu hiệu quan sát được.

Group key là Session + Person. Điều này giúp ngăn cùng một sinh viên trong cùng buổi học lọt vào cả train và test.

Với trường hợp không đủ bằng chứng để phân biệt hành vi, annotator được abstain và đưa ca đó sang QC thay vì bắt buộc đoán

2.Ở bước 2, nhóm em không chọn mục tiêu là thu thật nhiều frame mà chọn dữ liệu theo session, person, source, environment và behavior. Vì nếu lấy 5.000 frame từ một buổi học thì số lượng lớn nhưng diversity rất thấp.

Hai nguồn chính là footage từ lớp học và video lớp học công khai. Với footage có sinh viên thật, permission và privacy là một gate trước khi đưa dữ liệu vào annotation.

Mỗi file có một dòng trong Data Ledger để truy được source, session, person, thời điểm và trạng thái quyền sử dụng. Nếu file có vấn đề, ví dụ chưa xác minh được permission, nhóm không xóa im lặng mà đưa vào quarantine.

Cuối bước 2, artifact của nhóm là raw data, ledger, permission record và quarantine list. Chỉ dữ liệu vượt qua gate mới được chuyển sang bước preparation
3. Sau collection, nhóm em không đưa raw data trực tiếp vào annotation mà thực hiện EDA trước để hiểu phân bố theo source, session, person và các trường hợp occlusion.

Với video, nhóm đặc biệt kiểm tra duplicate và near-duplicate. Similarity cao chỉ là tín hiệu để review chứ không tự động xóa.

Sau đó nhóm thực hiện privacy processing nhưng phải đảm bảo không làm mất tín hiệu cần thiết để nhận diện hành vi.

Quan trọng nhất là split. Nhóm không random từng frame vì các frame liên tiếp của cùng một sinh viên có thể lọt vào cả train và test. Group key được xác định từ session và person. Nếu mục tiêu là đánh giá khả năng tổng quát hóa sang sinh viên mới, nhóm sử dụng person-disjoint split

4. Đây là bước nhóm em coi là quan trọng nhất để kiểm soát label noise. Nhóm không định nghĩa hành vi bằng suy đoán như 'sinh viên đang tập trung' mà dùng observable evidence.

Ví dụ, cúi đầu chưa đủ để gán Phone vì sinh viên có thể đang ghi chép. Tương tự, một frame cúi đầu chưa đủ để gán Sleeping. Với Group Talk, chỉ quay sang nhìn bạn cũng chưa đủ.

Vì hành vi có tính thời gian, nếu hành vi chuyển trạng thái trong clip thì nhóm split clip tại điểm chuyển. Nếu bị che khuất hoặc evidence không đủ, annotator không được đoán mà gán Unclear và escalate.

Guideline được version hóa, có decision tree, edge cases và decision log. Gate của bước này là một annotator chưa tham gia dự án vẫn có thể đọc guideline và gán sample theo cùng một rule

5. Sau khi viết guideline, nhóm em không triển khai ngay cho toàn bộ dataset. Nhóm chạy pilot trên 20 đến 30 temporal clips, trong đó cố tình đưa cả ca dễ và ca khó.

Hai annotator gán độc lập, không nhìn kết quả của nhau. Nhóm không chỉ tính raw agreement mà còn xem confusion matrix, agreement theo class và nguyên nhân của từng disagreement.

Ví dụ nếu một người chọn Phone còn người kia chọn Unclear vì điện thoại bị che, đó không đơn thuần là lỗi của annotator mà có thể là thiếu rule trong guideline.

Vì vậy Pilot tạo feedback loop: disagreement → tìm nguyên nhân → sửa guideline → tạo reference set. Chỉ khi vượt gate đã đặt trước nhóm mới mở annotation hàng loạt

6.Ở bước 6, nhóm em chuyển sang production annotation. Team được phân vai rõ giữa annotator, reviewer, adjudicator và data owner.

Annotator không review chính batch của mình để giữ tính độc lập. Dataset được chia theo group key thay vì chia tùy ý từng frame, để sau này truy được lỗi và rework theo group.

Trước khi submit, annotator chạy self-check. Nếu evidence không đủ thì không được đoán mà phải escalate.

Nếu sử dụng AI prelabel, AI chỉ đóng vai trò đề xuất. Annotator vẫn phải xem evidence và có quyền accept, modify hoặc reject. Nhóm đặc biệt kiểm soát anchoring vì prelabel có thể khiến annotator vô thức đi theo dự đoán của AI.

Những edge case chưa có trong guideline được đưa vào decision log, adjudicator xử lý và cập nhật guideline version. Nhờ vậy quyết định không bị mất sau khi xử lý xong một case.

7.Ở bước QC, nhóm em tách hai loại sampling vì chúng có hai mục đích khác nhau. Random audit dùng để ước lượng chất lượng của toàn batch, còn risk queue dùng để tìm những case có nguy cơ lỗi cao.

Random audit được lấy bằng seed cố định để có thể tái hiện. Nhóm không lấy tỷ lệ lỗi trên risk queue rồi gọi đó là tỷ lệ lỗi của toàn batch.

Các lỗi cũng được phân loại riêng như label error, temporal error, missing annotation, schema error và lỗi xử lý Unclear. Mỗi tỷ lệ đều ghi rõ numerator và denominator.

Báo cáo không chỉ có một accuracy tổng thể mà có micro, macro, worst class và confusion matrix, đặc biệt theo dõi các class hiếm như Sleeping và Away.

Nếu phát hiện lỗi lặp lại, nhóm tìm root cause thay vì chỉ sửa từng clip. Nếu nguyên nhân là guideline thì cập nhật guideline và re-annotate đúng phạm vi bị ảnh hưởng, sau đó QC lại. Nếu batch không đạt gate thì không release

8. Ở bước Release, nhóm em không coi việc upload dataset là release. Release là đóng gói một phiên bản có thể tái hiện và audit được.

Release packet gồm data và labels, fixed split, guideline version, QC report và dataset card.

Đặc biệt dataset card phải ghi cả known limitations. Với Đ2, các limitation quan trọng gồm occlusion, ambiguity giữa Phone và note-taking, rare behaviors và giới hạn của camera cuối lớp.

Trước khi Data Owner ký, nhóm phải trả lời sáu câu hỏi bằng chính tài liệu trong packet. Nếu thiếu bằng chứng thì dataset chuyển sang HOLD, ghi rõ thiếu gì, ai bổ sung và thời hạn.

Như vậy người nhận dataset không cần dựa vào trí nhớ của nhóm mà có thể kiểm tra toàn bộ release dựa trên evidence.

9. Bước 9 là nơi dataset bắt đầu một vòng đời mới. Sau release, nhóm theo dõi cả dữ liệu thực tế và pattern lỗi.

Ví dụ camera mới có góc quay khác khiến occlusion tăng, hoặc Phone thường xuyên bị nhầm với note-taking. Monitoring cho biết vấn đề đang xảy ra, còn error analysis tìm root cause.

Quan trọng là không phải mọi lỗi đều quay về guideline. Nếu thiếu dữ liệu thì quay về collection, leakage thì quay về preparation, annotation error thì quay về annotation, QC miss thì quay về QC, còn objective thay đổi thì quay về Step 1.

Mỗi finding phải có lifecycle destination, owner và action cụ thể. Khi sửa xong, nhóm tạo dataset version mới như v1.1 thay vì overwrite v1.0.

Như vậy dataset thực sự là một sản phẩm có version và có feedback loop
