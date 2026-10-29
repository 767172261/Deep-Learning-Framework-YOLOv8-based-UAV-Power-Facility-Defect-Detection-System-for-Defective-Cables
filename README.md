 **航拍无人机巡查电力设施缺陷检测数据集** 

---
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c9a76a5ea6f947b7a94abeab7821ff80.png)

### 📊 无人机电力设施缺陷检测数据集概览表

| **项目**                | **描述**                                                                 |
|------------------------|--------------------------------------------------------------------------|
| **数据集名称**          | 无人机巡查电力设施缺陷检测数据集                                          |
| **总图像数量**          | 8,487 张                                                                 |
| **图像分辨率**          | 640 × 640 像素（已预处理为统一尺寸）                                      |
| **采集平台**            | 无人机（典型用于电力巡检）                                                |
| **应用场景**            | 电力杆塔与线路缺陷自动识别、智能运维、状态评估                            |
| **标注格式**            | ✅ YOLO 格式（`.txt`）<br>✅ PASCAL VOC 格式（`.xml`）                     |
| **类别总数**            | 3 类（均为缺陷类型）                                                     |
| **总标注实例数**        | **9,708 个**（所有类别之和）                                             |
| **数据划分**            | - 训练集：7,426 张<br>- 验证集：708 张<br>- 测试集：353 张                |

---
以下文字及代码仅供参考
### 🏷️ 类别分布详情表

| **序号** | **类别英文名**         | **中文名称**             | **包含图像数** | **标注实例数** |
|---------|------------------------|------------------------|---------------|---------------|
| 1       | `cable_defectueux`     | 有缺陷的电缆           | 3,890         | 4,044         |
| 2       | `isolateur_manquant`   | 缺失的绝缘子           | 2,884         | 3,080         |
| 3       | `rouille`              | 铁锈（或锈蚀处）       | 1,713         | 2,584         |
|         | **总计**               |                        | **8,487**     | **9,708**     |

> 🔍 注：
> - 同一张图像可能包含多个类别或多个实例（因此“图像数”总和 = 8,487，但各类图像数之和 > 8,487）；
> - “铁锈”类虽然图像数最少，但平均每图标注实例更多（2,584 / 1,713 ≈ 1.51），说明锈蚀区域常呈多点分布；
> - 数据集聚焦**缺陷检测**，无正常设备类别，适用于异常定位任务。

---
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/cea591cb03e4407e877a681b78e010b1.png)

### 🗂️ 推荐文件组织结构

```
power_defect_dataset/
├── images/
│   ├── train/      # 7,426 张
│   ├── val/        # 708 张
│   └── test/       # 353 张
├── labels_yolo/
│   ├── train/
│   ├── val/
│   └── test/
├── annotations_voc/
│   ├── train/
│   ├── val/
│   └── test/
└── dataset.yaml    # YOLOv8 配置文件
```

---

### 📄 `dataset.yaml` 示例（YOLOv8）

```yaml
train: ./images/train
val: ./images/val
test: ./images/test

nc: 3
names: ['cable_defectueux', 'isolateur_manquant', 'rouille']
```

---

### ✅ 数据集特点

- **专注缺陷**：仅包含三类典型电力设施缺陷，适合高精度异常检测；
- **双标注支持**：同时提供 YOLO 与 VOC 格式，兼容主流检测框架；
- **尺寸统一**：640×640 输入，便于直接训练 YOLOv8 等模型；
- **划分明确**：已按 87.5% : 8.3% : 4.2% 划分训练/验证/测试集，可直接使用。

---

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9c66afc4ec744af79f24756b597594f3.png)
 **完整的基于 YOLOv8 的无人机电力设施缺陷检测系统** 实现代码，包含：

✅ 调用预训练模型  
✅ 训练自定义数据集（3类：有缺陷电缆、缺失绝缘子、铁锈）  
✅ 支持图片/视频/摄像头推理  
✅ PyQt5 可视化界面（中文友好）  
✅ 项目结构清晰，可直接运行  

---

## 📁 一、项目目录结构

```
power_defect_detection/
├── data/
│   └── dataset.yaml                # 数据集配置
├── models/
│   └── best.pt                     # 训练好的模型（训练后生成）
├── runs/
│   └── detect/                     # 推理结果保存
├── test_files/
│   ├── demo.jpg
│   └── demo.mp4
├── utils/
│   ├── detector.py                 # 检测核心类
│   └── video_thread.py             # 多线程视频处理
├── train.py                        # 模型训练脚本
├── inference_test.py               # 命令行推理测试
├── main_ui.py                      # PyQt5 主界面
└── requirements.txt                # 依赖库
```

---

## 📦 二、`requirements.txt`

```txt
ultralytics==8.2.0
pyqt5==5.15.9
opencv-python==4.8.0
numpy==1.24.3
pillow==9.5.0
matplotlib==3.6.3
```

