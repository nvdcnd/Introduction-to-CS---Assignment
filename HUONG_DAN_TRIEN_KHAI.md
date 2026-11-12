# 📌 HƯỚNG DẪN TRIỂN KHAI ASSIGNMENT — CSCU9M3 (Aut22 template)

> Tài liệu này **chỉ là cẩm nang gợi ý**: đọc, lên kế hoạch, tự làm.
> Không có code mẫu, không làm hộ. Mọi cell trong notebook **phải do bạn viết và hiểu**.

---

## 0. TỔNG QUAN "1 TRANG"

| Hạng mục | Thông tin |
|---|---|
| **Bắt đầu** | 5/11/2026 |
| **Hạn nộp** | **11/12/2026 lúc 16:00** (giữ chỗ 1–2 ngày trước hạn để submit thử) |
| **Trọng số** | **60% điểm cuối cùng của môn** |
| **Phạm vi** | Bao trùm **tất cả units** của môn |
| **Bản chất** | **Individual Assessment** — làm việc một mình |
| **Thời lượng còn lại** | ~5 tuần (từ 10/11) → chia làm 5 giai đoạn xem §4 |
| **Sản phẩm nộp** | 1 file `.ipynb` (đổi tên đúng) + **1 file `.html` tải từ notebook, upload lên Canvas** |
| **Quyền nộp lại** | Nộp bao nhiêu lần tuỳ thích từ 5/11 → 11/12, **chỉ lần cuối được chấm** |

### 5 quy tắc vàng về Academic Integrity (A–E của trang thông tin)
1. **Làm một mình** — không trao đổi bài với bất kỳ ai ngoài giảng viên của môn.
2. **Không hỏi bài** với bạn bè / forum / AI ở chế độ "giải hộ".
3. Dùng **mọi nguồn** (online, sách, tài liệu) được — miễn không vi phạm 1 & 2.
4. **Mọi nguồn dùng được, dù nhỏ, phải trích dẫn** đúng chỗ dùng **VÀ** trong `## Bibliography`.
5. **Dùng lại code của nguồn ngoài** → ngoài mục 4, phải **giải thích ngắn gọn bạn đã chỉnh gì** để code hợp với bài của mình.

> ⚠️ Hệ quả thực tế: mọi snippet bạn copy từ Stack Overflow / blog / docs / AI
> → **① ghi chú ngay lúc dùng, ② ghi nguồn vào Bibliography, ③ viết 1–2 câu "đã chỉnh gì".**
> Đây là lỗi mất điểm phổ biến nhất, không phải lỗi thiếu code.

---

## 1. QUY TRÌNH 7 ĐOẠN NGẮN TRONG NOTEBOOK

Template yêu cầu **đúng thứ tự heading này**. Cell hướng dẫn ở đầu notebook **phải xoá** trước khi nộp.

```
# (xoá cell hướng dẫn đầu tiên)
## Executive Summary      ← 1 cell markdown DUY NHẤT, ≤ 200 từ
## Introduction           ← ≤ 200 từ
## Data Cleaning          ← code + markdown, giải thích WHY mỗi bước
## Exploratory Data Analysis   ← ≥ 3 quan sát thú vị, có hình/số chứng minh
## Descriptive Analytics  ← 3 câu hỏi, mỗi câu 1 subsection
## Suggestions            ← đề xuất (không bắt buộc cài đặt)
## Bibliography           ← mọi nguồn đã dùng
```

**Quy tắc "kể chuyện" của môn:** trộn code + markdown để kể **1 data story**.
Mỗi phần (trừ Executive Summary) phải có **nhiều cell**, không phải một cell khổng lồ.
Mỗi khối code nên có 1 dòng markdown đứng trước/nữa giải thích: *đang làm gì, vì sao làm*.

---

## 2. DỮ LIỆU BẠN ĐANG CÓ (đã kiểm kê sẵn — không cần đếm lại từ đầu)

