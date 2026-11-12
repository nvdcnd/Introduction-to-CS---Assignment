# 🧭 HƯỚNG DẪN TỪNG CELL — HƯỚNG ĐI A: "KHOẢNG CÁCH ĐIỆN & SỐ HOÁ" (2000–2020)

> **Bản chất tài liệu này:** bản đồ chi tiết (blueprint) cho notebook của bạn — nói rõ
> *cell nào viết gì, chạy ra kết quả gì, diễn giải thế nào, bẫy ở đâu*.
> **Không có sẵn câu chữ trả lời và không có sẵn code.** Toàn bộ phần prose/code
> do bạn tự viết — đây là Individual Assessment (60% điểm môn).
>
> Ngày lập: 11/11/2026 · Hạn nộp: **11/12/2026 16:00**

---

## 0. VÌ SAO CHỌN HƯỚNG NÀY (và tại sao đây là hướng DỄ NHẤT)

**Trọng tâm câu chuyện:**
> *"Khoảng cách về tiếp cận điện & internet giữa các nhóm thu nhập đã thu hẹp hay mở rộng
> trong 20 năm 2000–2020? Ai là người cải thiện nhanh nhất, ai bị bỏ lại phía sau?"*

**5 chỉ số dùng cho cả bài (đã kiểm tra thực tế trong file):**

| # | Indicator Code | Tên | Độ phủ (217 nước thật) | Ghi chú nguồn |
|---|---|---|---|---|
| 1 | `EG.ELC.ACCS.ZS` | Access to electricity (% of population) | 87,6% | World Bank Global Electrification DB |
| 2 | `EG.CFT.ACCS.ZS` | Access to clean fuels for cooking (% of population) | 59,3% | WHO Global Health Observatory |
| 3 | `IT.NET.USER.ZS` | Individuals using the Internet (% of population) | 90,2% | ITU World Telecommunication Indicators |
| 4 | `SP.URB.TOTL.IN.ZS` | Urban population (% of total population) | 99,1% | UN Population Division |
| 5 | `NY.GDP.PCAP.CD` | GDP per capita (current US$) | 92,2% | World Bank / OECD national accounts |

**Lý do hướng này dễ nhất (5 lý do, đều đã kiểm chứng):**

1. **4/5 chỉ số là đơn vị %** → không phải hoà trộn đơn vị (%, US$, sq.km…) → không có lỗi phổ biến nhất của bài này.
2. **Độ phủ cao** (59–99%), dữ liệu 2000–2020 gần như đầy đủ → ít phải xử lý missing phức tạp.
3. **Câu chuyện trực quan, lập luận dễ viết** (ai nghèo, ai thiếu điện, ai bắt internet) → phần *justify* trong Descriptive Analytics không bị bí.
4. **Nguồn metadata có sẵn trong `SDGSeries.csv`** (Topic, Source) → trích dẫn Bibliography nhanh, đúng rule C4.
5. **Phù hợp đúng kỹ năng của môn**: groupby / merge / pivot / line–bar–scatter — không cần model phức tạp.

**5 chỉ số này cover đủ mọi chủ đề lớn của SDG** nên vẫn trả lời được yêu cầu *"cover material from all units"*:
Energy (Environment unit), Internet & Urbanisation (Infrastructure unit), GDP (Economics unit),
Income-group comparison (Social/Equality unit), nguồn WHO/ITU/WB (Data Sources unit).

---

## 1. KIỂM KÊ SỐ LIỆU "CHUẨN" — dùng để tự đối chiếu (không phải đáp án chép)

> ⚠️ **Đây là các con số tham chiếu để bạn kiểm tra code của mình có SAI không.**
> Bạn **vẫn phải tự chạy ra** và tự diễn giải. Nếu số của bạn lệch → quay lại soát lại
> phần lọc aggregate / lọc chỉ số / năm chọn sai.

