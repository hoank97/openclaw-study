# Linux Commands Căn Bản Cho DevOps

## Mục tiêu
Nắm các lệnh Linux cơ bản để thao tác file, thư mục, log, process và system — nền móng bắt buộc trước khi đi sâu vào DevOps.

---

## 1) Điều hướng thư mục

### `pwd`
Xem thư mục hiện tại.

```bash
pwd
```

### `ls`
Liệt kê file / thư mục.

```bash
ls
ls -l
ls -la
```

- `ls`: hiện tên file
- `ls -l`: hiện chi tiết
- `ls -la`: hiện cả file ẩn

### `cd`
Di chuyển giữa các thư mục.

```bash
cd /etc
cd ~
cd ..
cd -
```

- `cd ~`: về home
- `cd ..`: lùi 1 cấp
- `cd -`: quay lại thư mục trước đó

---

## 2) Làm việc với file và thư mục

### `mkdir`
Tạo thư mục.

```bash
mkdir devops
mkdir -p project/app/logs
```

### `touch`
Tạo file rỗng.

```bash
touch note.txt
```

### `cp`
Copy file / thư mục.

```bash
cp a.txt b.txt
cp -r folder1 folder2
```

### `mv`
Di chuyển hoặc đổi tên file.

```bash
mv old.txt new.txt
mv app.txt /tmp/
```

### `rm`
Xóa file / thư mục.

```bash
rm file.txt
rm -r myfolder
```

> Cẩn thận với `rm`, đặc biệt khi dùng quyền cao.

---

## 3) Xem nội dung file

### `cat`
Xem nhanh nội dung file.

```bash
cat note.txt
```

### `less`
Xem file dài, cuộn được.

```bash
less /var/log/syslog
```

- Thoát bằng `q`

### `head` và `tail`
Xem đầu hoặc cuối file.

```bash
head -n 5 file.txt
tail -n 5 file.txt
tail -f app.log
```

- `tail -f`: theo dõi log realtime

---

## 4) In text và redirect

### `echo`

```bash
echo hello
echo hello > a.txt
echo world >> a.txt
```

- `>` ghi đè
- `>>` nối thêm

---

## 5) Tìm kiếm

### `grep`
Tìm text trong file.

```bash
grep "error" app.log
grep -i "warning" app.log
```

### `find`
Tìm file theo tên.

```bash
find . -name "*.log"
find /etc -name "nginx.conf"
```

---

## 6) Process cơ bản

### `ps`
Xem process đang chạy.

```bash
ps aux
ps aux | grep nginx
```

### `top`
Xem process realtime.

```bash
top
```

- Thoát bằng `q`

---

## 7) Dung lượng đĩa

### `df -h`
Xem dung lượng ổ đĩa.

```bash
df -h
```

### `du -sh`
Xem dung lượng thư mục.

```bash
du -sh .
du -sh /var/log
```

---

## 8) Quyền và quyền admin

### `chmod`
Đổi quyền file.

```bash
chmod +x script.sh
```

### `sudo`
Chạy lệnh với quyền admin.

```bash
sudo apt update
```

---

## 9) Nhóm lệnh cần nhớ trước
- `pwd`
- `ls`
- `cd`
- `mkdir`
- `touch`
- `cp`
- `mv`
- `rm`
- `cat`
- `less`
- `head`
- `tail`
- `grep`
- `find`
- `ps`
- `top`
- `df`
- `du`
- `chmod`
- `sudo`

---

## 10) Bài tập thực hành mini

```bash
mkdir linux-practice
cd linux-practice
touch file1.txt
mkdir logs
echo hello > file1.txt
cp file1.txt file2.txt
mv file2.txt logs/
find . -name "*.txt"
cat file1.txt
ls -la
pwd
```

---

## Ghi chú
Mục tiêu của giai đoạn này không phải thuộc lòng mọi option, mà là quen tay với command line, hiểu file system, và có thể tự tìm / đọc / sửa / chạy lệnh trên server Linux.