**Nguồn:** World Bank — *Sustainable Development Goals Indicators*, giai đoạn **1990–2020**.

| File | Kích thước | Vai trò trong bài |
|---|---|---|
| `Dataset/SDGData.csv` | 37 MB, 106.488 dòng | **Bảng chính** (wide): `Country Name, Country Code, Indicator Name, Indicator Code, 1990…2020` |
| `Dataset/SDGSeries.csv` | 407 chỉ số (file nhiều dòng vì ô định nghĩa chứa ngắt dòng) | Metadata chỉ số: `Topic`, định nghĩa, unit, periodicity, aggregation, source |
| `Dataset/SDGCountry.csv` | 261 dòng | Metadata quốc gia: **`Region`, `Income Group`** ← kho để groupby |
| `Dataset/SDGCountry-Series.csv` | 1.611 dòng | Nguồn đặc thù theo từng cặp (country, series) → dùng cho Bibliography / giải thích missing |
| `Dataset/SDGFootNote.csv` | 160.540 dòng | Ghi chú theo (country, series, year) |
| `Dataset/SDGSeries-Time.csv` | 84 dòng | Cảnh báo về năm (VD: *"ước tính 2020 không tính tác động COVID"*) → vật liệu cho **Suggestions/limitations** |
| `SDG_csv.zip` | 12 MB | Bản zip gốc — **không cần đụng**, đã giải nén rồi |

**Số liệu nền đã kiểm chứng (dùng luôn cho phần EDA, khỏi tính lại):**

- **106.488 dòng** = 261 thực thể × **408 mã chỉ số**; **1990–2020 = 31 năm**
- **261 thực thể = 217 quốc gia/thực thể thật + 44 vùng/tổng hợp** (`Region` rỗng ⇒ là tổng hợp)
- **Tỉ lệ ô có dữ liệu = 40,2%** (1.326.219 / 3.301.128 ô, cột rỗng cuối bảng đã loại) → missing là quy luật
- Số giá trị/năm: 1990 ≈ 24.000 → 2017 ≈ 54.971 → **2020 rớt còn 34.858** (COVID)
- **4 chỉ số rỗng 100%**: `NE.CON.GOVT.KD`, `SL.ISV.IFRM.ZS`, `SI.POV.RUHC`, `SI.POV.URHC`
- `NE.CON.GOVT.KD` **không có metadata** trong `SDGSeries.csv` (408 mã dữ liệu vs 407 mã series)
- Chủ đề phổ biến nhất (đếm theo cột `Topic` trong `SDGSeries.csv`): Health (86), Education (82), Social Protection & Labor (55), Environment (52), Economic Policy & Debt, Poverty, Gender…

---

## 3. CHIẾN LƯỢC CHỌN ĐỀ TÀI — việc QUYẾT ĐỊNH chất lượng

**Mistake lớn nhất:** cố phân tích cả 408 chỉ số → mỏng, chung chung, hết thời gian.
**Cách tốt nhất:** gói bài vào **1 chủ đề hẹp, 1 câu chuyện**, chỉ chọn **6–10 chỉ số** liên quan.

Ba hướng đề xuất (chọn 1, hoặc tự nghĩ hướng tương đương):

### 🅰 "Khoảng trống năng lượng & số hoá" *(dữ liệu dày nhất — làm nhanh)*
Chỉ số gợi ý: `EG.ELC.ACCS.ZS` (điện), `EG.CFT.ACCS.ZS` (nhiên liệu sạch), `IT.NET.USER.ZS` (internet), `SP.URB.TOTL.IN.ZS` (đô thị hoá), `NY.GDP.PCAP.CD` (GDP/người).
- *Câu hỏi 1:* Khu vực/nhóm thu nhập nào cách biệt lớn nhất về **tiếp cận điện & internet**? *(hợp với groupby `Region`/`Income Group`)*
- *Câu hỏi 2:* Khoảng cách High income ↔ Low income **mở rộng hay thu hẹp** từ 2000 → 2020? *(line chart 2 đường)*
- *Câu hỏi 3:* 10 quốc gia **cải thiện nhanh nhất** trong 2000–2020 là ai? *(sort theo delta)*

