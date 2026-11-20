# Đề 8 · Đặt vé xem phim

> **Mã:** DE08 · **Độ khó:** 8/8 · **Vai trò:** 3 (khách, nhân viên rạp, quản lý rạp) · **Nền tảng:** App khách iOS / Android · Web quản lý rạp · App soát vé (thiết bị cầm tay)

Hệ thống đặt vé cho một rạp chiếu phim: quản lý rạp lập lịch chiếu và giá vé; khách chọn phim, suất, ghế theo thời gian thực, thêm bắp nước, thanh toán và nhận vé điện tử; nhân viên soát vé ở cửa phòng chiếu, kể cả khi mất mạng; khách tự đổi, huỷ vé; rạp huỷ suất và hoàn tiền tự động. Đây là đề có **tranh chấp tài nguyên thời gian thực** (hàng nghìn người cùng chọn một ghế), **đồng hồ giữ chỗ xuyên nhiều bước**, **hoạt động offline** và **ràng buộc pháp lý về độ tuổi**. Đề gồm **1 tính năng**, là **1 luồng** gồm 5 bước và các màn hình; tài liệu gồm 1 PRD và 1 SRS.

## Tính năng

| **Tính năng** | **Luồng** | **PRD** | **SRS** |
| --- | --- | --- | --- |
| Đặt vé xem phim | Lịch chiếu, phòng chiếu & giá vé → Chọn phim & suất → Chọn ghế & giữ chỗ → Bắp nước, thanh toán & vé điện tử → Soát vé, đổi & huỷ vé | [PRD](docs/PRD.md) | [SRS](docs/SRS.md) |

## Các bước và màn hình của luồng

| **Bước** | **Tên bước** | **Màn hình** | **Phạm vi** |
| --- | --- | --- | --- |
| Bước 1 | Lịch chiếu, phòng chiếu & giá vé | SCR-W01, SCR-W02, SCR-W03, SCR-W04 | Phim, sơ đồ ghế, khoá ghế, lưới lịch, kiểm tra chồng lấn, nhân bản, mở bán, bảng giá (Web quản lý) |
| Bước 2 | Chọn phim & suất | SCR-K01, SCR-K02, SCR-K03 | Đang chiếu, sắp chiếu, theo ngày, chi tiết phim, chọn suất, xác nhận độ tuổi, nhắc mở bán |
| Bước 3 | Chọn ghế & giữ chỗ | SCR-K04 | Sơ đồ ghế thời gian thực, giữ ghế khi chạm, đồng hồ 10 phút, ghế đôi, chọn nhanh, quy tắc ghế lẻ |
| Bước 4 | Bắp nước, thanh toán & vé điện tử | SCR-K05, SCR-K06, SCR-K07 | Combo, ví / thẻ / QR, gia hạn khi chờ cổng, hoàn tự động, vé QR offline, nhận bắp nước, nhắc giờ chiếu |
| Bước 5 | Soát vé, đổi & huỷ vé | SCR-S01, SCR-S02, SCR-K08, SCR-W05 | App soát vé, soát từng phần, kiểm tra tuổi, offline; khách đổi suất, huỷ; rạp huỷ suất |

Luồng: Bước 1 Lịch chiếu, phòng chiếu & giá vé → Bước 2 Chọn phim & suất → Bước 3 Chọn ghế & giữ chỗ → Bước 4 Bắp nước, thanh toán & vé điện tử → Bước 5 Soát vé, đổi & huỷ vé.

## Vòng đời đặt vé (dùng chung cả đề)

