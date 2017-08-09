ROS Navigation Stack
====================

start from 6.12  
https://zhuanlan.zhihu.com/ros-nav  

### sketched map两篇论文的思路：  
*   修改下粒子滤波的过程，将pose(x,y,theta)多加两个  
    扩展为pose + (a,b)，也就是局部拉伸变形  
    里程计的距离也拉伸下  

论文的问题：  
*   quick change in the scale，因为scale不像pose一样  
    pose有测量保证单步之后是个高斯分布  
    scale也学人家pose，认为是原来的scale发散下  
    那变化快了就GG，localization丢了  


### 一个思路：相对参考系变换  
*   对于一次观测，变换scale，找到最配的  
    变换过程中应该是个凸函数  
    拿到所有观测中最好结果的scale后更新下odom  
    这样就抛弃了scale连续性假设  

*   但是这样运算量可能略大  
    还有一个方法是先找到最好的pose  
    然后看这个pose的观测的最佳scale  
    再用这个scale更新odom  
    这利用了一个假设：  
    局部变形让局部的所有pose同时降低可能性    
    相当于把手绘地图当成真的，odom不准  雷达的比例也不准  
    但是，虽然雷达不准，都是拉伸变的，  
    所以不改变pose的相对权重（存疑）  
    于是把odom搞发散点  
    观测到最好pose  
    然后求得scale  

*   根据odom发散点位置反求scale
    带入到pose的观测中运算  
    这样如果odom水平 垂直方向的scale就估计不出来了？  
    会影响pose权重吗？  
    需要采样一个维度的scale吗？  
    这些需要数学证明  