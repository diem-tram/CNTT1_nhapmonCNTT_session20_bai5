# Bài 5 : Xây dựng cơ chế xác thực an toàn cho Crypto Wallet
## Kiến trúc sơ luồng
lớp 1 ( thứ bạn biết): tên đăng nhập,mật khẩu
lớp 2(thứ bạn có): mã TOTP
lớp 3(thứ bạn là): sinh trắc học, vân tay , face ID
## cơ chế phối hợp chống tấn công khi rò rỉ mật khẩu
- Nếu hacker có mật khẩu (lớp 1)Khi nhập xong mật khẩu, hệ thống lập tức chặn lại ở Lớp 2 và yêu cầu mã TOTP 6 chữ số. Hacker không có thiết bị vật lý chứa Secret Key nên không thể sinh mã TOTP hợp lệ.

- Nếu Hacker đánh cắp được Session / Token (Bypass Lớp 1 & 2): Khi thực hiện giao dịch chuyển tiền (Crypto Transfer), ứng dụng kích hoạt Lớp 3 yêu cầu xác nhận FaceID/Khóa phần cứng để lấy Private Key giải mã giao dịch. Hacker từ xa sẽ bị dừng lại hoàn toàn.
## Xử lý bẫy - Nếu người dùng đang ở trên máy bay (không có internet/sóng điện thoại), làm sao họ vẫn có thể lấy mã OTP để thực hiện giao dịch?
Thuật toán chạy hoàn toàn cục bộ (Client-side):

Ứng dụng Authenticator trên điện thoại không cần gọi API lên server để lấy mã.

Nó tự tính toán mã OTP dựa trên Khóa bí mật (Secret Key) đã lưu từ trước và Thời gian hiện tại của đồng hồ phần cứng điện thoại (
T) thông qua công thức toán học chuẩn RFC 6238:

TOTP=HMAC-SHA1(Secret Key,T)
Xác thực tại Server:

Khi người dùng nhập mã OTP đó vào ứng dụng (dù đang ngoại tuyến hay vừa có mạng trở lại để gửi giao dịch), máy chủ backend của ví sẽ dùng thời gian chuẩn của server để tính lại và đối chiếu.

Chính sách độ lệch thời gian (Time Skew Tolerance):

Vì đồng hồ trên điện thoại của người dùng có thể chạy lệch một vài giây so với đồng hồ của server khi không có mạng, hệ thống cấu hình cho phép chấp nhận mã OTP ở các bước thời gian lân cận (
±1 đến 
±2 khoảng 30 giây) để đảm bảo giao dịch không bị từ chối oan.
