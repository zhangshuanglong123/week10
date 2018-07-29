## 一、心得体会

本次作业练习主要熟悉对fcn-8s算法。我对FCN的理解，以VGG16为例。FCN的前五层结构和VGG16的结构一致，只是将后面的全连接变成了全卷积，而8Xfcn的实现通过pool5进行上采样和pool4进行分类操作后叠加后再进行2倍上采样，继续和pool3进行叠加继续2倍上采样，直到和pool2的分类后叠加操作完毕后，再进行一个8倍上采样还原到和输入图片一样的尺寸的大小。这样的好处，只是使用原来模型中的每个pool后的feature_map，未改变原来的结构，可以使用原来的训练的参数，减少计算量。还有这样能达到比直接使用32倍的上采样效果更好。 





**数据集以及预训练模型地址**：https://www.tinymind.com/code-wxy/datasets/week10-fcn

**训练模型地址**：https://www.tinymind.com/code-wxy/week10

**TinyMind运行完后输出的预测图片**：https://www.tinymind.com/executions/xos0qxzh/output/eval

**以下为部分日志：**

```
2018-07-26 17:21:07,825 - DEBUG - train.py:312 - step 2950 Current Loss: 181.60231018066406 
2018-07-26 17:21:07,825 - DEBUG - train.py:314 - [6.83] imgs/s
2018-07-26 17:21:19,435 - DEBUG - train.py:312 - step 2960 Current Loss: 113.5374984741211 
2018-07-26 17:21:19,435 - DEBUG - train.py:314 - [6.89] imgs/s
2018-07-26 17:21:31,331 - DEBUG - train.py:312 - step 2970 Current Loss: 90.49767303466797 
2018-07-26 17:21:31,331 - DEBUG - train.py:314 - [6.73] imgs/s
2018-07-26 17:21:43,012 - DEBUG - train.py:312 - step 2980 Current Loss: 126.39532470703125 
2018-07-26 17:21:43,012 - DEBUG - train.py:314 - [6.85] imgs/s
2018-07-26 17:21:54,808 - DEBUG - train.py:312 - step 2990 Current Loss: 89.2998046875 
2018-07-26 17:21:54,809 - DEBUG - train.py:314 - [6.78] imgs/s
2018-07-26 17:22:06,283 - DEBUG - train.py:312 - step 3000 Current Loss: 115.31446838378906 
2018-07-26 17:22:06,283 - DEBUG - train.py:314 - [6.97] imgs/s
2018-07-26 17:22:08,261 - DEBUG - train.py:321 - Model saved in file: /output/train/model.ckpt-3000
2018-07-26 17:22:08,261 - DEBUG - train.py:328 - validation generated at step [3000]
2018-07-26 17:22:35,927 - DEBUG - train.py:347 - Model saved in file: /output/train/model.ckpt-3000
```



## 二、实现过程

### 1.  训练数据准备

本次使用的数据集是Pascal2 VOC2012的数据中，语义分割部分的数据。

VOC网址：<http://host.robots.ox.ac.uk/pascal/VOC/voc2012/>

VOC2012训练和测试数据集下载：[training/validation data](http://host.robots.ox.ac.uk/pascal/VOC/voc2012/VOCtrainval_11-May-2012.tar) 

将**convert_fcn_dataset.py** 脚本代码进行完善后在Ubuntu环境下运行如下命令运行。其中我把dataset_util.py这个文件单独从objection_detection中复制出来利用。

```
python3 convert_fcn_dataset.py --data_dir=./data/VOCdevkit/VOC2012/ --output_dir=./ 
```

运行后生成**fcn_train.record**,  **fcn_val.record** 两个文件。上传至Tiny Mind数据集备用。



### 2. 预训练模型下载

在slim的说明文档中找到vgg16的预训练模型下载地址：[vgg_16_2016_08_28.tar.gz](http://download.tensorflow.org/models/vgg_16_2016_08_28.tar.gz) 

（slim说明文档：https://github.com/tensorflow/models/blob/master/research/slim/README.md）

下载后将解压的vgg_16.ckpt文件上传至Tiny Mind数据集备用。



### 3. 8Xfcn 代码完善

在代码的第114行左右增加了上采样代码。

过程如心得描述。