### 🅱 "Môi trường & tăng trưởng" *(sâu hơn, dễ trích nguồn)*
`AG.LND.FRST.K2` / `AG.LND.FRST.ZS` (rừng), `EN.ATM.CO2E.PC` (CO₂), `NY.GDP.FRST.RT.ZS` & `NY.GDP.TOTL.RT.ZS` (resource rents).
- Q1: Diện tích rừng thay đổi thế nào theo từng vùng 1990–2020?
- Q2: CO₂/người vs GDP/người — có giống nhau ở mọi nhóm thu nhập? *(scatter + màu theo Income Group)*
- Q3: Resource rents (% GDP) nhóm nghèo vs nhóm giàu?

### 🅲 "Giáo dục & y tế" *(gần SDG nhất, trích UNESCO/WHO dễ)*
`SE.PRM.DURS`, `SH.DYN.MORT` (trẻ dưới 5 tuổi), `SE.XPD.TOTL.GD.ZS`, `SH.STA.MMRT`.
- Q1: Country nào có tỉ lệ tử vong dưới 5 tuổi cải thiện mạnh nhất?
- Q2: So sánh chỉ số giáo dục theo `Income Group` (boxplot).
- Q3: Tương quan giữa chi tiêu y tế và kết quả — có nước "bất thường"?

> **Chọn xong đề tài, LƯU Ý ĐIỀU NÀY:** mỗi câu hỏi descriptive phải đủ 3 phần
> **① Câu hỏi → ② Vì sao câu hỏi này thú vị/interesting → ③ Trả lời bằng con số + biểu đồ.**
> Phần ② là phần hay bị bỏ qua và bị trừ điểm.

---

## 4. LỊCH TRÌNH 5 TUẦN (10/11 → 11/12/2026)

> Nguyên tắc: **hoàn thành từng section một, nộp bản nháp EARLY.**
> Không cần đẹp ngay — cần **nộp được bản chạy sạch sớm**, rồi nâng cấp.

### 🗓 Tuần 1 (10/11 – 16/11): Nền tảng + chọn đề tài
- [ ] **Đổi tên file** ngay: `ZZZZZZZ_M3_Aut22_TheAssignment.ipynb` (ZZZZZZZ = số sinh viên)
- [ ] Ghi ra giấy **đề tài + 6–10 chỉ số + 3 câu hỏi** (chỉ ghi ý, chưa code)
- [ ] Đọc `SDGSeries.csv` để **xác nhận Topic/unit/periodicity** của các chỉ số đã chọn
- [ ] Đọc `SDGCountry.csv` để hiểu `Region` / `Income Group`
- [ ] **Nộp bản nháp đầu tiên** (dù chỉ có Introduction) → làm quen Canvas, kiểm tra format upload

### 🗓 Tuần 2 (17/11 – 23/11): Data Cleaning (phần nặng nhất, nhiều điểm nhất)
- [ ] Load dữ liệu, ghi **shape trước/sau** vào markdown
- [ ] Xử lý các "bẫy" ở §5 (cột thừa, aggregate, 4 chỉ số rỗng, Year trong footnote…)
- [ ] Quyết định chiến lược missing: **đừng impute bừa** — thường chỉ drop/để trống + báo cáo %
- [ ] Kiểm tra duplicates, kiểu số, giá trị ngoài [0,100] với chỉ số %
- [ ] **Nộp bản nháp 2** (đã có Data Cleaning chạy sạch)

### 🗓 Tuần 3 (24/11 – 30/11): EDA — ≥ 3 quan sát
- [ ] Profile: shape, dtype, % missing theo năm / theo chỉ số (heatmap hoặc bar)
- [ ] Phân bố thực thể theo `Region` và `Income Group`
- [ ] **Rút đúng 3 quan sát "đáng ngạc nhiên"** — mỗi quan sát phải kèm **con số/hình chứng minh**
- [ ] Chuẩn bị sẵn 3–4 kiểu hình: line theo vùng, bar top-N, boxplot theo nhóm, scatter tương quan
- [ ] **Nộp bản nháp 3**