**Cấu trúc file (đã xác minh):**
- `SDGData.csv`: **106.488 dòng × 36 cột** (4 cột định danh + 31 cột năm 1990–2020 + **1 cột tên rỗng ở cuối**)
- 106.488 = 261 thực thể × **408 mã chỉ số** (mã chỉ số trong data nhiều hơn `SDGSeries.csv` đúng 1 cái: `NE.CON.GOVT.KD`)
- **Tỉ lệ ô có dữ liệu = 1.326.219 / 3.301.128 = 40,2%** *(số 38,9% từng lưu ở file trước là do tính nhầm cả cột rỗng cuối bảng — số đúng là **40,2%**)*
- **Không có cặp (CountryCode, IndicatorCode) trùng lặp**: 0 dòng duplicate
- 261 thực thể = **217 quốc gia thật + 44 vùng/tổng hợp**
- 217 nước thật phân bổ: Europe & Central Asia 58 · Sub-Saharan Africa 48 · Latin America & Caribbean 42 · East Asia & Pacific 37 · Middle East & North Africa 21 · South Asia 8 · North America 3
- Nhóm thu nhập: High 80 · Lower middle 54 · Upper middle 54 · Low 28 · **để trống 1 (Venezuela – VEN)**

**Số đối chiếu nhanh (chỉ để sanity-check):**

| Kiểm tra | Giá trị tham chiếu |
|---|---|
| Điện, trung bình nhóm **Low income** | 2000 ≈ 15,9% → 2020 ≈ 40,5% |
| Điện, trung bình nhóm **High income** | 2000 ≈ 99,2% → 2020 ≈ 100% |
| Số nước có điện < 50% dân số | 2000: **52 nước** → 2020: **25 nước** |
| Điện toàn cầu (`WLD`) | 2000 ≈ 78,2% → 2020 ≈ 90,5% |
| Internet, trung bình đơn giản 2015 (200 nước) | ≈ 48,0% — nhưng giá trị `WLD` 2015 chỉ ≈ 40,5% (**vì World là weighted average, không phải trung bình cộng**) |
| Độ phủ dữ liệu internet | 2015: 200/217 → **2020 chỉ còn 151/217** (ITU ngừng cập nhật dần sau 2015) |
| `EG.CFT.ACCS.ZS` | **không có dữ liệu năm nào trước 2000** (bắt đầu đúng 2000) |
| Cải thiện điện mạnh nhất 2000→2020 | Afghanistan ≈ **+96,1 điểm %** |
| Suy giảm mạnh nhất 2000→2020 | Libya ≈ **−30,1 điểm %** (xung đột — câu chuyện hay cho discussion) |

---

## 2. LỊCH LÀM NGẮN (áp dụng từ hôm nay 11/11)

| Ngày | Xong việc |
|---|---|
| 12–13/11 | Đổi tên file + Cells #1–#13 (Introduction + Data Cleaning) |
| 14–17/11 | Cells #14–#22 (EDA, 3 quan sát) |
| 18–22/11 | Cells #23–#31 (3 câu Descriptive Analytics) |
| 23–25/11 | Cells #32–#34 (Suggestions + Bibliography) → **nộp bản nháp #1** |
| 26/11 – 5/12 | Mở rộng (hình đẹp hơn, thêm robustness check, viết lại prose) |
| 6–9/12 | Viết Executive Summary + Introduction cuối, Restart & Run All, nộp HTML |
| 10–11/12 | Nộp lại lần cuối trước 16:00 ngày 11/12 |

> 🎯 Mục tiêu: **bản HTML hoàn chỉnh trước 9/12**, những ngày còn lại để nâng chất.

---

## 3. SƠ ĐỒ NOTEBOOK (tổng ≈ 34 cells)

```
[Cell 0]   MD  : XOÁ cell hướng dẫn của template
─── Executive Summary ───        (1 MD, viết CUỐI CÙNG)
[1–3]      Introduction
[4–14]     Data Cleaning          ← nặng nhất, nhiều điểm nhất
[15–22]    Exploratory Data Analysis   ← ≥3 quan sát
[23–31]    Descriptive Analytics  ← 3 câu hỏi × 3 cell
[32–34]    Suggestions + Bibliography
```

**Quy tắc chung cho MỌI cell:**
- Mỗi **cell code** nên có **1 cell markdown đứng ngay trước** giải thích *"đang làm gì – vì sao làm"*.
- Mỗi cell code chỉ làm **1 việc**, in ra **bằng chứng** (shape trước/sau, số đếm, %) — examiner chấm cái này.
- Output phải **được giữ lại** sau khi chạy (không xoá output trước khi export HTML).

---

## 4. CHI TIẾT TỪNG CELL

### ▸ CELL 0 — MD — "Dọn template"
- **Làm:** xoá hẳn cell hướng dẫn đầu notebook (*"This is the report template…"*).
- **Kiểm tra:** notebook bắt đầu bằng heading `#` (tên báo cáo + số sinh viên).