```mermaid
stateDiagram-v2
  [*] --> DANG_CHON_GHE : Chọn suất, Bước 2
  DANG_CHON_GHE --> GIU_CHO : Giữ ghế đầu tiên, đồng hồ 10 phút, Bước 3
  GIU_CHO --> CHO_THANH_TOAN : Qua bắp nước, bấm thanh toán, Bước 4
  GIU_CHO --> HET_HAN_GIU : Hết 10 phút, Bước 3
  CHO_THANH_TOAN --> GIU_CHO : Thanh toán thất bại, còn giờ
  CHO_THANH_TOAN --> HET_HAN_GIU : Hết giờ và hết gia hạn
  CHO_THANH_TOAN --> HIEU_LUC : Thanh toán thành công, vé điện tử, Bước 4
  HIEU_LUC --> DA_SU_DUNG_MOT_PHAN : Soát một phần, Bước 5
  DA_SU_DUNG_MOT_PHAN --> DA_SU_DUNG : Soát đủ
  HIEU_LUC --> DA_SU_DUNG : Soát đủ, Bước 5
  HIEU_LUC --> DA_DOI : Khách đổi suất, Bước 5
  HIEU_LUC --> DA_HUY : Khách huỷ hoặc rạp huỷ suất, Bước 5
  HIEU_LUC --> HET_HAN : Suất kết thúc, chưa soát
  HET_HAN_GIU --> [*]
  DA_SU_DUNG --> [*]
  DA_DOI --> [*]
  DA_HUY --> [*]
  HET_HAN --> [*]
```

Song song: **suất chiếu** có vòng đời `NHAP` → `MO_BAN` → `DANG_CHIEU` → `DA_CHIEU` hoặc `DA_HUY` (Bước 1); **mỗi ghế** của suất có trạng thái `TRONG`, `DANG_GIU`, `DA_BAN`, `KHONG_BAN` (Bước 3).

## Bản đồ màn hình

```mermaid
flowchart LR
  subgraph WEB["Web quản lý rạp"]
    W01["SCR-W01 Phim"]
    W02["SCR-W02 Phòng & sơ đồ ghế"]
    W03["SCR-W03 Lịch chiếu"]
    W04["SCR-W04 Bảng giá"]
    W05["SCR-W05 Huỷ suất"]
  end
  subgraph KH["App khách"]
    K01["SCR-K01 Trang chủ"]
    K02["SCR-K02 Chi tiết phim"]
    K03["SCR-K03 Chọn suất"]
    K04["SCR-K04 Chọn ghế"]
    K05["SCR-K05 Bắp nước"]
    K06["SCR-K06 Thanh toán"]
    K07["SCR-K07 Vé của tôi"]
    K08["SCR-K08 Đổi, huỷ vé"]
  end
  subgraph SV["App soát vé"]
    S01["SCR-S01 Soát vé, quầy bắp nước"]
    S02["SCR-S02 Kết quả soát"]
  end
  W01 --> W03
  W02 --> W03
  W04 --> W03
  W03 --> W05
  K01 --> K02 --> K03 --> K04 --> K05 --> K06 --> K07
  K01 --> K03
  K07 --> K08 --> K03
  S01 <--> S02
  W03 -.->|Mở bán| K03
  K04 -.->|Ghế thời gian thực| K04
  K07 -.->|QR vé| S01
  W05 -.->|Hoàn tiền, báo khách| K07
```

## Bối cảnh chung

Rạp có 6–10 phòng, 40–80 suất mỗi ngày. Ngày khởi chiếu phim hot, hàng nghìn người cùng mở một sơ đồ ghế; giờ cao điểm, 200 người vào một phòng trong 10 phút với mạng trong rạp chập chờn. Khách cần chọn được ghế đẹp, trả tiền nhanh, không bị trừ hai lần, vào phòng không kẹt. Rạp cần lịch không trùng, giá đúng, không bán trùng ghế, không để ghế lẻ, tuân thủ phân loại phim theo độ tuổi. Bài toán thiết kế chính là **giữ đúng một sự thật về ghế và vé** giữa nhiều người dùng, nhiều thiết bị, cả khi mất mạng.

| **Nhóm người dùng** | **Nhu cầu chính** |
| --- | --- |
| Khách (nhóm bạn, gia đình, người hâm mộ phim) | Chọn suất theo giờ rảnh, ghế liền nhau, bắp nước, vé dùng được offline, đổi khi bận |
| Nhân viên soát vé, quầy bắp nước | Quét nhanh, kết quả rõ, chạy khi mất mạng, kiểm tra tuổi |
| Quản lý rạp | Lập lịch nhanh, không chồng lấn, giá theo quy tắc, xử lý sự cố một lần |

