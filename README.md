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
注：以上资料参考：https://blog.csdn.net/hongju_tang/article/details/85008888<br>
其他资料请参见：<br>
1.	关于三维计算机视觉的研究内容：<br>
https://www.cnblogs.com/yhlx125/p/4952522.html<br>
2.	关于点云数据处理方法：（三维计算机视觉中点云数据处理面对的问题，主要方法和技术，概述其特点。）<br>
https://blog.csdn.net/pdw521/article/details/82492428


# 本存储库收集：<br>
-----
* 书籍和文献<br>
* 课程和博客<br>
* 数据集<br>
* 开源工具包<br>
* 重点会议和期刊<br>
`注`：由于作者仍处于学习阶段，知识浅薄，所以有错误请指正，作者将会持续扩充本库内容，谢谢！<br>

### 书籍和文献<br>
-----
书籍：由于纯粹的点云配准技术发展的并不是很成熟，所以关于点云配准的书籍目前很少，我了解的书籍只有一本国防工业出版社的《点云数据配准及曲面细分技术》讲解了点云配准的相关技术，但主要内容稍显过时，适合没有基础的小白看看，参考意义不多。<br>
笔者主要还是从博客和硕士博士论文中学习。
<br>文献：<br>
1.	朱琛琛. 基于ICP算法的点云配准研究[D]. 2019.（注：基于摄像技术的点云配准）<br>
2.	点云配准若干问题研究[D].2018.  （注：基于雷达激光的点云配准）<br>
3.	Besl P J , Mckay H D . A method for registration of 3-D shapes[J]. IEEE Transactions on Pattern Analysis and Machine Intelligence, 1992, 14(2):0-256.<br>
4.	Aiger D , Mitra N J , Cohen-Or D . 4-points congruent sets for robust pairwise surface registration[J]. ACM Transactions on Graphics, 2008, 27(3):1.<br>
5.	Automatic registration of large-scale urban scene point clouds based on semantic feature points[J]. ISPRS Journal of Photogrammetry and Remote Sensing, 2016, 113:43-58.<br>
### 课程与博客<br>
-----
1）点云配准算法的说明与流程介绍：<br>
https://blog.csdn.net/Ha_ku/article/details/79755623<br>
2）几种点云配准算法的方法的介绍与比较：<br>
https://blog.csdn.net/weixin_43236944/article/details/88188532<br>
3）三维点云用机器学习的方法进行处理：<br>
https://blog.csdn.net/u014636245/article/details/82755966<br>
4）以一个例子详细介绍了点云配准的过程：<br>
https://www.zhihu.com/question/34170804<br>
### 数据集<br>
-----
1.	The Stanford 3D Scanning Repository（斯坦福大学的3 d扫描存储库）<br>
链接：http://graphics.stanford.edu/data/3Dscanrep/<br>
这应该是做点云数据最初大家用最多的数据集，其中包含最开始做配准的Bunny、Happy Buddha、Dragon等模型。<br>
2.	Shapenet<br>
ShapeNet是一个丰富标注的大规模点云数据集，其中包含了55中常见的物品类别和513000个三维模型。<br>
3.	The KITTI Vision Benchmark Suite<br>
链接：http://www.cvlibs.net/datasets/kitti/<br>
这个数据集来自德国卡尔斯鲁厄理工学院的一个项目，其中包含了利用KIT的无人车平台采集的大量城市环境的点云数据集（KITTI），这个数据集不仅有雷达、图像、GPS、INS的数据，而且有经过人工标记的分割跟踪结果，可以用来客观的评价大范围三维建模和精细分类的效果和性能。<br>
4.	Robotic 3D Scan Repository<br>
链接：http://kos.informatik.uni-osnabrueck.de/3Dscans/<br>
这个数据集比较适合做SLAM研究，包含了大量的Riegl和Velodyne雷达数据<br>
5.	佐治亚理工大型几何模型数据集<br>
链接：https://www.cc.gatech.edu/projects/large_models/<br>
6.	PASCAL3D+<br>
链接：http://cvgl.stanford.edu/projects/pascal3d.html<br>
包含了12中刚体分类，每一类超过了3000个实例。并且包含了对应的imageNet中每一类的图像。<br>
7.	还有其他的数据集下面有更全面的总结：<br>
链接：https://github.com/timzhang642/3D-Machine-Learning<br>
### 开源工具<br>
1.	MeshLab<br>
简介：是一款开源、可移植和可扩展的三维几何处理系统。主要用于处理和编辑3D三角网格，它提供了一组用于编辑、清理、修复、检查、渲染、纹理化和转换网格的工具。提供了处理由3D数字化工具/设备生成的原始数据以及3D打印功能，功能全面而且丰富。MeshLab支持多数市面上常见的操作系统，包括Windows、Linux及Mac OS X，支持输入/输出的文件格式有：STL 、OBJ 、 VRML2.0、U3D、X3D、COLLADA
MeshLab可用于各种学术和研究环境，如微生物学、文化遗产及表面重建等。<br>
2.	几个ICP开源库<br>
1）	SLAM6D<br>
链接：http://slam6d.sourceforge.net/<br>
2）	Libicp<br>
链接：http://www.cvlibs.net/software/libicp/<br>
3）	libpointmatcher<br>
链接：https://github.com/ethz-asl/libpointmatcher<br>
   4）g-icp<br>
       链接：https://github.com/avsegal/gicp<br>
   5）n-icp<br>
链接：http://jacoposerafin.com/nicp/<br>
### 重点会议与期刊<br>
点云配准主要应用于工业制造业的逆向工程、古文物修复、医学三维图像构建等领域。研究内容是属于计算机视觉领域的研究范畴。国际上的会议如计算机视觉三大顶会ICCV、CVPR、ECCV等都会有相关技术，除此之外，还有ACCV、BMVC、SSVM等认可度也比较高。<br>
期刊的话，IEEE旗下的TPAMI，TIP等，还有SIAM Journal Image Sciences，Springer那边有IJCV。