---

### ▸ SECTION: EXECUTIVE SUMMARY (1 cell MD — viết SAU CÙNG)

### ▸ CELL 1 — MD — Executive Summary *(chưa viết vội)*
- **Quy tắc cứng:** **1 cell duy nhất, ≤ 200 từ.**
- **Cấu trúc gợi ý cho 200 từ:** ① bộ dữ liệu + phạm vi (217 nước, 2000–2020, 5 chỉ số) →
  ② 2–3 phát hiện chính bằng **con số** → ③ 1 câu về hàm ý chính sách.
- **Viết khi nào:** sau khi xong hết Descriptive Analytics.
- **Bẫy:** viết phần này trước → thường phải viết lại 2 lần.

---

### ▸ SECTION: INTRODUCTION (Cells 2–4)

### ▸ CELL 2 — MD — Introduction
- **≤ 200 từ**, 3 đoạn:
  1. **Bộ dữ liệu là gì** (SDG Indicators của World Bank, 1990–2020, tải từ đâu, format CSV 6 file).
  2. **Vì sao chọn đề tài này** (khoảng cách điện & internet = chủ đề SDG 7 & 9, dễ thấy bất bình đẳng).
  3. **Kỳ vọng phát hiện gì** (VD: kỳ vọng khoảng cách thu hẹp nhưng chưa đều…) — *kỳ vọng khác với kết quả thật cũng không sao, template nói rõ điều đó được chấp nhận*.
- **Bẫy:** đừng viết quá 200 từ; đừng liệt kê file ở đây (để sang Data Cleaning).

### ▸ CELL 3 — CODE — "Chốt phạm vi 5 chỉ số từ metadata"
- **Làm:** đọc `SDGSeries.csv` → lọc ra 5 mã chỉ số đã chọn → hiển thị các cột
  `Series Code / Topic / Indicator Name / Source` (hoặc `Periodicity`, `Aggregation method`).
- **Output mong đợi:** bảng 5 dòng — chứng minh bạn **chọn chỉ số có cơ sở**, không chọn bừa.
- **Bẫy:** cột `Unit of measure` trong `SDGSeries.csv` **trống rỗng** → đừng in ra rồi kết luận "không có đơn vị"; đơn vị hãy đọc từ `Indicator Name` (cả 4 cái đầu là "%").

### ▸ CELL 4 — MD — diễn giải bảng metadata
- **Viết:** ngắn gọn — mỗi chỉ số thuộc Topic gì, nguồn nào, tại sao 5 cái này ghép được thành 1 câu chuyện.
- **Bắt buộc:** nguồn (ITU/WHO/WB/UN) sẽ được nhắc lại ở **Bibliography** — ghi chú lại ngay đây (rule C4).

---

### ▸ SECTION: DATA CLEANING (Cells 5–16) — *phần quan trọng nhất của bài*

### ▸ CELL 5 — MD — kế hoạch làm sạch
- **Viết:** liệt kê **bullet các bước dự kiến** (6–7 bước) + lý do từng bước.
- Gợi ý thứ tự: đọc file → xử lý cột thừa → tách aggregate → melt wide→long →
  lọc chỉ số → phân tích missing → chốt cửa sổ thời gian → join metadata.
- **Vì sao cần cell này:** nó cho examiner thấy bạn **có kế hoạch**, không phải chọc ngoáy.

### ▸ CELL 6 — CODE — đọc `SDGData.csv` & khảo sát
- **Làm:** đọc file, in ra **shape**, danh sách cột, 2–3 dòng đầu, kiểu dữ liệu.
- **Output kỳ vọng:** `(106488, 36)`; cột cuối cùng **tên rỗng/`Unnamed: 35`**; 31 cột năm dạng chuỗi.

### ▸ CELL 7 — MD — giải thích phát hiện
- **Viết:** nêu 3 phát hiện: (a) dạng **wide**, (b) **1 cột thừa** cuối bảng do dấu phẩy,
  (c) các cột năm đang là **chuỗi (object)** → phải chuyển numeric nếu muốn tính.

### ▸ CELL 8 — CODE — xử lý cột thừa + kiểu dữ liệu
- **Làm:** xoá cột tên rỗng; ép 31 cột năm sang numeric (hoặc để đó và melt rồi convert);
  **in ra shape sau xử lý** → kỳ vọng `(106488, 35)`.
