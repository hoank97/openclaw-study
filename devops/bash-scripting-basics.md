# Bash Scripting Cơ Bản Cho DevOps

## Mục tiêu
Biết viết các script Bash đơn giản để tự động hóa thao tác lặp lại trong Linux và DevOps.

---

## 1) Bash script là gì?
Bash script là file chứa nhiều lệnh shell được chạy tự động theo thứ tự.

Ví dụ:

```bash
#!/bin/bash
echo "Hello Đại ca"
```

Chạy script:
```bash
chmod +x hello.sh
./hello.sh
```

---

## 2) Shebang

```bash
#!/bin/bash
```

Dòng này cho Linux biết script sẽ được chạy bằng Bash.

---

## 3) Biến

```bash
name="Hoa"
echo "Hello $name"
```

Lưu ý:
- Không có khoảng trắng quanh dấu `=`
- Dùng `$name` để lấy giá trị biến

---

## 4) Input từ user

```bash
#!/bin/bash
echo "Nhập tên:"
read name
echo "Xin chào $name"
```

---

## 5) Tham số dòng lệnh

```bash
#!/bin/bash
echo "File name: $1"
echo "Mode: $2"
```

Chạy:
```bash
./script.sh app.log production
```

Ý nghĩa:
- `$0`: tên script
- `$1`, `$2`: tham số đầu vào
- `$#`: số lượng tham số
- `$@`: toàn bộ tham số

---

## 6) Câu lệnh điều kiện `if`

```bash
#!/bin/bash
if [ -f /etc/passwd ]; then
  echo "File exists"
else
  echo "File not found"
fi
```

Một số điều kiện hay gặp:
- `-f file`: file tồn tại
- `-d dir`: thư mục tồn tại
- `-z str`: chuỗi rỗng
- `-n str`: chuỗi không rỗng

Ví dụ:
```bash
if [ "$name" = "Hoa" ]; then
  echo "Đúng tên rồi"
fi
```

---

## 7) So sánh số

```bash
a=10
b=5

if [ $a -gt $b ]; then
  echo "a lớn hơn b"
fi
```

Toán tử thường dùng:
- `-eq`: bằng
- `-ne`: khác
- `-gt`: lớn hơn
- `-lt`: nhỏ hơn
- `-ge`: lớn hơn hoặc bằng
- `-le`: nhỏ hơn hoặc bằng

---

## 8) Vòng lặp `for`

```bash
for i in 1 2 3 4 5
do
  echo "Number: $i"
done
```

Hoặc:
```bash
for file in *.log
do
  echo "Found: $file"
done
```

---

## 9) Vòng lặp `while`

```bash
count=1
while [ $count -le 5 ]
do
  echo "Count: $count"
  count=$((count + 1))
done
```

---

## 10) Function

```bash
hello() {
  echo "Hello Đại ca"
}

hello
```

Có tham số:
```bash
greet() {
  echo "Hello $1"
}

greet Hoa
```

---

## 11) Exit code

Trong Linux:
- `0` = thành công
- khác `0` = lỗi

Ví dụ:
```bash
#!/bin/bash
if [ -f /etc/passwd ]; then
  echo "OK"
  exit 0
else
  echo "FAIL"
  exit 1
fi
```

Exit code rất quan trọng trong automation và CI/CD.

---

## 12) Kiểm tra lệnh thành công hay không

```bash
mkdir test
if [ $? -eq 0 ]; then
  echo "Tạo thư mục thành công"
else
  echo "Có lỗi"
fi
```

Cách gọn hơn:
```bash
if mkdir test; then
  echo "Success"
else
  echo "Failed"
fi
```

---

## 13) Redirect output

```bash
echo "hello" > file.txt
echo "world" >> file.txt
ls /abc 2> error.log
```

- `>` ghi đè
- `>>` ghi thêm
- `2>` ghi stderr vào file

---

## 14) Pipe

```bash
ps aux | grep nginx
cat app.log | grep error
```

Ví dụ thực tế:
```bash
tail -f app.log | grep 500
```

---

## 15) Command substitution

```bash
now=$(date)
echo "Current time: $now"
```

---

## 16) Script thực tế đơn giản

### Kiểm tra file tồn tại
```bash
#!/bin/bash

FILE=$1

if [ -f "$FILE" ]; then
  echo "File exists: $FILE"
else
  echo "File not found: $FILE"
fi
```

### Backup file
```bash
#!/bin/bash

SRC=$1
DEST="${SRC}.bak"

cp "$SRC" "$DEST"
echo "Backup created: $DEST"
```

### Kiểm tra service còn sống không
```bash
#!/bin/bash

SERVICE=$1

if systemctl is-active --quiet "$SERVICE"; then
  echo "$SERVICE is running"
else
  echo "$SERVICE is NOT running"
fi
```

---

## 17) Thói quen tốt khi viết Bash

### Luôn quote biến
Không nên:
```bash
cp $file $dest
```

Nên:
```bash
cp "$file" "$dest"
```

### Đặt tên biến rõ ràng
```bash
LOG_FILE="app.log"
BACKUP_DIR="/backup"
```

### Dùng chế độ an toàn
```bash
set -e
```

Mạnh hơn:
```bash
set -euo pipefail
```

Ý nghĩa:
- `-e`: gặp lỗi thì dừng
- `-u`: dùng biến chưa khai báo sẽ lỗi
- `pipefail`: bắt lỗi trong pipeline

---

## 18) Mẫu script sạch sẽ

```bash
#!/bin/bash
set -euo pipefail

APP_NAME="nginx"

echo "Checking service: $APP_NAME"

if systemctl is-active --quiet "$APP_NAME"; then
  echo "$APP_NAME is running"
else
  echo "$APP_NAME is not running"
  exit 1
fi
```

---

## 19) Những gì nên nhớ trước
- shebang: `#!/bin/bash`
- biến
- `read`
- args: `$1`, `$2`, `$@`
- `if`
- `for`, `while`
- function
- exit code
- pipe: `|`
- redirect: `>`, `>>`, `2>`
- `$(...)`
- `set -euo pipefail`

---

## 20) Ứng dụng trong DevOps
Bash rất hay dùng để:
- deploy app
- restart service
- backup file / database
- dọn log
- check health server
- chạy cron job
- kết nối nhiều công cụ lại với nhau

---

## Ghi chú
Bash không phải ngôn ngữ mạnh nhất để xây hệ thống lớn, nhưng cực kỳ hữu dụng cho automation, server ops và các task glue code trong DevOps.