安装命令：
```bash
pip install -r requirements.txt
```

---

## 🗂️ 三、`data/dataset.yaml`

```yaml
train: ../data/images/train
val: ../data/images/val
test: ../data/images/test

nc: 3
names: ['cable_defectueux', 'isolateur_manquant', 'rouille']
```

> 💡 图像和标签需按如下结构存放：
```
data/
└── images/
    ├── train/      # 7426 张
    ├── val/        # 708 张
    └── test/       # 353 张
└── labels/
    ├── train/
    ├── val/
    └── test/
```

---

## 🔧 四、`train.py` —— 模型训练

```python
# train.py
from ultralytics import YOLO

if __name__ == "__main__":
    # 加载预训练模型
    model = YOLO("yolov8n.pt")  # 可换为 yolov8s.pt 等

    # 开始训练
    results = model.train(
        data="data/dataset.yaml",
        epochs=100,
        imgsz=640,
        batch=16,
        name="power_defect_yolov8n",
        project="runs/train",
        device=0,          # 使用 GPU，若无则设为 "cpu"
        patience=15,       # 早停
        save=True,
        exist_ok=True
    )

    print(f"✅ 训练完成！最佳模型保存于: {results.save_dir}/weights/best.pt")
```

运行：
```bash
python train.py
```

---

## 🔍 五、`utils/detector.py` —— 检测工具类

```python
# utils/detector.py
from ultralytics import YOLO
import cv2
import numpy as np

class PowerDefectDetector:
    def __init__(self, model_path="runs/train/power_defect_yolov8n/weights/best.pt"):
        self.model = YOLO(model_path)
        self.class_names = ['有缺陷的电缆', '缺失的绝缘子', '铁锈']
        self.colors = [(0, 0, 255), (255, 0, 0), (0, 255, 255)]  # BGR

    def predict_image(self, image):
        """输入: numpy array (HWC, BGR)；输出: 带框图像 + 检测结果列表"""
        results = self.model(image, conf=0.3, iou=0.45)
        annotated_frame = results[0].plot()

        detections = []
        for box in results[0].boxes:
            cls_id = int(box.cls.item())
            conf = float(box.conf.item())
            x1, y1, x2, y2 = map(int, box.xyxy[0].tolist())
            detections.append({
                'class': self.class_names[cls_id],
                'confidence': conf,
                'bbox': (x1, y1, x2, y2)
            })
        return annotated_frame, detections

    def predict_video_frame(self, frame):
        """用于视频/摄像头流，仅返回带框图像"""
        results = self.model(frame, conf=0.3, iou=0.45)
        return results[0].plot()
```

---

## 🎥 六、`utils/video_thread.py` —— 多线程视频处理（避免UI卡顿）

```python
# utils/video_thread.py
from PyQt5.QtCore import QThread, pyqtSignal
import cv2

class VideoThread(QThread):
    change_pixmap_signal = pyqtSignal(object)

    def __init__(self, detector, source=0):
        super().__init__()
        self.detector = detector
        self.source = source
        self._run_flag = True

    def run(self):
        cap = cv2.VideoCapture(self.source)
        while self._run_flag:
            ret, frame = cap.read()
            if ret:
                frame = self.detector.predict_video_frame(frame)
                self.change_pixmap_signal.emit(frame)
        cap.release()

    def stop(self):
        self._run_flag = False
        self.wait()
```

---

## 🖥️ 七、`main_ui.py` —— PyQt5 主界面（完整可运行）