- **Bẫy:** `pd.to_numeric(..., errors='coerce')` — nếu bạn ép cả cột text thì sẽ thành NaN hàng loạt;
  chỉ ép **31 cột năm**.

### ▸ CELL 9 — CODE — đọc `SDGCountry.csv` & dựng cờ tổng hợp
- **Làm:** đọc file; dùng cột **`Region` rỗng/không rỗng** để tạo cờ *quốc gia thật vs vùng tổng hợp*;
  in số đếm từng nhóm và in **danh sách 44 tên tổng hợp**.
- **Output kỳ vọng:** `217` thật, `44` tổng hợp; danh sách có `World`, `European Union`, `OECD members`, `High income`…

### ▸ CELL 10 — MD — vì sao PHẢI lọc aggregate
- **Viết:** giải thích lỗi nghiêm trọng nhất của bài: nếu giữ `World` + `European Union` + các nước
  rồi tính trung bình → **đếm trùng dữ liệu**, mọi con số trung bình đều sai.
- **Bẫy đặc biệt (quan trọng):** tồn tại thực thể tổng hợp tên là **`East Asia & Pacific`**, trùng hệt tên
  một `Region` của quốc gia thật → **không được lọc theo tên**, phải lọc theo **`Country Code`**
  lấy từ file country (Region khác rỗng).

### ▸ CELL 11 — CODE — lọc quốc gia thật + 5 chỉ số, rồi melt
- **Làm:** ① giữ 217 nước thật (theo `Country Code`) → ② giữ 5 mã chỉ số →
  ③ **xoay wide → long** (cột năm thành cột `Year`, giá trị thành `Value`) → in shape trước/sau.
- **Output kỳ vọng:** wide `1.085 dòng` (217×5) → long `33.635 dòng` (1.085×31).
- **Công cụ gợi ý (tự viết thao tác):** `melt` / `pivot_table` / `merge`.

### ▸ CELL 12 — CODE — khảo sát missing (toàn bộ & subset)
- **Làm:** đếm % ô trống **theo từng năm** và **theo từng chỉ số**; trực quan hoá bằng bar/heatmap.
- **Kết quả cần thấy:** độ phủ tăng dần 1990→2017 rồi **rơi ở 2020**;
  `EG.CFT.ACCS.ZS` **trống hoàn toàn trước năm 2000**; internet sụt nguồn sau 2015.

### ▸ CELL 13 — CODE — kiểm tra chất lượng dữ liệu tổng quát
- **Làm:** (a) tìm dòng duplicate theo cặp `(CountryCode, IndicatorCode)` → **kỳ vọng 0**;
  (b) tìm chỉ số **rỗng 100%** → kỳ vọng đúng 4: `NE.CON.GOVT.KD`, `SL.ISV.IFRM.ZS`, `SI.POV.RUHC`, `SI.POV.URHC`;
  (c) check `NE.CON.GOVT.KD` **không có metadata** trong `SDGSeries.csv`;
  (d) check chỉ số % có giá trị ngoài [0,100] không.

### ▸ CELL 14 — MD — quyết định về missing *(phần được chấm nhiều)*
- **Viết rõ 4 điểm:**
  1. **Không impute** (không điền trung bình) — vì missing **không ngẫu nhiên** (thiếu nhiều ở nước nghèo).
  2. **Chốt cửa sổ thời gian 2000–2020**: vì `EG.CFT.ACCS.ZS` bắt đầu 2000, và độ phủ các năm 1990s thấp.
  3. **Internet dùng mốc 2015 (hoặc 2019)** làm năm cuối so sánh — vì độ phủ 2020 chỉ 151/217;
     ghi rõ điều này là **hạn chế dữ liệu**, không phải lỗi phân tích.
  4. **Venezuela (VEN) không có `Income Group`** → xử lý: gán nhãn "No income group reported" **hoặc**
     loại khỏi so sánh theo thu nhập kèm 1 câu giải thích (chọn 1 cách và nhất quán).

### ▸ CELL 15 — CODE — dựng bảng phân tích cuối
- **Làm:** tạo bảng chứa cho mỗi quốc gia: giá trị **năm 2000** và **năm 2015/2020** của từng chỉ số
  (+ cột Region, Income Group, tên nước); xử lý VEN theo quyết định ở Cell 14;
  in shape cuối, `describe()`, và đếm missing còn lại của bảng phân tích.