### 🗓 Tuần 4 (1/12 – 7/12): Descriptive Analytics (3 câu hỏi)
- [ ] Mỗi câu 1 subsection: *① hỏi ② justify ③ trả lời*
- [ ] Trả lời phải có **con số cụ thể** (VD: "tăng từ 62% lên 89%, +27 điểm %"), không chỉ "tăng đáng kể"
- [ ] Viết **Suggestions** (dùng `SDGSeries-Time.csv` làm luận cứ cho limitations + 1–2 ý mở rộng: nối WDI/Our World in Data/SDG Index, dự báo giá trị thiếu…)
- [ ] Viết **Bibliography** (dồn sớm, đừng để cuối — dễ bị trừ điểm vì quên)
- [ ] **Nộp bản nháp 4**

### 🗓 Tuần 5 (8/12 – 11/12): Đóng băng & nộp
- [ ] Viết **Executive Summary** (≤200 từ) và **Introduction** (≤200 từ) — **viết SAU CÙNG**, khi đã biết kết quả
- [ ] `Kernel → Restart & Run All` → **không cell nào lỗi**
- [ ] Kiểm tra không cell markdown nào đang trong chế độ edit
- [ ] Xoá cell hướng dẫn đầu notebook
- [ ] Save → `File → Download as HTML` (cùng tên, khác phần mở rộng)
- [ ] **Nộp trước 9/12** (mặc định cho bạn 2 ngày đệm) — nhưng **vẫn được nộp lại tới 11/12 16:00**

> 🎯 **Mốc "kịp nộp chất lượng tốt": bản HTML hoàn chỉnh ít nhất từ 7/12.**
> Sau đó mọi ngày còn lại là để **cải thiện điểm**, không phải để chạy đua.

---

## 5. CHECKLIST "BẪY DỮ LIỆU" — tick từng dòng khi xử lý xong

- [ ] Cột cuối của `SDGData.csv` **tên rỗng** (dấu phẩy thừa) → loại
- [ ] **44 aggregate** phải được đánh dấu/tách (`Region` rỗng) — nếu không, mọi trung bình đều sai vì đếm trùng "World" + các nước
- [ ] Melt wide (1990…2020) → long (Year, Value) nếu tiện phân tích
- [ ] **4 chỉ số rỗng 100%** → loại, ghi rõ vào phần Data Cleaning
- [ ] `NE.CON.GOVT.KD` thiếu metadata → join với `SDGSeries` sẽ loại nó ra, **phải báo cáo lại**
- [ ] `SDGFootNote.Year` dạng `"YR1990    "` (padding + tiền tố) → strip + bỏ "YR" nếu join
- [ ] Chỉ số % phải nằm trong [0,100] → check outlier
- [ ] Đơn vị khác nhau giữa các chỉ số (%, US$, sq.km, per 1.000) → **không bao giờ trung bình cộng lẫn lộn**; kiểm tra cột `Unit of measure` trong `SDGSeries`
- [ ] Số liệu 2020 bị ảnh hưởng COVID → ghi disclaimer, trích `SDGSeries-Time.csv`
- [ ] Ghi lại **số dòng/cột trước – sau** mỗi bước cleaning (đây là "bằng chứng chương trình hoá")

---

## 6. CHECKLIST CHẤT LƯỢNG TRƯỚC KHI NỘP

