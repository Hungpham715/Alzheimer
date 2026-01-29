# Báo cáo 

# Baseline B
## Bộ dữ liệu
Link: [OASIS-1 Github](https://github.com/14thibea/OASIS-1_dataset)
## Xử lý dữ liệu (mô phỏng cách bác sĩ quan sát MRI)
### Lấy mẫu 
Lấy mẫu lát cắt MRI với ưu tiên vùng hippocampus (hải mã) - vùng não quan trọng để phát hiện Alzheimer.

Cách thức:
- Loại bỏ phần không quan trọng: Bỏ 20% đầu (cổ) và 20% cuối (cằm).
- Xác định vùng hippocampus: Nằm ở khoảng 45% chiều cao não, chiếm 20% diện tích.

→ Kết quả: Tập k slices ưu tiên hippocampus nhưng vẫn giữ context tổng thể của não.

### Chuẩn hóa 
Chuẩn hóa cường độ ảnh MRI bằng z-score, nhưng chỉ trên vùng não, không tính nền.

### Đồng bộ kích thước 
Chuẩn hóa lát cắt MRI 2D trước khi đưa vào mô hình:
- Trước hết, nó tự động tìm vùng não trên ảnh, xung quanh là nền đen nên cần loại bỏ, chỉ giữ lại ảnh não.
- Cắt ảnh nền xung quanh vùng não.
- Sau đó thêm hoặc cắt đối xứng để mọi ảnh đều có kích thước cố định (ví dụ: 256×256).

→ Kết quả: ảnh đầu vào gọn gàng, cùng kích thước, tập trung vào não, giúp mô hình học ổn định và hiệu quả hơn.

### Mô hình 
