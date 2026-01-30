# Báo cáo 

## Bộ dữ liệu
Link: [MRI 3D Scan](https://www.kaggle.com/datasets/muhammadzahraan/3d-mri-scans-for-alzheimer-disease/data)

## Tổng quan dữ liệu 
Dữ liệu gồm 366 bệnh nhân (chỉ lấy mỗi file đầu tiên của mỗi bệnh nhân), chia 60/20/20
- Train: 219 bệnh nhân. Trong đó:  AD: 99, CN: 120
- Validation: 73 bệnh nhân. Trong đó: AD: 33, CN: 40 
- Test: 74 bệnh nhân. Trong đó: AD: 34, CN: 40
  
## Xử lý dữ liệu (mô phỏng cách bác sĩ quan sát MRI)
### Lấy mẫu 
Lấy mẫu lát cắt MRI với ưu tiên vùng hippocampus (hải mã) - vùng não quan trọng để phát hiện Alzheimer.

Cách thức:
- Loại bỏ phần không quan trọng: Bỏ 10% đầu (cổ) và 90% cuối (cằm).
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

## Mô hình (ResNet Pretrained + Slice Attention)
Quá trình huấn luyện diễn ra theo epoch: Trong mỗi epoch, mô hình học lần lượt trên toàn bộ bệnh nhân trong tập train theo batch.

Biểu diễn bệnh nhân
- Mỗi bệnh nhân được biểu diễn bằng 48 lát cắt MRI 2D (ảnh grayscale).
- Một batch có dạng: (B, 48, 1, 256, 256).

Trích xuất đặc trưng bằng ResNet
- Tất cả lát cắt trong batch được xử lý đồng thời: Dồn (B, 48) thành (B×48).
- Mỗi lát cắt đi qua ResNet18 (sửa conv1 từ 3→1) và được ánh xạ thành 1 vector embedding 256 chiều.
- Kết quả: Mỗi bệnh nhân có 48 embedding tương ứng 48 lát cắt.

Slice Attention (gộp theo lát cắt)
- Từ 48 embedding của mỗi bệnh nhân:
  + Mô hình học điểm quan trọng cho từng lát cắt.
  + Dùng softmax để chuyển các điểm này thành trọng số attention.
- Các embedding được gộp bằng trung bình có trọng số, tạo ra 1 vector đại diện cho toàn bộ não của bệnh nhân.

Phân loại: Vector đại diện này được đưa qua lớp linear để dự đoán:
- Alzheimer (AD).
- Không Alzheimer (non-AD/CN).

## Đánh giá 
- Accuracy: 0.72
- Precision: 0.93
- Recall: 0.41
- F1 score: 0.57
- Confusion matrix:

            [39] [1]
            
            [20] [14]
- AUC: 0.89
  
## Dự đoán và giải thích
Ví dụ: ID bệnh nhân: 031_S_0618
- Dự đoán: CN (xác suất P(AD) = 0.36) -> Dự đoán đúng
- Giải thích (top 5): 
  + Top 1: view=axial, slice_idx=1, attention=0.0332 (slice_idx là chỉ số của 48 lát cắt 2D)
  + Top 2: view=coronal, slice_idx=20, attention=0.0317
  + Top 3: view=axial, slice_idx=2, attention=0.0310
  + Top 4: view=axial, slice_idx=12, attention=0.0275
  + Top 5: view=sagittal, slice_idx=37, attention=0.0268
