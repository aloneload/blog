---
title: 线性代数
categories:
- 其他
tags:
- 数学知识
- 线性代数
---
 1. 笛卡尔平面直角坐标系：如果这坐标系看成是点的空间，那每个点都有坐标（x,y），从原点到（x,y）的有向线段为向量**a**(x,y),那这个空间中的点最少用几个怎么样关系的向量，然后通过向量运算得出呢，答案是只要不共线的两向量（非线性），这个空间也称为这两向量张成的空间。**i**(1,0)和**j**(0,1)便是符合标准之一，称为基底，特别的，这两向量正交**ij**=0(向量点积为0)，向量长度为1（欧几里得范数），变成了标准正交向量

 2. 矩阵：想象这个平面，对它进行各种变换，有三种情况：
     4. 它还是一个平面，只不过发生着拉伸旋转等（二维）
     1. 它变成一条线，此时原来平面上的向量有的成了点，有的依然是向量（一维）
     1. 既然能变成线，在变换一下变成了点（零维）

         **那这种变换怎么表示呢，如果有了表示是不是研究这种表示的性质可以控制平面变换呢？这种表示就是矩阵，现在要把矩阵当成动词（对空间的变换）来看待了**

 3. 线性相关：如果一组向量中的 任意一个向量都不能表示成其他向量的线性组合，那么这组向量被称为 线性无关。如果某个向量是一组向量中某些向量的线性组合，那么我 们将这个向量加入到这组向量后不会增加这组向量的生成子空间。
 4. 线性变换后原点不变，关系不变（直线依然是直线，平行的依然平行）

 5. 列空间：把二维矩阵看成两个列向量，这两个列向量的线性组合，就构成了A的列空间（列向量张成的空间）

 6. 零矩阵：将空间缩成一个点

 7. 单位矩阵：平面经过I=((1,0),(0,1))（二维单位矩阵）后，横向拉伸1，纵向拉伸1，原平面不变，于是有A=AI

 8. 逆矩阵：平面经过变换后只要是上述情况2.1，就可以逆向的变换回来，情况2.2存在信息丢失，好多个平面都可以变成直线，因此变不回来了，情况2.3信息丢失更严重，于是以下公式容易理解了
    inv(A)A=I
   其中inv(A)是A的逆矩阵 （这辈子你都忘不了这个公式了，对平面操作之后逆向操作，等于没操作）


 9. 行列式：考虑平面上原来面积为1的图形，变换后面积增大的倍率就是行列式的意义,如果平面经过变换后成直线或者点，那么行列式det=0

 10. 对角矩阵：diag(2,3)分别沿着x,y扩大2，3倍，面积扩大6倍，行列式det=6


 11. 点积：两个矩阵点积就是两个操作，先进行a操作，在进行b操作，当然和先进行b操作，后进行a操作不一样了，于是矩阵左乘右乘结果当然不一样了，不满足交换律

 12. 秩：线性变换后空间的维数，称之为秩rank()。秩与列数相等，称为满秩矩阵

 13. 零空间：变换后压缩到原点的向量集合，称为零空间

 14. 非奇异矩阵：如果变换后空间没有被扁平化，可逆，称为非奇异矩阵

 15. 奇异矩阵，当变换矩阵线性相关，变换后成为一维直线，秩为1，此时矩阵称为奇异矩阵，这个时候必定有向量成了零向量，且零向量的维度为1

 16. 维数定理：对于m*n的矩阵A列数=秩+零向量的维数  n=dim Ker(A) + rank(A)

 17. 特征向量：是指与 A 相乘后相当于对该向量进行缩放的非零向量 v
     
     ``` Av = λv ```
     
     标量 λ 被称为这个特征向量对应的 特征值 构建具有特定特征值和特征向量的矩阵，能够使我们在目标方向上延伸空间
     A = Vdiag(λ)inv(V)

 18. 奇异分解： 
     
     ``` A = UDV⊤ ```
     
     假设 A 是一个 m×n 的矩阵，那么 U 是一个 m×m 的矩阵，D 是一个 m×n 的矩阵，V 是一个 n × n 矩阵。矩阵 U 和 V 都被定义为正交矩阵，而矩阵 D 被定义为对角矩阵，对角矩阵 D 对角线上的元素被称为矩阵 A 的 奇异值。矩阵 U的列向量被称为 左奇异向量，矩阵 V 的列向量被称 右奇异 向量。SVD 最有用的一个性质可能是拓展矩阵求逆到非方矩阵

 19. 伪逆：
     
     ``` A+ = VD+U⊤（+代表伪逆） ``` 
     其中，矩阵 U，D 和 V 是矩阵 A奇异值分解后得到的矩阵。对角矩阵 D 的伪逆D+ 是其非零元素取倒数之后再转置得到的。 当矩阵 A 的列数多于行数时，使用伪逆求解线性方程是众多可能解法中的一 种。特别地，x =  A+y 是方程所有可行解中欧几里得范数 ∥x∥2 最小的一个。 当矩阵 A 的行数多于列数时，可能没有解。在这种情况下，通过伪逆得到的  x 使得 Ax 和 y 的欧几里得距离 ∥Ax−y∥2 最小

 20. 矮矩阵与长矩阵：以上都是方阵，行数小于列数的矮矩阵，实际上是将一个高维向量压缩到低纬度，有无穷解，行数大于列数的长矩阵，低纬度向高维转化，仅当向量恰好在低维度所在的空间里有解
 
 21. 求解Ax=b：两边左乘逆
           
         
         ``` inv(A)Ax=b     Ix=inv(A)b    x=inv(A)b  ```
         
            这便是线性方程解法，当然，逆有可能不存在
        
     
      1. 唯一解：就是向量x,经过线性变化后就是向量b,自然这种变换要求变换后还是平面，及行列式det!=0
     
       
     
      1. 无穷多个解：
         如果变换后成直线且与b向量共线，变换前与b向量相对应的事特解，变换前与被压缩成点成为零向量的向量是通解，这些线性相关的零空间解加特解就是通解
     
       
     
      1. 无解：如果变换后成直线且落不到b向量上，那就无解了

 22. 求解方程步骤：当逆变换存在时，用逆变换求解方程，当逆变换不存在，等价于行列式为0，看b向量是否落在A列空间里了

 23. 方程是否有解：在长矩阵中，为了使方程 Ax = b 对于任意向量 b ∈ Rm 都存在解，我们要求 A 的列空间构 成整个 Rm。如果 Rm 中的某个点不在 A 的列空间中，那么该点对应的 b 会使得 该方程没有解。矩阵 A 的列空间是整个 Rm 的要求，意味着 A 至少有 m 列，即 n ≥ m。否则，A 列空间的维数会小于 m。例如，假设 A 是一个 3 × 2 的矩阵。目 标 b 是 3 维的，但是 x 只有 2 维。所以无论如何修改 x 的值，也只能描绘出 R3 空 间中的二维平面。当且仅当向量 b 在该二维平面中时，该方程有解。不等式 n ≥ m 仅是方程对每一点都有解的必要条件。这不是一个充分条件，因 为有些列向量可能是冗余的。假设有一个 R2×2中的矩阵，它的两个列向量是相同 的。那么它的列空间和它的一个列向量作为矩阵的列空间是一样的。换言之，虽然 该矩阵有 2
 列，但是它的列空间仍然只是一条线，不能涵盖整个 R2 空间。

**以上都基于二维分析，高维分析时可类似考虑**
