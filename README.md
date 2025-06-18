# 基于 Nerf 和 3D Gaussian Splatting 的物体重建和新视图合成

## 仓库介绍

本项目为课程神经网络和深度学习期末作业的代码仓库

### Task 1: 基于 NeRF 的物体重建和新视图合成
基本要求：
  - 选取身边的物体拍摄多角度图片/视频，并使用 COLMAP 估计相机参数，随后选用以下其中一种 NeRF 加速技术（如Plenoxels、TensoRF 等）训练；
  - 基于训练好的 NeRF 在新的轨迹下渲染环绕物体的视频，并在预留的测试图片上评价定量结果。
  - 在报告中对选用的 NeRF 变体进行介绍，阐明其与原版 NeRF 的差异。

### Task 2: 基于 3D Gaussian Splatting 的物体重建和新视图合成
基本要求：
  - 选取身边的物体拍摄多角度图片/视频，并使用 COLMAP 估计相机参数，随后使用其官方代码库训练 3D Gaussian；
  - 基于训练好的 3D Gaussian在新的轨迹下渲染环绕物体的视频，并在预留的测试图片上评价定量结果。
  - 比较任务 1 中原版 NeRF、任务 1 NeRF 加速技术，以及 3D Gaussian Splatting 三种方法的合成结果和训练/测试效率，在报告中加入相应分析。
  
## Contents
- [Model 1: NeRF](#model-1-nerf)
- [Model 2: Instant-NGP](#model-2-instant-ngp)
- [Model 3: 3D Gaussian Splatting](#model-3-3d-gaussian-splatting)

# Model 1: NeRF
- 模型权重和渲染视频的下载地址：https://pan.baidu.com/s/1heRKXchSN-UaK1ZPAEckwA?pwd=45ai
##  1️⃣ 数据准备 
参考 https://zhuanlan.zhihu.com/p/576416530 ，处理好的数据已放在 data/nerf_llff_data/vasedeck 目录下。

##  2️⃣ 模型训练
```
python run_nerf.py --config configs/vasedeck.txt --spherify --no_ndc
```
##  3️⃣ 实验结果定量评价

```
 python run_nerf.py --config configs/vasedeck.txt --eval --spherify --no_ndc
```
  运行后会打印出模型在测试集上的PSNR指标。
##  4️⃣ Tensorboard可视化
```
tensorboard --logdir ./logs/summaries/
```

### 🎥 NeRF Demo

[▶️ Click to watch NeRF video](https://github.com/user-attachments/assets/ec5fe30e-1d61-43f2-820d-61c88374a459)

# Model 2: Instant-NGP
- 本项目依赖 nerstudio, 请参考 https://docs.nerf.studio/quickstart/installation.html 进行安装
- 模型权重和渲染视频的下载地址：https://pan.baidu.com/s/17wAoJvbfJIzDrmXNQgGECQ?pwd=8c5y

##  1️⃣ 数据准备 
```
ns-process-data images --data data/train_images --eval-data data/test_images --output-dir data/vasedeck
```

##  2️⃣ 模型训练
```
ns-train instant-ngp --data data/vasedeck --vis viewer+tensorboard nerfstudio-data --eval-mode filename
```
##  3️⃣ 实验结果定量评价与视频渲染
将网盘中的模型权重压缩包解压缩到项目目录下，得到模型权重 outputs 文件夹

### 1. 新视图合成与实验结果定量评价
* 在命令行中运行：
```bash
ns-eval --load-config outputs/vasedeck/instant-ngp/2025-06-13_200815/config.yml --render-output-path render-output
```
进行新视图渲染。在 render-output 文件夹下可得到渲染得到的测试集图片，同时会生成 output.json 文件，包含了计算出的 SSIM、PSNR、LPIPS 等指标。

### 2. 视频渲染
* 在命令行中运行：
```bash
ns-render camera-path --load-config outputs/vasedeck/instant-ngp/2025-06-13_200815/config.yml --camera-path-filename data/vasedeck/camera_paths/2025-06-13-22-36-11.json --output-path render-output/2025-06-13-22-36-11.mp4
```
该命令依据训练好的模型和预设的新相机轨迹，在 render-output 目录下生成环绕物体的视频。

##  4️⃣ Tensorboard可视化
```
tensorboard --logdir=outputs/vasedeck/instant-ngp/2025-06-13_200815/
```

### 🎥 Instant-NGP Demo

[▶️ Click to watch Instant-NGP video](https://github.com/user-attachments/assets/f2d80650-8f73-4c0e-8334-58006241bedd)

# Model 3: 3D Gaussian Splatting
- 本项目依赖 nerstudio, 请参考 https://docs.nerf.studio/quickstart/installation.html 进行安装
- 模型权重和渲染视频的下载地址：https://pan.baidu.com/s/1imadcTz35t3UpIXbN3AWrw?pwd=6ccv

##  1️⃣ 数据准备 
```
ns-process-data images --data data/train_images --eval-data data/test_images --output-dir data/vasedeck
```

##  2️⃣ 模型训练
```
ns-train splatfacto --data data/vasedeck/ --vis viewer+tensorboard nerfstudio-data --eval-mode filename
```
##  3️⃣ 实验结果定量评价与视频渲染
将网盘中的模型权重压缩包解压缩到项目目录下，得到模型权重 outputs 文件夹

### 1. 新视图合成与实验结果定量评价
* 在命令行中运行：
```bash
ns-eval --load-config outputs/vasedeck/splatfacto/2025-06-14_144920/config.yml --render-output-path render-output
```
进行新视图渲染。在 render-output 文件夹下可得到渲染得到的测试集图片，同时会生成 output.json 文件，包含了计算出的 SSIM、PSNR、LPIPS 等指标。

### 2. 视频渲染
* 在命令行中运行：
```bash
ns-render camera-path --load-config outputs/vasedeck/splatfacto/2025-06-14_144920/config.yml --camera-path-filename data/vasedeck/camera_paths/2025-06-14-15-11-18.json --output-path render-output/2025-06-14-15-11-18.mp4
```
该命令依据训练好的模型和预设的新相机轨迹，在 render-output 目录下生成环绕物体的视频。

##  4️⃣ Tensorboard可视化
```
tensorboard --logdir=outputs/vasedeck/splatfacto/2025-06-14_144920/
```

### 🎥 3D Gaussian Splatting Demo

[▶️ Click to watch 3D Gaussian Splatting video](https://github.com/user-attachments/assets/5b7c4511-7d74-4663-9e8e-256d545032b9)



