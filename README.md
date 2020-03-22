# darknet yolov3 training (VoTT, VOC, VS2019)

## 1. 安装环境
|  software   | version  |
|  ----  | ----  |
| CUDA  | 10.1 |
| CUDNN  | 7.6.4 |
| OpenCV  | 4.2.0 |
| VS 2019 | 16.5.0 |
| Python  | 3.7.4 |
| VoTT  | 2.1.0 |


GPU版本  
1，CUDA 安装的时候要选择 vs 编译库 visual_studio_integration，还要拷贝到vs编译工具里  
2，还要设置cudnn安装步骤，把cudnn里面的文件都复制到CUDA目录下面，否则 cudnn.h 找不到  
3，opencv 4.2.0 里只有opencv2了，不过不影响使用，我用的是vc14里的lib  
4，打开darknet.sln提示升级–重定向项目，默认的，别动，也就是要升级匹配编译环境，否则也编译不过去，因为我只装了vs 2019  
5，vs 设置“最大并行生成项目数” = 1，否则还是编译不过去  
6，【最重要】的是，如果想用darknet 的 python 版本，还要按照上述编译darknet教程一步不差的设置，编译 yolo_cpp_dll.dll，然后就是缺啥库 就 pip install 吧，darknet.py 人家写好了，就不用pip install了  

## 2. VoTT 标注图片
导出 Export Settings -> Provider -> Pascal VOC

## 3. VOC 生成 darknet dataset，自动生成 voc_custom 目录
使用脚本  `python pascal_voc_to_label.py --data_dir=training\4\4 --imgaugloop=10 --augcheck` 生成 darknet format.
```
--data_dir #pascal voc directory
--imgaugloop  # image augmentation ，default：0，means no augmentation
--augcheck  # whether generate check image for augmentation
```
最终包含:
./voc_custom
│  coco_custom.names
│  train.txt
│  tree.txt
│  val.txt
│  voc_custom.data
│  yolov3_custom.cfg
│  
├─backup
│      yolov3_custom_1000.weights
│      yolov3_custom_last.weights
│      
└─labels
        IMG_1544.jpg
        IMG_1544.txt
        IMG_1544_aug_0.jpg
        IMG_1544_aug_0.txt
  **注意，要修改yolov3.cfg成yolov3_custom.cfg，修改里面的配置，然后放到 voc_custom 目录下**

## 4. 开始训练
命令`darknet detector train training\3\voc_custom\voc_custom.data training\3\voc_custom\yolov3_custom.cfg darknet53.conv.74`

darknet53.conv.74 需要单独下载

## 5. 最后，测试你的weight
命令 `python detect.py --config=training\3\voc_custom\yolov3_custom.cfg --weight=training\3\voc_custom\backup\yolov3_custom_10000.weights --meta=training\3\voc_custom\voc_custom.data --image=input\IMG_1876.jpg --output=output\IMG_1876_out.jpg`

