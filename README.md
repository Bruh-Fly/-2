# 01 — OBJECTIVE & SCOPE

### Dataset phục vụ quyết định nào?

**Mục tiêu:**
Xác định sinh viên **có mặt/vắng** và phân loại **hành vi quan sát được theo thời gian** để hỗ trợ giảng viên theo dõi tình trạng lớp học.

### Đơn vị dữ liệu

**Person × Temporal Clip**

* Person localization trên frame
* Behavior classification trên đoạn clip ngắn
* Mỗi hành vi gắn với một khoảng thời gian quan sát được

### 5 hành vi

`Listening` · `Phone` · `Sleeping` · `Group Talk` · `Away`

### Group Key

**Session + Person**

→ Không để cùng một sinh viên trong cùng buổi học xuất hiện ở cả Train và Test.

### In scope

* Sinh viên trong lớp
* 5 hành vi quan sát được
* Video từ camera cuối lớp

### Out of scope

* Cảm xúc / ý định
* "Mức độ chăm chỉ"
* Nhận diện danh tính thật
* Đánh giá chất lượng sinh viên

### Quy tắc an toàn

**Không chắc → Abstain / Escalate, không ép annotator đoán.**
# 02 — DATA COLLECTION & SELECTION

### Nguồn dữ liệu

**1. Classroom footage**

* Camera cuối lớp
* Đúng bối cảnh triển khai
* Có kiểm tra permission/consent trước annotation

**2. Public classroom videos**

* Bổ sung đa dạng bối cảnh
* Chỉ sử dụng nguồn có quyền sử dụng phù hợp

### Sampling strategy

Không tối ưu theo **số frame**, mà theo:

**Session · Person · Source · Environment · Behavior**

Đặc biệt chủ động tìm dữ liệu cho lớp hiếm:

`Sleeping` · `Away`

### Data Ledger — mỗi file một dòng

`File ID | Session | Person/Group | Source | Timestamp | Permission | Status`

### Privacy gate

`Raw → Permission check → Privacy processing → Eligible`

Nguồn/quyền chưa xác minh:

**→ QUARANTINE, không xoá im lặng**

### Artifact

**Raw data + Data Ledger + Permission/Consent record + Quarantine list**

### Gate

* Số lượng raw / eligible / quarantine / rejected cộng đúng
* 100% file có provenance
* Mọi nguồn đều có trạng thái quyền sử dụng
* Dữ liệu chưa đủ điều kiện không đi vào annotation
# 03 — DATA PREPARATION & PREPROCESSING

### Input

`Eligible raw data + Data Ledger`

### 1. EDA

Kiểm tra:

* Schema & metadata
* Phân bố theo source / session / person
* Rare behavior
* Occlusion
* Chất lượng hình ảnh/video

### 2. Duplicate & Near-Duplicate

Phát hiện:

* Duplicate file
* Frame/video gần trùng
* Chuỗi frame liên tiếp quá giống nhau

**Similarity cao → review, không tự động xoá.**

### 3. Privacy Processing

`Raw → Privacy processing → Annotation-ready`

Mọi biến đổi được ghi lại trong ledger.

**Không làm mất tín hiệu cần thiết để nhận diện hành vi.**

### 4. Group Split

**Group key: Session + Person**

Không chia random từng frame.

→ Chống cùng người/cùng session xuất hiện ở Train & Test.

### Evaluation policy

Nếu mục tiêu là tổng quát hóa sang **sinh viên chưa từng thấy**:

**Person-disjoint split**

### Artifact

**Eligible dataset + EDA report + Duplicate report + Fixed split + Group-key definition**

### Gate

**Không leakage · split tái hiện được · metadata đầy đủ · transformation có provenance**
# 04 — ANNOTATION GUIDELINE

### Nguyên tắc

**Label bằng observable evidence, không suy đoán ý định/trạng thái bên trong.**

### Behavior definitions

