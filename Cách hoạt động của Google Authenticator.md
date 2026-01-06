# Tác giả bài viết: Thanh Hoang
# Link bài viết: [facebook](https://www.facebook.com/thanhhm/posts/pfbid0qPxYTkD3RFLTxMn38hrhWPiUtD7aabgnAr1EkJCw9rNjzdma84z9e4obC1nLQwaLl?locale=vi_VN)

Các ứng dụng xác thực như Google Authenticator mà chúng ta dùng hằng ngày thực sự hoạt động thế nào (kể cả khi không có internet)?

Nhiều người nghĩ rằng các mã 2FA 6 chữ số được “gửi” về điện thoại, nhưng thực tế không phải vậy. Ứng dụng và website chỉ đơn giản là cùng thực hiện một phép tính và cho ra cùng một con số tại cùng một thời điểm.

Khi bạn quét mã QR để thiết lập 2FA, website âm thầm cung cấp cho ứng dụng một khóa bí mật, và cả hai phía đều lưu lại khóa này. Từ đó về sau, cứ mỗi 30 giây, cả hai bên sẽ tự biến “thời gian hiện tại + khóa bí mật” thành một mã 6 chữ số mới. Nếu mã bạn nhập trùng với mã mà máy chủ vừa tính ra, việc xác thực sẽ thành công.

Vì không có dữ liệu nào cần gửi về điện thoại, nên ứng dụng vẫn hoạt động bình thường ngay cả khi không có internet hay sóng di động. Đây cũng là lý do nó an toàn hơn SMS: không có tin nhắn để bị đánh cắp, và mã xác thực thì hết hạn rất nhanh.