- **Bẫy:** năm chọn phải **nhất quán** cho cả 3 câu hỏi; đừng để bảng của Q1 dùng 2020 còn Q2 dùng 2019
  mà không giải thích.

### ▸ CELL 16 — MD — bảng tóm tắt "trước / sau" (bằng chứng chương trình hoá)
- **Viết:** bảng markdown liệt kê: số dòng, số cột, số thực thể, số chỉ số, số ô trống
  **tại mỗi bước** (sau khi đọc → sau khi drop cột → sau khi lọc aggregate → sau khi melt → bảng cuối).
- **Vì sao quan trọng:** template yêu cầu *"demonstrate how you have **programmatically** cleaned"* —
  bảng này chính là bằng chứng.

---

### ▸ SECTION: EXPLORATORY DATA ANALYSIS (Cells 17–24)

### ▸ CELL 17 — MD — tiêu đề + mô tả "bộ dữ liệu trông thế nào"
- **Viết:** 1 đoạn mô tả cấu trúc (217 nước × 5 chỉ số × 31 năm, 7 vùng, 4 nhóm thu nhập),
  độ phủ, đơn vị — **không nói kết luận**, chỉ mô tả.

### ▸ CELL 18 — CODE — hồ sơ dữ liệu (profile)
- **Làm:** bảng thống kê: số giá trị thiếu theo năm, theo chỉ số; số nước theo `Region`;
  số nước theo `Income Group` (nhớ VEN); `describe()` cho các cột giá trị.

### ▸ CELL 19 — MD — **QUAN SÁT 1**
- **Chủ đề gợi ý:** *"Dữ liệu thiếu không ngẫu nhiên"* — độ phủ tăng dần theo năm,
  rơi mạnh ở 2020, và `EG.CFT.ACCS.ZS` hoàn toàn trống trước 2000.
- **Quy tắc:** kèm **con số cụ thể** ngay trong văn bản (VD: % ô trống, số nước có dữ liệu 2020).

### ▸ CELL 20 — CODE — hình cho Quan sát 1
- **Làm:** biểu đồ cột/đường thể hiện số giá trị có dữ liệu theo năm (2 đường: toàn bộ vs 5 chỉ số chọn).

### ▸ CELL 21 — MD — **QUAN SÁT 2**
- **Chủ đề gợi ý:** *"Bất bình đẳng cơ sở hạ tầng đi theo nhóm thu nhập"* —
  xếp theo `Income Group` thấy thứ tự gần như cố định: High > Upper middle > Lower middle > Low.
- **Kèm hình (Cell 22):** boxplot hoặc bar trung bình theo Income Group cho 2 chỉ số điện + internet.

### ▸ CELL 22 — CODE — hình cho Quan sát 2

### ▸ CELL 23 — MD — **QUAN SÁT 3**
- **Chủ đề gợi ý:** *"Trung bình cộng ≠ giá trị World"* — vì `Aggregation method` của các chỉ số này là
  **Weighted average** (trọng số dân số), nên trung bình simple của 200 nước khác với con số `WLD`.
  Đây là quan sát **kỹ thuật, rất được điểm** vì nó cảnh báo sai lầm phân tích.
- **Kèm cell 24:** bảng đối chiếu trung bình đơn giản vs giá trị `WLD` cho 1–2 năm.

### ▸ CELL 24 — CODE — đối chiếu simple mean vs World

> ✅ **Check EDA:** đúng **3 quan sát**, mỗi quan sát = 1 MD + 1 CODE có output + hình/bảng chứng minh.

---

### ▸ SECTION: DESCRIPTIVE ANALYTICS (Cells 25–33) — *3 câu hỏi × 3 cell*
> **Mỗi câu hỏi bắt buộc đủ 3 phần theo template: ① câu hỏi → ② vì sao thú vị → ③ trả lời.**
> Phương án tổ chức cell: `MD(câu hỏi + justify)` → `CODE(chạy)` → `MD(trả lời)`.

### 🔹 CÂU HỎI 1 (Cells 25–27)
- **① Câu hỏi (MD):** *"Khoảng cách tiếp cận điện và internet giữa nhóm thu nhập cao nhất và thấp nhất
  là bao nhiêu, và nó khác nhau thế nào giữa các khu vực (Region)?"*