| Label        | Evidence chính                                                |
| ------------ | ------------------------------------------------------------- |
| `Listening`  | Ở vị trí học tập, không có evidence đủ mạnh của hành vi khác  |
| `Phone`      | Quan sát được phone + evidence đang tương tác/nhìn phone      |
| `Sleeping`   | Tư thế ngủ/gục được duy trì theo temporal rule                |
| `Group Talk` | Có evidence tương tác với người bên cạnh theo temporal rule   |
| `Away`       | Không còn ở vị trí học tập trong khoảng thời gian đủ xác nhận |

### Edge cases

**Cúi đầu ≠ Phone**
→ Có thể là ghi chép → cần evidence phone

**Một frame nhắm/cúi đầu ≠ Sleeping**
→ Cần temporal evidence

**Quay sang bạn ≠ Group Talk**
→ Cần evidence tương tác

**Đứng lên ≠ Away**
→ Phải xác định đã rời vị trí

**Occlusion nặng / evidence không đủ**
→ `UNCLEAR → ESCALATE`

### Temporal rule

Nếu hành vi thay đổi trong clip:

**→ Split clip tại điểm chuyển trạng thái**

### Artifact

**Guideline v1.0 + Schema + Decision Tree + Edge-case examples + Decision Log**

### Gate

**Annotator chưa tham gia dự án đọc guideline và gán được sample theo cùng một rule.**
# 05 — PILOT ANNOTATION

### Mục tiêu

**Kiểm chứng guideline trước khi annotation hàng loạt.**

### Sample

**20–30 temporal clips**

Cố tình bao gồm:

* Clear cases
* Rare behaviors
* Phone vs note-taking
* Sleeping vs head-down
* Group Talk
* Away
* Occlusion
* Behavior transition
* Unclear cases

### Quy trình

`Sample → Annotator A + Annotator B → Independent labels → Compare → Analyze disagreement`

**Hai annotator không xem kết quả của nhau.**

### Đo lường

* Raw agreement
* Agreement theo class
* Confusion matrix
* Disagreement reasons
* Worst/confusing cases

### Feedback loop

`Disagreement → Root cause → Guideline v1.1 → Reference Set`

**Không đạt gate → không mở batch thật.**

### Artifact

**Pilot report + Revised Guideline + Reference Set + Decision Log**

### Gate

* Agreement đạt threshold đặt trước
* Disagreement lặp lại đã thành rule
* Reference cases đã được chuẩn hóa
* Guideline version mới được chốt
# 06 — PRODUCTION ANNOTATION

### Input

**Guideline v1.x + Schema + Reference Set + Eligible Dataset + Batch Plan**

### Team 7 người

| Vai trò     | Trách nhiệm                     |
| ----------- | ------------------------------- |
| Annotators  | Gán nhãn độc lập theo guideline |
| Reviewer    | Kiểm batch của annotator khác   |
| Adjudicator | Xử lý disagreement / edge case  |
| Data Owner  | Chịu trách nhiệm cuối cùng      |

**Không để annotator tự review chính batch của mình.**

### Batch strategy

**Chia theo Group Key: Session + Person**

→ Truy được lỗi theo lô
→ Giữ được provenance
→ Dễ rework khi guideline thay đổi

### Annotator workflow

`Observe → Label → Self-check → Submit`

**Evidence không đủ → UNCLEAR → ESCALATE**

### AI Prelabel

`AI suggestion → Human verifies → Accept / Modify / Reject`

**AI output ≠ Ground Truth**

Cảnh giác **anchoring**: không để prelabel quyết định thay cho evidence.

### Decision Log

`Edge case → Adjudication → Rule update → Guideline version`

### Artifact

**Candidate labels + Escalation list + Annotation log + Decision log**

### Gate

**Đủ coverage · đúng schema · có audit trail · edge cases được xử lý · reviewer độc lập**
# 07 — QA / QC

### Input

**Candidate Labels + Reference Set + Guideline + Pre-defined QC Gate**

