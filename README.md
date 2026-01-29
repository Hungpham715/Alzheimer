# Báo cáo 

# Baseline B
## Bộ dữ liệu
Baseline B: [OASIS-1 Github](https://github.com/14thibea/OASIS-1_dataset)
## Xử lý dữ liệu (mô phỏng cách bác sĩ quan sát MRI)
### Lấy mẫu 
Lấy mẫu lát cắt MRI với ưu tiên vùng hippocampus (hải mã) - vùng não quan trọng để phát hiện Alzheimer.

Cách thức:
- Loại bỏ phần không quan trọng: Bỏ 20% đầu (cổ) và 20% cuối (cằm)
- Xác định vùng hippocampus: Nằm ở khoảng 45% chiều cao não, chiếm 20% diện tích
  
Phân bổ slices:
- 60% slices lấy từ vùng hippocampus
- 40% slices lấy từ phần trước và sau hippocampus để có context toàn cảnh
- Chọn slices đều nhau trong mỗi vùng bằng np.linspace

→ Kết quả: Tập k slices ưu tiên hippocampus nhưng vẫn giữ context tổng thể của não.

### 
