# File Permissions, Process và Logs Cho DevOps

## Mục tiêu
Hiểu quyền file, quản lý process, service và log — các kỹ năng đụng hàng ngày khi vận hành hệ thống Linux.

---

## 1) File permissions

Linux có 3 loại quyền:
- `r` = read
- `w` = write
- `x` = execute

Và 3 nhóm đối tượng:
- `u` = user
- `g` = group
- `o` = others

### Xem quyền file
```bash
ls -l
```

Ví dụ:
```bash
-rwxr-xr-- 1 root root 1234 Mar 18 script.sh
```

Giải thích:
- `rwx` = owner được đọc / ghi / chạy
- `r-x` = group được đọc / chạy
- `r--` = others chỉ được đọc

### Đổi quyền bằng `chmod`
```bash
chmod +x script.sh
chmod u+x script.sh
chmod g-w file.txt
chmod o-r file.txt
```

### Đổi quyền bằng số
```bash
chmod 755 script.sh
chmod 644 file.txt
```

Ý nghĩa:
- `7` = rwx = 4 + 2 + 1
- `6` = rw-
- `5` = r-x
- `4` = r--

Ví dụ:
- `755`: owner full quyền, group/others được đọc + chạy
- `644`: owner đọc/ghi, group/others chỉ đọc

### Trường hợp hay gặp
- Script không chạy được → thiếu `+x`
- Config file thường để `644`
- SSH private key thường để:
```bash
chmod 600 ~/.ssh/id_rsa
```

### Đổi owner bằng `chown`
```bash
chown user:group file.txt
sudo chown -R nginx:nginx /var/www/app
```

---

## 2) Process management

Process là tiến trình đang chạy trong hệ thống.

### Xem process
```bash
ps aux
ps aux | grep nginx
```

### Xem realtime
```bash
top
```

Nếu có `htop`:
```bash
htop
```

### Dừng process
```bash
kill PID
kill -9 PID
```

- `kill`: dừng nhẹ nhàng
- `kill -9`: ép dừng

### Tìm PID nhanh
```bash
pgrep nginx
pidof nginx
```

### Chạy nền
```bash
python app.py &
```

### Xem job nền
```bash
jobs
```

---

## 3) Service management với `systemctl`

Các lệnh quan trọng:

```bash
systemctl status nginx
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl reload nginx
systemctl enable nginx
systemctl disable nginx
```

Ý nghĩa:
- `status`: xem trạng thái
- `start`: chạy service
- `stop`: dừng service
- `restart`: khởi động lại
- `reload`: nạp lại config
- `enable`: tự khởi động khi boot
- `disable`: tắt tự khởi động

---

## 4) Xem log với `journalctl`

```bash
journalctl -u nginx
journalctl -u nginx -f
journalctl -xe
```

- `-u nginx`: log của service nginx
- `-f`: follow realtime
- `-xe`: log lỗi gần đây

---

## 5) Làm việc với file log

### Xem log
```bash
cat app.log
less app.log
tail -f app.log
```

### Tìm lỗi
```bash
grep "error" app.log
grep -i "failed" app.log
```

### Kết hợp
```bash
tail -f app.log | grep error
```

---

## 6) Kiểm tra port và network

### Kiểm tra port đang listen
```bash
ss -tulpn
```

Hoặc:
```bash
netstat -tulpn
```

### Xem ai đang dùng port 8080
```bash
lsof -i :8080
```

### Gửi request HTTP để test nhanh
```bash
curl http://localhost:8080
curl -I https://example.com
```

---

## 7) Case thực chiến

### App không chạy
```bash
systemctl status myapp
journalctl -u myapp -f
```

### Port bị chiếm
```bash
lsof -i :3000
```

### Script không chạy
```bash
ls -l script.sh
chmod +x script.sh
```

### Server đầy disk
```bash
df -h
du -sh /var/log
```

### Nginx lỗi sau khi sửa config
```bash
nginx -t
systemctl reload nginx
journalctl -u nginx -n 50
```

---

## 8) Nhóm lệnh nên nhớ
- `chmod`
- `chown`
- `ps aux`
- `kill`
- `top`
- `systemctl`
- `journalctl`
- `tail -f`
- `grep`
- `ss -tulpn`
- `lsof -i :PORT`
- `curl`

---

## Ghi chú
Đây là nhóm kỹ năng rất thực chiến: khi app lỗi, service chết, port bị chiếm, disk đầy hoặc log báo đỏ, gần như lúc nào cũng phải quay về các lệnh trong bài này để chẩn đoán.
