# Preface

## 传统RGBD（以及高斯）点云渲染流程

### 传统RGBD点云渲染流程

传统RGBD点云渲染流程主要步骤如下：

1. **随机点散步**：在UV空间上随机生成点，这些点将作为后续深度和颜色采样的基础
2. **采样深度图**：根据生成的点位置，从深度图或点云图中采样对应的深度信息，获取每个点在3D空间中的Z坐标
3. **颜色采样**：同时从RGB图像中采样对应位置的颜色信息，为每个3D点赋予颜色属性
4. **粒子烘焙**：将采样得到的点（包含位置和颜色信息）通过粒子系统渲染，构建成3D场景

### 高斯点云渲染技术

高斯点云渲染（3D Gaussian Splatting，3DGS）是一种更先进的点云渲染技术，其核心原理如下：

1. **高斯椭球表示**：将场景表示为多个3D高斯椭球，每个椭球包含位置、协方差矩阵、颜色（RGB）和透明度（α）等属性
2. **辐射场函数**：每个高斯点的辐射场函数为：
   \[G(x) = e^{-\frac{1}{2}(x-\mu)^T\Sigma^{-1} (x-\mu)}\]
   其中，μ是高斯椭球的中心位置，Σ是协方差矩阵
3. **可微分泼溅**：渲染时通过可微分的泼溅（Splatting）技术将3D高斯椭球投影到2D图像平面
4. **光线投射累积**：通过光线投射累积高斯点的颜色和透明度，生成每个像素的最终颜色

具体的RGBD例子可以参考 `RGBDSample` 文件夹内的图片，包括：

### RGBD_Only_Examples 文件夹
![ComfyUI_temp_nhace_00003_.png](RGBDSample/RGBD_Only_Examples/ComfyUI_temp_nhace_00003_.png)
![ComfyUI_temp_nhace_00010_.png](RGBDSample/RGBD_Only_Examples/ComfyUI_temp_nhace_00010_.png)
![ComfyUI_temp_nhace_00012_.png](RGBDSample/RGBD_Only_Examples/ComfyUI_temp_nhace_00012_.png)

### FluxDemo_1D 文件夹
![Color.png](RGBDSample/FluxDemo_1D/Color.png)
![Depth.png](RGBDSample/FluxDemo_1D/Depth.png)
![RGBD.png](RGBDSample/FluxDemo_1D/RGBD.png)

### FluxDemo_2Klein 文件夹
![Color.png](RGBDSample/FluxDemo_2Klein/Color.png)
![Depth.png](RGBDSample/FluxDemo_2Klein/Depth.png)
![RGBD.png](RGBDSample/FluxDemo_2Klein/RGBD.png)

## 传统方法的弊端

1. **依赖粒子渲染压力大**：由于需要渲染大量粒子来构建3D场景，对硬件性能要求较高，特别是在点云密度较大的情况下。对于高斯点云渲染，虽然技术更先进，但仍然需要处理大量的高斯椭球，计算复杂度较高

2. **点云稀疏问题**：当深度图质量不佳或场景复杂度较高时，生成的点云可能会过于稀疏，导致渲染效果不连贯，影响视觉体验。即使是高斯点云渲染，如果高斯椭球的分布不合理，也会出现类似问题

3. **内存占用高**：无论是传统粒子系统还是高斯点云渲染，都需要存储大量的点数据，对内存要求较高

4. **渲染精度与性能平衡**：增加点云密度可以提高渲染精度，但会进一步增加渲染压力和内存占用，需要在精度和性能之间寻找平衡

5. **文件传输困难**：高斯点云即便使用Apple ML-Sharp生成，其ply文件格式较大，难以通过网络协议进行Comfyui等AI作图软件到Unity等游戏软件的实时传输，限制了跨平台实时应用的可能性