```python
# main_ui.py
import sys
import os
import cv2
from PyQt5.QtWidgets import (
    QApplication, QMainWindow, QLabel, QPushButton, QVBoxLayout,
    QHBoxLayout, QWidget, QFileDialog, QTextEdit, QComboBox
)
from PyQt5.QtGui import QPixmap, QImage
from PyQt5.QtCore import Qt, QTimer
from utils.detector import PowerDefectDetector
from utils.video_thread import VideoThread

class PowerDefectDetectionApp(QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("无人机电力设施缺陷检测系统")
        self.setGeometry(100, 100, 1100, 700)
        self.detector = PowerDefectDetector()
        self.video_thread = None
        self.init_ui()

    def init_ui(self):
        central_widget = QWidget()
        self.setCentralWidget(central_widget)
        main_layout = QHBoxLayout(central_widget)

        # 左侧：图像显示区
        self.image_label = QLabel()
        self.image_label.setFixedSize(640, 640)
        self.image_label.setStyleSheet("background-color: #f0f0f0; border: 1px solid #ccc;")
        self.image_label.setAlignment(Qt.AlignCenter)

        # 右侧：控制面板
        control_layout = QVBoxLayout()
        self.mode_combo = QComboBox()
        self.mode_combo.addItems(["选择模式", "图片检测", "视频检测", "摄像头实时检测"])
        self.mode_combo.currentIndexChanged.connect(self.on_mode_change)

        self.file_btn = QPushButton("📁 选择文件")
        self.file_btn.clicked.connect(self.select_file)
        self.file_btn.setEnabled(False)

        self.start_btn = QPushButton("▶ 开始检测")
        self.start_btn.clicked.connect(self.start_detection)
        self.start_btn.setEnabled(False)

        self.result_text = QTextEdit()
        self.result_text.setReadOnly(True)
        self.result_text.setPlaceholderText("检测结果将在此显示...")

        self.exit_btn = QPushButton("🚪 退出")
        self.exit_btn.clicked.connect(self.close)

        control_layout.addWidget(QLabel("检测模式："))
        control_layout.addWidget(self.mode_combo)
        control_layout.addWidget(self.file_btn)
        control_layout.addWidget(self.start_btn)
        control_layout.addWidget(QLabel("检测结果："))
        control_layout.addWidget(self.result_text)
        control_layout.addWidget(self.exit_btn)
        control_layout.addStretch()

        main_layout.addWidget(self.image_label)
        main_layout.addLayout(control_layout)

        self.current_mode = None
        self.selected_path = None

    def on_mode_change(self, index):
        mode_map = {1: "image", 2: "video", 3: "camera"}
        self.current_mode = mode_map.get(index)
        self.file_btn.setEnabled(index in [1, 2])
        self.start_btn.setEnabled(True)

    def select_file(self):
        if self.current_mode == "image":
            path, _ = QFileDialog.getOpenFileName(self, "选择图片", "", "Images (*.jpg *.png *.jpeg)")
        elif self.current_mode == "video":
            path, _ = QFileDialog.getOpenFileName(self, "选择视频", "", "Videos (*.mp4 *.avi *.mov)")
        else:
            path = None
        self.selected_path = path

    def start_detection(self):
        if self.current_mode == "image":
            self.detect_image()
        elif self.current_mode == "video":
            self.play_video()
        elif self.current_mode == "camera":
            self.start_camera()

    def detect_image(self):
        if not self.selected_path or not os.path.exists(self.selected_path):
            return
        image = cv2.imread(self.selected_path)
        result_img, detections = self.detector.predict_image(image)
        self.display_image(result_img)
        self.show_results(detections)

    def display_image(self, img):
        h, w, ch = img.shape
        bytes_per_line = ch * w
        q_img = QImage(img.data, w, h, bytes_per_line, QImage.Format_BGR888)
        self.image_label.setPixmap(QPixmap.fromImage(q_img).scaled(640, 640, Qt.KeepAspectRatio))

    def show_results(self, detections):
        text = ""
        for i, det in enumerate(detections):
            text += f"【{i+1}】{det['class']} | 置信度: {det['confidence']:.2%}\n"
        self.result_text.setText(text if text else "未检测到缺陷")

    def play_video(self):
        if not self.selected_path:
            return
        cap = cv2.VideoCapture(self.selected_path)
        while cap.isOpened():
            ret, frame = cap.read()
            if not ret:
                break
            result_frame = self.detector.predict_video_frame(frame)
            self.display_image(result_frame)
            cv2.waitKey(30)  # 控制播放速度
        cap.release()

    def start_camera(self):
        self.video_thread = VideoThread(self.detector, source=0)
        self.video_thread.change_pixmap_signal.connect(self.display_image)
        self.video_thread.start()

    def closeEvent(self, event):
        if self.video_thread:
            self.video_thread.stop()
        event.accept()

if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = PowerDefectDetectionApp()
    window.show()
    sys.exit(app.exec_())
```

---

## ▶ 八、运行说明

### 1. 训练模型
```bash
python train.py
```

### 2. 启动可视化界面
```bash
python main_ui.py
```

### 3. 使用流程
- 选择模式（图片/视频/摄像头）
- 点击“选择文件”（图片或视频）
- 点击“开始检测”
- 查看图像与文字结果

---

## ✅ 系统特点

- **三类缺陷精准识别**：电缆缺陷、缺失绝缘子、铁锈
- **多输入支持**：图片 / 视频 / 实时摄像头
- **中文界面**：操作直观，适合现场巡检人员
- **轻量高效**：基于 YOLOv8n，可在普通 GPU 或 CPU 上运行
- **结果可读**：显示类别 + 置信度 + 可视化框

---

## 📌 注意事项

- 若无 GPU，训练时将 `device=0` 改为 `device="cpu"`
- 首次运行需确保 `best.pt` 存在（或先运行 `train.py`）
- 摄像头索引默认为 `0`，如有多个摄像头可修改 `VideoThread(source=1)`

---

如需 **打包成 exe**、**添加报警功能**、**导出检测报告**，可继续扩展。是否需要我提供这些增强功能？
