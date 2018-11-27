## FourthDemo线性滤波实验报告

姓名：王刚

学号：16020031075

#### 实验过程：

1. 基本流程：
   (1) 将多光谱影像重采样到与全色影像具有相同的分辨率；

   (2) 将多光谱图像的Ｒ、Ｇ、Ｂ三个波段转换到IHS空间，得到Ｉ、Ｈ、Ｓ三个分量；

   (3) 以Ｉ分量为参考，对全色影像进行直方图匹配

   (4) 用全色影像替代Ｉ分量，然后同Ｈ、Ｓ分量一起逆变换到RGB空间，从而得到融合图像。

   RGB与IHS变换的基本公式：

2. 

   显然对上述六个卷积核的操作有很大的共同点，那么我将这个操作封装成函数

   ```c++
   void CalculateConvolution(int order, GDALDataset* input, GDALDataset* output, int N, double Multi)
   ```

   其中，order:确定使用哪一个convolutionKernel;input:输入图像;output:输出图像;N:convolutionKernel的大小;Multi:最终和的倍数。

3. 对于上述函数的具体实现，详见代码。具体过程就是，将input的三个波段遍历，对于每一个波段进行卷积核处理，然后写入到output中。



#### 实验中遇到的问题以及解决方法：

1. 对于得到的卷积核与像素值之和ans，若大于255或小于0的处理：
   一开始写成了：

   ```c++
   buffTmp[i * inputXlen + j] = (GByte)fabs((int)(ans*Multi));
   ```

   这种写法略显偏颇。正确的做法是，若当前的ans大于255直接将ans赋值为255， 若ans值为负，直接将其置为0即可：

   ```c++
   ans = ans * Multi;/// ans扩大Multi倍
   if (ans < 0)/// ans为负，直接置零
   ans = 0;
   if (ans > 255)/// ans大于255，直接置为255
   ans = 255;
   buffTmp[i * inputXlen + j] = (GByte)ans;/// 将buffTmp当前位置像素值置为ans
   ```

2. 对于卷积核的理解：

```
我认为，老师提供的课件中的
```

   ![img](https://github.com/Histra/FourthDemo/blob/master/FourthDemo/Pictures_ExperimentReport/01.png) 

   这张图片可能在某些方面有问题。

   问题如下：

   图像：
​	   ![img](https://github.com/Histra/FourthDemo/blob/master/FourthDemo/Pictures_ExperimentReport/02.png) 

   Kernel：
​	![img](https://github.com/Histra/FourthDemo/blob/master/FourthDemo/Pictures_ExperimentReport/03.png) 

   不应该计算【蓝色字体和Kernel对应相乘】

   而应计算【红色背景部分和Kernel对应相乘】  

   这样才能得到正确的图像锐化，浮雕和边缘检测。

#### 实验结果展示：

均值模糊：

![img](https://github.com/Histra/FourthDemo/blob/master/FourthDemo/Pictures_ExperimentReport/04.png) 

运功模糊：

![img](https://github.com/Histra/FourthDemo/blob/master/FourthDemo/Pictures_ExperimentReport/05.png) 

边缘检测：

![img](https://github.com/Histra/FourthDemo/blob/master/FourthDemo/Pictures_ExperimentReport/06.png) 

图像锐化：

![img](https://github.com/Histra/FourthDemo/blob/master/FourthDemo/Pictures_ExperimentReport/07.png) 

浮雕：

![img](https://github.com/Histra/FourthDemo/blob/master/FourthDemo/Pictures_ExperimentReport/08.png) 

高斯模糊：

![img](https://github.com/Histra/FourthDemo/blob/master/FourthDemo/Pictures_ExperimentReport/09.png) 

  

#### 实验反思总结：

​	这个实验是第四个实验。本次实验进行地并不是特别顺利。从data文件夹下面的try.jpg,try2.jpg,try3.jpg以及新建文本文档.txt可以看出，我至少进行了好多个小时的Debug。对于FourDemo_Probelm.docx中的问题（及上述问题2）的原因还是模棱两可。
​	通过这个实验，发现图像处理真的非常有趣，卷积核也非常有用。希望自己再接再厉，严谨治学！