Ngoài phạm vi cả đề: chuỗi nhiều rạp, bán vé tại quầy, tiền mặt, mã giảm giá, thẻ thành viên, điểm thưởng, quản lý kho bắp nước, đánh giá phim, ghế dành cho xe lăn, cổng soát tự động, hoá đơn VAT.

## Quy ước dùng chung

- Màn App khách mã `SCR-K..`, Web quản lý `SCR-W..`, App soát vé `SCR-S..`.
- Tiền tệ VND, không có số lẻ; giá làm tròn tới 1.000 ₫.
- Phân loại phim theo quy định phổ biến phim hiện hành: P, K, T13, T16, T18 (mốc tuổi là cấu hình).
- Mọi giờ là giờ Việt Nam; giờ kết thúc suất = giờ bắt đầu + 15 phút quảng cáo + thời lượng phim.
- Khách đăng nhập bằng số điện thoại và OTP theo luồng chuẩn, không mô tả trong bộ tài liệu này.
- Mã BR, MSG, UC đánh số liên tục trong cả đề: Bước 1 dùng BR-01–12, MSG-01–08, UC-01–03; Bước 2 dùng BR-13–21, MSG-09–14, UC-04–05; Bước 3 dùng BR-22–33, MSG-15–23, UC-06; Bước 4 dùng BR-34–45, MSG-24–33, UC-07–09; Bước 5 dùng BR-46–58, MSG-34–44, UC-10–12.

## Danh sách màn hình

| **Mã** | **Màn hình** | **Nền tảng** | **Mô tả chi tiết ở** |
| --- | --- | --- | --- |
| SCR-W01 | Phim | Web quản lý | Bước 1 |
| SCR-W02 | Phòng chiếu & sơ đồ ghế | Web quản lý | Bước 1 |
| SCR-W03 | Lịch chiếu | Web quản lý | Bước 1 |
| SCR-W04 | Bảng giá | Web quản lý | Bước 1 |
| SCR-K01 | Trang chủ | App khách | Bước 2 |
| SCR-K02 | Chi tiết phim | App khách | Bước 2 |
| SCR-K03 | Chọn suất chiếu | App khách | Bước 2 |
| SCR-K04 | Chọn ghế | App khách | Bước 3 |
| SCR-K05 | Bắp nước | App khách | Bước 4 |
| SCR-K06 | Thanh toán | App khách | Bước 4 |
| SCR-K07 | Vé của tôi & vé điện tử | App khách | Bước 4 |
| SCR-S01 | Soát vé (và chế độ Quầy bắp nước) | App soát vé | Bước 5 |
| SCR-S02 | Kết quả soát | App soát vé | Bước 5 |
| SCR-K08 | Đổi suất / Huỷ vé | App khách | Bước 5 |
| SCR-W05 | Huỷ suất chiếu | Web quản lý | Bước 5 |

## Cấu trúc repo

```text
de08-dat-ve-xem-phim/
├── README.md
└── docs/
    ├── PRD.md    Bối cảnh, phạm vi, luồng, use case theo từng bước
    └── SRS.md    Quy tắc, trạng thái, use case, màn hình, API, thông báo theo từng bước
```

## Cách dùng bộ tài liệu

1. Đọc README để nắm ba vòng đời: suất chiếu, ghế, đặt vé.
2. Đọc PRD để nắm toàn bộ tính năng và luồng, rồi đọc SRS theo từng bước của luồng. Thiết kế đủ **ba nền tảng**: Web quản lý (màn hình lớn, thao tác nhiều), App khách (điện thoại, đồng hồ giữ chỗ), App soát vé (một tay, môi trường tối, ồn).
3. Đặc biệt chú ý các tình huống tranh chấp và lỗi: hai người chọn cùng ghế, hết giờ giữ chỗ khi đang thanh toán, soát vé khi mất mạng, rạp huỷ suất.
4. Thiết kế màn hình theo đúng mã SCR, mã MSG trong SRS để dễ đối chiếu khi review.
5. Sơ đồ viết bằng Mermaid, xem trực tiếp trên GitHub hoặc trình xem Markdown có hỗ trợ Mermaid.
