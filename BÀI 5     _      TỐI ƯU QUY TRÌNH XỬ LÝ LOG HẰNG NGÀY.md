***BÀI 5     :      TỐI ƯU QUY TRÌNH XỬ LÝ LOG HẰNG NGÀY***

Phần 1: Thiết kế kiến trúc tệp PowerShell Script (.ps1)

Dựa trên bảng ánh xạ quy đổi cấu trúc từ .bat sang .ps1, quy trình xử lý log tự động được thiết kế mạch lạc thành 4 khối cấu trúc kỹ thuật như sau:

1. Khối Header Block (Thông tin cấu hình ẩn): Sử dụng ký tự \# để viết ghi chú (comment). Khối này chứa các siêu dữ liệu (metadata) của script như tên file, tác giả, ngày tạo để các kỹ sư DevOps khác dễ dàng bảo trì.  
2. Khối Khai báo biến cấu hình: Định nghĩa các đường dẫn tệp tin bằng các biến có ký hiệu gốc \$ (\$FileLogGoc, \$FileBaoCao). Việc gom đường dẫn vào biến ở đầu file giúp dễ dàng thay đổi cấu hình hệ thống về sau mà không cần sửa sâu vào mã nguồn logic.  
3. Khối Thông báo tiến trình: Sử dụng lệnh Write-Host kết hợp tham số màu sắc để in trạng thái xử lý trực tiếp ra màn hình Terminal giúp kỹ sư theo dõi trực quan, các dòng thông báo này hoàn toàn độc lập và không bị ghi lẫn vào file báo cáo.  
4. Khối Logic xử lý trung tâm: Thực hiện chuỗi hành động: Ghi khởi tạo tiêu đề \=== HE THONG TU DONG LOC LOG \=== vào file báo cáo trước, sau đó dùng đường ống Pipeline (Get-Content ➔ Select-String ➔ Add-Content) để quét dữ liệu, lọc mã lỗi 404 và xuất file báo cáo ở chuẩn mã hóa UTF-8.

Phần 2: Mã nguồn tệp auto\_report.ps1 hoàn chỉnh

Bạn hãy tạo một tệp tin đặt tên chính xác là auto\_report.ps1 và dán toàn bộ đoạn mã nguồn chuẩn mực dưới đây vào:

powershell  
 BLOCK 1: HEADER BLOCK (THÔNG TIN SCRIPT)  
 Tên kịch bản: auto\_report.ps1  
 Tác giả: Sinh viên PTIT \- Tech Lead DevOps  
 Ngày tạo: 13/10/2026  
Công dụng: Tự động hóa quy trình lọc lỗi 404 từ file Access Log

 BLOCK 2: KHAI BÁO BIẾN CẤU HÌNH (BIẾN BẮT ĐẦU BẰNG DẤU \$)  
\$FileLogGoc \= "access.log"  
\$FileBaoCao \= "daily\_404\_report.txt"

 BLOCK 3: THÔNG BÁO TIẾN TRÌNH KHỞI ĐỘNG (HIỂN THỊ TERMINAL)  
Write-Host ""  
Write-Host "------------------------------------------------" \-ForegroundColor Cyan  
Write-Host "\[\*\] KICH HOAT QUY TRINH XU LY LOG TU DONG..." \-ForegroundColor Green  
Write-Host "------------------------------------------------" \-ForegroundColor Cyan

 BLOCK 4: LOGIC XỬ LÝ TRUNG TÂM

 Bước 4.1: Khởi tạo file báo cáo mới và ghi dòng tiêu đề chuẩn (Header)  
 (Sử dụng Set-Content để làm sạch file cũ nếu có và ghi tiêu đề, ép định dạng utf8)  
\$TieuDeBaoCao \= "=== HE THONG TU DONG LOC LOG \==="  
Set-Content \-Path \$FileBaoCao \-Value \$TieuDeBaoCao \-Encoding utf8

 Bước 4.2: Đọc dữ liệu \-\> Lọc lỗi 404 qua Pipeline \-\> Ghi nối tiếp (Append) vào file báo cáo  
Write-Host "\[\>\] Dang quet va loc ma loi 404 tu file: \$FileLogGoc..." \-ForegroundColor Yellow

if (Test-Path \$FileLogGoc) {  
    \# Thực hiện luồng dữ liệu Pipeline tối ưu  
    Get-Content \-Path \$FileLogGoc | Select-String \-Pattern "404" | Add-Content \-Path \$FileBaoCao \-Encoding utf8  
      
    Thông báo hoàn thành thành công với giao diện chuyên nghiệp  
    Write-Host "\[OK\] Da loc xong\! Du lieu bao cao da duoc luu tai: \$FileBaoCao" \-ForegroundColor Green  
    Write-Host "------------------------------------------------" \-ForegroundColor Cyan  
} else {  
    Xử lý bẫy ngoại lệ nếu không tìm thấy file log gốc để hệ thống không bị crash  
    Write-Host "\[LÔI\] Khong tim thay file nguon \$FileLogGoc\! Vui long kiem tra lai." \-ForegroundColor Red  
    Write-Host "------------------------------------------------" \-ForegroundColo Cyan}

