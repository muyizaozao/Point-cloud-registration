# Point-cloud-registration
点云配准入门知识
* 点云的概念：点云是在同一空间参考系下表达目标空间分布和目标表面特性的海量点集合，在获取物体表面每个采样点的空间坐标后，得到的是点的集合，称之为“点云”（Point Cloud）。<br>
* 点云图像是最基础也是最常见的三维图像。
那什么是三维图像呢？<br>三维图像是一种特殊的图像信息表达形式。相比较于常见的二维图像，其最大的特征是表达了空间中三个维度（长度宽度和深度）的数据。<br>
* 三维图像的表现形式包括：`深度图`（以灰度表达物体与相机的距离），`几何模型`（由CAD软件建立），`点云模型`（所有逆向工程设备都将物体采样成点云）。<br>
* 点云根据测量原理主要分为两种：
根据`激光`测量原理得到的点云，包括三维坐标（XYZ）和激光反射强度（Intensity）。强度信息与目标的表面材质、粗糙度、入射角方向，以及仪器的发射能量，激光波长有关。
根据`摄影`测量原理得到的点云，包括三维坐标（XYZ）和颜色信息（RGB）。
当然也有把激光和摄影相结合在一起的（多传感器融合技术），这种结合激光测量和摄影测量原理得到点云，包括三维坐标（XYZ）、激光反射强度（Intensity）和颜色信息（RGB）。<br>
本次的文章主要讲的是基于`摄像技术`的点云配准。<br>
* 基于摄像技术的点云获取设备：RGBD设备（深度摄像机）常用来获取点云的设备。比如PrimeSense公司的PrimeSensor、微软的Kinect、华硕的XTionPRO。<br>
* 点云的属性：空间分辨率、点位精度、表面法向量等。<br>
* 点云存储格式：*.pts; *.asc ; *.dat; .stl ; [1] .imw；.xyz；.las。<br>
* 一般将点云图像处理分为三个层次:<br>1) 低层次包括图像强化，滤波，关键点/边缘检测等基本操作。<br>2) 中层次包括连通域标记（label），图像分割等操作。<br>3) 高层次包括物体识别，场景分析等操作。工程中的任务往往需要用到多个层次的图像处理手段。<br>PCL官网对点云处理方法给出了较为明晰的层次划分：![](https://github.com/muyizaozao/Point-cloud-registration/blob/master/photo.png)<br>
## 点云配准的常规处理方法<br>
---
* 低层次处理方法:<br>
①滤波方法：双边滤波、高斯滤波、条件滤波、直通滤波、随机采样一致性滤波。<br>②关键点：ISS3D、Harris3D、NARF，SIFT3D
* 中层次处理方法：<br>
①特征描述：法线和曲率的计算、特征值分析、SHOT、PFH、FPFH、3D Shape Context、Spin Image<br>
②分割与分类：<br>
  * 分割：区域生长、Ransac线面提取、全局优化平面提取
　　　K-Means、Normalize Cut（Context based）
　　　3D Hough Transform(线、面提取)、连通分析。<br>
  * 分类：基于点的分类，基于分割的分类，基于深度学习的分类（PointNet，OctNet）
* 高层次处理方法：<br>
①配准：点云配准分为粗配准（Coarse Registration）和精配准（Fine Registration）两个阶段。<br>
  * 精配准的目的是在粗配准的基础上让点云之间的空间位置差别最小化。应用最为广泛的精配准算法应该是ICP以及ICP的各种变种（稳健ICP、point to plane ICP、Point to line ICP、MBICP、GICP、NICP）。<br>
  * 粗配准是指在点云相对位姿完全未知的情况下对点云进行配准，可以为精配准提供良好的初始值。当前较为普遍的点云自动粗配准算法包括基于穷举搜索的配准算法和基于特征匹配的配准算法。<br>
    * 基于穷举搜索的配准算法：遍历整个变换空间以选取使误差函数最小化的变换关系或者列举出使最多点对满足的变换关系。如RANSAC配准算法、四点一致集配准算法（4-Point Congruent Set, 4PCS）、Super4PCS算法等……
    * 基于特征匹配的配准算法：通过被测物体本身所具备的形态特性构建点云间的匹配对应，然后采用相关算法对变换关系进行估计。如基于点FPFH特征的SAC-IA、FGR等算法、基于点SHOT特征的AO算法以及基于线特征的ICL等…
    <br>
  ②SLAM图优化<br>
    Ceres（Google的最小二乘优化库，很强大）， g2o、LUM、ELCH、Toro、SPA.<br>
    SLAM方法：ICP、MBICP、IDC、likehood Field、NDT<br>
  ③三维重建<br>
  泊松重建、 Delaunay triangulations、表面重建，人体重建，建筑物重建，树木重建。结构化重建：不是简单的构建一个Mesh网格，而是为场景进行分割，为场景结构赋予语义信息。场景结构有层次之分，在几何层次就是点线面。实时重建：重建植被或者农作物的4D（3D+时间）生长态势；人体姿势识别；表情识别；<br>
④点云数据管理：点云压缩，点云索引（KD、Octree），点云LOD（金字塔），海量点云的渲染。<br>
## 点云配准在三维重建流程中的位置：<br>
![](https://github.com/muyizaozao/Point-cloud-registration/blob/master/123.png)<br>
注：以上资料参考：https://blog.csdn.net/hongju_tang/article/details/85008888


# 本存储库收集：
-----
书籍和学术论文<br>
在线课程和视频<br>
离群数据集<br>
开源和商业图书馆/工具包<br>
重点会议和期刊<br>
`注`：由于作者仍处于学习阶段，知识浅薄，所以有错误请指正，作者将会持续扩充本库内容，谢谢！<br>

### 书籍和文献<br>