**Về nội dung**
- [ ] Executive Summary **≤ 200 từ, đúng 1 cell markdown**
- [ ] Introduction **≤ 200 từ**, nêu *vì sao chọn dataset này* + *kỳ vọng phát hiện gì*
- [ ] Data Cleaning chứng minh được việc làm sạch **bằng code** (không phải mô tả suông)
- [ ] EDA: mô tả bộ dữ liệu + **đúng 3 quan sát**, mỗi quan sát có hình/số
- [ ] Descriptive: **đúng 3 câu hỏi**, mỗi câu đủ ①②③
- [ ] Suggestions: có luận cứ, không cần cài đặt hoàn chỉnh
- [ ] Bibliography: **mọi nguồn** (kể cả docs, Stack Overflow, AI, bài giảng) + chú thích chỗ dùng

**Về kỹ thuật**
- [ ] Tên file đúng format `ZZZZZZZ_M3_Aut22_TheAssignment.ipynb`
- [ ] `Restart & Run All` chạy sạch từ trên xuống, **không lỗi, không warning đỏ**
- [ ] Output của mọi cell đều hiển thị (mọi cell code đều chạy được, không "(no output)")
- [ ] Không cell markdown đang edit
- [ ] Cell hướng dẫn đầu tiên đã xoá
- [ ] Tải HTML cùng tên → upload lên Canvas → **xác nhận upload thành công** (đừng chỉ tin "đã gửi")
- [ ] Nộp sớm hơn deadline 1–2 ngày

**Về điểm số (đây là 60% môn học)**
- [ ] Mỗi phần đều có **markdown giải thích WHY** — đây là điểm "kể chuyện"
- [ ] Có ít nhất 4–6 hình trực quan, có title/nhãn trục/legend rõ
- [ ] Kết luận trong mỗi phần **nêu con số**, không mơ hồ
- [ ] Executive Summary tóm tắt được phát hiện chính (viết cuối cùng)

---

## 7. NGUYÊN TẮC "LÀM NHANH NHƯNG VẪN ĐẠT"

1. **Section đi theo thứ tự template, nhưng Executive Summary & Introduction viết CUỐI CÙNG.**
2. **Nộp sớm, nộp nhiều** — quyền lợi duy nhất của bạn: chỉ bản cuối được chấm.
   Submit bản nháp sớm = test được pipeline nộp + không bị stress ngày cuối.
3. **Chủ đề hẹp > chủ đề rộng.** 6–10 chỉ số kể được câu chuyện hay hơn 408 chỉ số kể không nổi câu nào.
4. **"Bằng chứng chương trình hoá"** = in ra số dòng/cột/ô trước–sau. Examiners chấm cái này, không chấm cảm tính.
5. **Mọi thứ lấy từ ngoài đều phải trích** — dùng ghi chú ngay lúc dùng, đừng để cuối tuần mới nhớ.
6. **Không đụng đến ai** — mọi trao đổi chỉ với giảng viên của môn. Hỏi bạn = rủi ro vi phạm rule C1/C2.
7. **Giữ notebook luôn chạy được** — commit/save sau mỗi section hoàn thành, tránh "chạy lại là lỗi" ngày nộp.

---

## 8. MỐC THỜI GIAN TỐI THIỂU (đánh dấu lịch)

| Mốc | Việc phải xong | Trạng thái |
|---|---|---|
| 12/11 | Đổi tên file + chốt đề tài + 3 câu hỏi | ⬜ |
| 16/11 | Đọc metadata, chọn 6–10 chỉ số | ⬜ |
| 23/11 | **Data Cleaning chạy sạch** + nộp bản nháp 2 | ⬜ |
| 30/11 | **EDA + 3 quan sát** + nộp bản nháp 3 | ⬜ |
| 7/12 | **3 câu descriptive + Suggestions + Bibliography** | ⬜ |
| 9/12 | **Nộp bản gần-final (HTML)** | ⬜ |
| 11/12 trước 16:00 | Nộp lần cuối, xác nhận thành công trên Canvas | ⬜ |

---

*Chúc làm bài suôn sẻ. Mọi câu hỏi về nội dung file này (chọn đề tài, cách trả lời câu hỏi, cách xử lý missing…) hỏi lại để được tư vấn thêm — vẫn chỉ ở mức gợi ý.*