- **② Vì sao thú vị (cùng MD hoặc MD riêng):** đây là chỉ tiêu đo bất bình đẳng cơ sở hạ tầng (SDG 7 & 9);
  trả lời được "phân phối cơ hội tiếp cận" chứ không chỉ con số toàn cầu.
- **③ CODE:** bảng trung bình theo `Income Group` và theo `Region` cho **năm cuối đã chốt**;
  kèm biểu đồ cột có group. In luôn **số nước trong từng nhóm** (tránh trung bình trên 3 nước).
- **④ MD trả lời:** nêu **con số cụ thể** (VD: nhóm cao nhất ≈ …%, nhóm thấp nhất ≈ …%, chênh ≈ … điểm %)
  và **1 câu diễn giải**. Không viết chung chung kiểu "khoảng cách khá lớn".

### 🔹 CÂU HỎI 2 (Cells 28–30)
- **① Câu hỏi (MD):** *"Từ 2000 đến 2020 (hoặc 2015 với internet), khoảng cách đó THU HẸP hay MỞ RỘNG?
  Tốc độ thu hẹp có giống nhau giữa 3 chỉ số?"*
- **② Vì sao thú vị:** trả lời trực tiếp cho mục tiêu "không để ai bị bỏ lại" — số điểm % thu hẹp là kết quả
  policy-relevant, dễ kể chuyện.
- **③ CODE:** tính trung bình theo nhóm thu nhập tại **2 mốc năm** → bảng **gap (chênh lệch) năm 1 và năm 2**
  → biểu đồ **đường theo năm** cho từng nhóm (3 chỉ số, hoặc 1 hình/1 chỉ số).
- **④ MD trả lời:** nêu gap năm 1, gap năm 2, **độ thay đổi theo điểm %**, và chỉ ra nhóm nào thu hẹp nhanh nhất.

### 🔹 CÂU HỎI 3 (Cells 31–33)
- **① Câu hỏi (MD):** *"Những quốc gia nào cải thiện nhanh nhất và những quốc gia nào thậm chí thụt lùi
  về tiếp cận điện trong 2000–2020? Điều gì giải thích cho nhóm thụt lùi?"*
- **② Vì sao thú vị:** chỉ số tổng quát (World/region) che mất **outlier quốc gia**; tìm người bị bỏ lại
  là bước tiếp theo tự nhiên sau Q1–Q2.
- **③ CODE:** tính **delta = giá trị năm cuối − giá trị 2000** cho từng nước → sort → top 10 cải thiện &
  bottom 5 thụt lùi → biểu đồ thanh ngang. (Gợi ý thêm 1 dòng check nhanh: so delta với GDP/người
  hoặc nhóm thu nhập để có 1 nhận xét tương quan đơn giản.)
- **④ MD trả lời:** nêu đích danh vài nước + con số delta; giải thích nhóm thụt lùi bằng **bối cảnh
  (xung đột, thiên tai)** — nếu bạn trích nguồn bên ngoài thì **phải ghi nguồn** (rule C4).

> ✅ **Check Descriptive:** đúng 3 câu, mỗi câu đủ ①②③, mỗi câu có **số cụ thể + hình**,
> và mỗi câu trả lời đúng câu hỏi đã đặt (không trả lời lệch sang câu khác).

---

### ▸ SECTION: SUGGESTIONS (Cells 34–35)

### ▸ CELL 34 — MD — các bước tiếp theo (không bắt buộc cài)
- **Đề xuất nên có (chọn 2–3):**
  1. **Nối dữ liệu ngoài:** `SDG Index (Our World in Data)` / `WDI` / `SDG Report` theo `CountryCode`
     → bổ sung variabile dân số, đầu tư điện để giải thích gap.
  2. **Mô hình hoá:** regression `GDP per capita (log) → access` để kiểm định xem thu nhập giải thích bao nhiêu %;
     hoặc dự báo giá trị còn thiếu (imputation có cơ sở) thay vì bỏ trống.
  3. **Dashboard/visual:** chuyển bảng thành dashboard Power BI/Tableau cho bên ra quyết định.
- **Vì sao được gợi ý:** dùng chính **hạn chế đã phát hiện** (missing không ngẫu nhiên, nguồn ITU hết sau 2015)
  làm luận cứ — thể hiện bạn **đọc thấu dữ liệu**.

