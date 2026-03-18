# DevOps Roadmap

## Mục tiêu
Lộ trình này dành cho người muốn học DevOps theo hướng thực chiến: hiểu hệ thống, biết tự động hóa, deploy được ứng dụng, vận hành được môi trường thật, và dần tiến tới cloud/Kubernetes.

---

## 1) Linux + Networking + Git

### Cần học
- Linux command line
- File system, permissions, process, service
- SSH, systemd, environment variables
- Networking cơ bản: DNS, HTTP/HTTPS, TCP/IP, reverse proxy
- Git/GitHub

### Mục tiêu
- SSH vào server và thao tác tự tin
- Tự deploy app thủ công lên server
- Hiểu request đi qua hệ thống như thế nào

---

## 2) Programming / Scripting

### Cần học
- Bash
- Python cơ bản
- Viết script để automate các việc lặp lại

### Ví dụ thực hành
- Backup file / database
- Rotate logs
- Health check service
- Script deploy đơn giản

### Mục tiêu
- Bất cứ việc gì lặp lại nhiều lần đều có thể script hóa

---

## 3) Server + Web Stack

### Cần học
- Nginx hoặc Apache
- systemd
- Biến môi trường, config app
- Database cơ bản: PostgreSQL hoặc MySQL
- Redis cơ bản

### Mục tiêu
- Hiểu app chạy trên server kiểu gì
- Biết các điểm lỗi phổ biến: app, web server, database, network, config

---

## 4) Containers

### Cần học
- Docker
- Dockerfile
- Image, container, volume, network
- Docker Compose

### Mục tiêu
- Đóng gói app thành container
- Chạy local và staging ổn định
- Biết cách debug container logs, network, volume

---

## 5) CI/CD

### Cần học
- GitHub Actions hoặc GitLab CI
- Build, test, lint, deploy tự động
- Secrets trong pipeline

### Mục tiêu
- Push code là pipeline tự chạy
- Tách rõ các bước: test → build → deploy

---

## 6) Cloud cơ bản

### Nên chọn trước
- AWS (phổ biến nhất, tài liệu nhiều)

### Cần học
- EC2
- S3
- IAM
- VPC
- Load Balancer
- RDS

### Mục tiêu
- Dựng được một môi trường production mini trên cloud
- Hiểu networking và permission trên cloud

---

## 7) Infrastructure as Code

### Cần học
- Terraform

### Mục tiêu
- Tạo server, network, service bằng code
- Infra có thể version control, review và tái tạo dễ dàng

---

## 8) Kubernetes

### Cần học
- Pod
- Deployment
- Service
- ConfigMap
- Secret
- Ingress
- Helm cơ bản

### Mục tiêu
- Deploy app có rolling update
- Scale app được
- Quản lý config và secret đúng cách

> Lưu ý: Không nên học Kubernetes quá sớm khi Linux, Docker, network còn yếu.

---

## 9) Monitoring + Logging

### Cần học
- Prometheus
- Grafana
- ELK hoặc Loki

### Mục tiêu
- Theo dõi CPU, memory, disk, network, application metrics
- Tìm lỗi bằng log nhanh hơn thay vì đoán mò

---

## 10) Security + DevSecOps cơ bản

### Cần học
- SSH key
- Secret management
- Principle of least privilege
- SSL/TLS
- Vulnerability scanning
- Image scanning

### Mục tiêu
- Không deploy kiểu “cầu trời”
- Giảm rủi ro lộ secrets, sai permission, image lỗi thời

---

# Lộ trình 6 tháng

## Tháng 1
- Linux
- Networking cơ bản
- Git
- Bash

## Tháng 2
- Python cơ bản
- Server / web stack
- Database cơ bản

## Tháng 3
- Docker
- Docker Compose
- Deploy app thủ công

## Tháng 4
- CI/CD
- AWS cơ bản

## Tháng 5
- Terraform
- Monitoring / Logging

## Tháng 6
- Kubernetes
- Project thực chiến

---

# 3 project nên làm

## Project 1: Deploy web app lên VPS
### Thành phần
- Nginx
- Docker
- Domain
- HTTPS
- Script deploy

### Mục tiêu
- Hiểu quy trình deploy end-to-end

## Project 2: CI/CD pipeline hoàn chỉnh
### Flow
- Push code
- Run test
- Build image
- Deploy staging

### Mục tiêu
- Biết tự động hóa quy trình phát hành

## Project 3: Mini production system
### Thành phần
- App
- Database
- Redis
- Monitoring
- Alert
- Terraform dựng infra

### Nâng cấp thêm
- Đưa lên Kubernetes nếu muốn level cao hơn

---

# Tool stack đề xuất cho người mới
- Linux
- Git / GitHub
- Bash
- Python
- Docker
- GitHub Actions
- AWS
- Terraform
- Kubernetes
- Prometheus + Grafana

---

# Sai lầm thường gặp
- Học Kubernetes quá sớm khi chưa chắc Linux / Docker
- Chỉ học tool mà không hiểu hệ thống
- Xem video nhiều nhưng không làm project
- Không viết note / tài liệu khi học

---

# Lộ trình thực dụng để đi làm nhanh

**Linux → Git → Bash/Python → Docker → CI/CD → AWS → Terraform → Kubernetes**

Đây là thứ tự hợp lý nếu mục tiêu là vừa học được nền tảng, vừa tiến dần tới môi trường thực tế trong công việc DevOps.