### Hai queue — hai mục đích

**RANDOM AUDIT**

→ Ước lượng chất lượng toàn batch

`Batch → Seeded Random Sample → Audit`

**RISK QUEUE**

→ Tìm lỗi có nguy cơ cao

`Occlusion / Rare class / Unclear / AI low-confidence / Previous disagreement`

**Không dùng risk queue để báo cáo lỗi toàn batch.**

### Sampling

Random audit phải có:

* Population
* Sample size
* Random seed
* Sampling rule

→ **Reproducible**

### Error taxonomy

* Label error
* Temporal error
* Missing annotation
* Schema error
* Unclear-handling error

Mỗi metric ghi rõ:

**Numerator / Denominator**

### Quality report

**Micro + Macro + Worst Class + Confusion Matrix**

Đặc biệt theo dõi:

`Sleeping · Away · Phone`

### Corrective Action

`Error → Root Cause → Fix → Re-annotate affected scope → Re-QC`

Root cause có thể là:

**Annotator / Guideline / Data / Objective**

### Artifact

**QC Report + Audit Sample + Confusion Matrix + Issue Log + Corrective Action**

### Gate

**PASS → Release**

**FAIL → Rework + Re-QC**
# 08 — RELEASE

### Input

**QC-passed dataset + Guideline + Fixed Split + QC Report + Limitations**

### Release Packet v1.0

**5 thành phần bắt buộc:**

1. **Data + Labels**
2. **Fixed Split**
3. **Guideline version**
4. **QC Report**
5. **Dataset Card**

### Traceability

Mỗi label phải truy được:

`Clip → Person / Session → Annotator → Guideline version → QC`

### Dataset Card

Ghi rõ:

* Objective / Intended use
* Data sources
* Annotation schema
* Split strategy
* Quality results
* Known limitations
* Privacy / usage constraints

### Pre-release checklist

**6 câu hỏi phải trả lời được bằng evidence:**

1. Dataset phục vụ quyết định nào?
2. FP hay FN đắt hơn?
3. Guideline xử lý edge case nào?
4. Group key & leakage prevention?
5. Quality đo bằng gì, mẫu bao nhiêu, ai đo?
6. Team cần bao nhiêu người, điểm dễ vỡ ở đâu?

### Release Gate

**Tất cả câu hỏi trả lời được bằng tài liệu + QC PASS + Data Owner ký.**

Thiếu evidence:

**→ HOLD, không release.**

### Artifact

**Release Packet v1.0 + Signed Dataset Card**
# 09 — MONITORING, ERROR ANALYSIS & FEEDBACK

### Input

**Release Packet + Real-world data + Operational/model errors**

### Monitoring

Theo dõi:

* Data distribution / drift
* Error rate theo class
* Error theo source / session / camera
* Rare behavior coverage
* Occlusion / image quality
* Model failure patterns

### Error Analysis

`Observed Error → Categorize → Root Cause`

Các root cause chính:

**Data · Guideline · Annotation · QC · Objective**

### Feedback Routing

| Finding                      | Lifecycle quay lại |
| ---------------------------- | ------------------ |
| Objective không còn phù hợp  | **Step 1**         |
| Thiếu / lệch dữ liệu         | **Step 2**         |
| Leakage / split issue        | **Step 3**         |
| Rule mơ hồ                   | **Step 4**         |
| Pilot chưa bao phủ edge case | **Step 5**         |
| Annotation error             | **Step 6**         |
| QC không phát hiện lỗi       | **Step 7**         |

### Action Log

Mỗi issue phải có:

**Issue → Root cause → Lifecycle step → Owner → Action → Status**

### Versioning

`v1.0 → Monitoring → Finding → Fix → Rework → QC → v1.1`

**Không overwrite release cũ.**

### Artifact

**Monitoring Report + Error Analysis + Feedback Log + Next-version Plan**

### Gate

**Mỗi finding có lifecycle destination + owner + action.**