### ▸ CELL 35 — CODE (tùy chọn nhưng nên có) — minh hoạ tính khả thi
- **Làm:** 1 thí nghiệm nhỏ để chứng minh đề xuất khả thi, VD: **ma trận tương quan** giữa 5 chỉ số
  (hoặc scatter `log(GDP)` vs `access` kèm đường xu hướng).
- **Output:** 1 bảng/1 hình → nối với câu "mô hình hoá ở bước sau sẽ khả thi vì …".

### ▸ CELL 36 — MD — Bibliography
- **Bắt buộc có:** World Bank SDG dataset (nơi tải, ngày tải), `SDGSeries.csv` (nguồn từng chỉ số:
  ITU, WHO GHO, UN Population Division, WB national accounts), tài liệu pandas/matplotlib,
  **mọi nguồn bạn đọc** (blog, Stack Overflow, docs, AI…).
- **Quy tắc:** ① ghi nguồn **ngay chỗ dùng** (chú thích dưới hình/mục) + ② liệt kê ở đây;
  ③ nếu **dùng lại code của nguồn ngoài** → thêm 1 câu mô tả *"đã chỉnh gì cho khớp dữ liệu của mình"* (rule C5).

---

### ▸ SECTION: HOÀN TẤT (không cần cell mới)

**Checklist 10 phút cuối:**
1. ✅ Tên file: `ZZZZZZZ_M3_Aut22_TheAssignment.ipynb`
2. ✅ Cell hướng dẫn đầu đã xoá
3. ✅ Executive Summary ≤ 200 từ, Introduction ≤ 200 từ, **viết sau cùng**
4. ✅ `Kernel → Restart & Run All` → **không cell lỗi**, mọi cell có output
5. ✅ Không cell markdown nào đang trong chế độ edit
6. ✅ Save → `File → Download as HTML` (cùng tên, `.html`)
7. ✅ Upload lên Canvas → **kiểm tra file hiện trong danh sách nộp**
8. ✅ Nộp bản nháp sớm, trước **11/12/2026 16:00**, ideally trước 9/12

---

## 5. BẢNG BẪY — tick khi đã xử lý xong

- [ ] Cột thừa cuối `SDGData.csv` (36 cột → 35 cột)
- [ ] Lọc aggregate **theo `Country Code`**, KHÔNG theo tên (bẫy `East Asia & Pacific` trùng tên Region)
- [ ] 44 aggregate bị loại, giữ đúng **217** nước
- [ ] 4 chỉ số rỗng 100% bị phát hiện & báo cáo
- [ ] `NE.CON.GOVT.KD` thiếu metadata → được nhắc trong báo cáo
- [ ] `EG.CFT.ACCS.ZS` không có dữ liệu trước 2000 → chốt cửa sổ 2000–2020
- [ ] Internet có nguồn dữ liệu mỏng sau 2015 (151/217 ở 2020) → chọn mốc năm & ghi chú
- [ ] Venezuela không có `Income Group` → xử lý nhất quán + giải thích
- [ ] Không trung bình cộng lẫn lộn đơn vị (% vs US$) — `NY.GDP.PCAP.CD` để riêng, dùng log nếu vẽ scatter
- [ ] Không trình bày **trung bình simple** như thể nó là con số `World` (weighted average)
- [ ] Số liệu 2020 chịu ảnh hưởng COVID → disclaimer + trích `SDGSeries-Time.csv`
- [ ] Mọi nguồn ngoài được trích ở chỗ dùng **và** trong Bibliography

---

## 6. 5 LỖI THƯỜNG GÂY MẤT ĐIỂM NHẤT (tránh ngay từ đầu)

1. **Viết Introduction/Executive Summary trước** → phải viết lại khi có kết quả.
2. **Không có "vì sao thú vị"** cho mỗi câu hỏi descriptive → mất ngay 1 phần rubric.
3. **Trả lời chung chung** ("khoảng cách lớn", "cải thiện đáng kể") thay vì **con số + đơn vị**.
4. **Quên trích nguồn** dù chỉ 1 dòng copy → vi phạm rule C4/C5 (điểm rèn luyện học thuật).
5. **Không Restart & Run All** trước khi export HTML → HTML có cell lỗi / thiếu output.

---

*File này là cẩm nang lộ trình — mọi câu chữ trong notebook phải do bạn viết.*
*Cần tư vấn thêm phần nào (cách viết justify, cách chọn hình, cách xử lý VEN…) → hỏi lại.*
