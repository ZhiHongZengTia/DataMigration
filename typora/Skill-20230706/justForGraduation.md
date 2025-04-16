













# for Graduation





## Chapter 1 Paper

20230907 IEEE 官网的 IEEE Transactions templates 没有修改



Step 1 -> ORCID：

Email：zhihongzegn@outlook.com

password: Neversay1v1r

first name: Zhihong

Step 2 -> IEEE



***

format requirements

- abstract 150~250 words



hyzhang0915@outlook.com











固态硬盘分区表类型MBR与GUID

- Window11 必须使用 GUID 分区类型，在C盘之前会有ESP(0), MSR(1) 两个引导分区，文件类型分别为 FAT32, MSR
- Window10 使用MBR分区类型，

安装系统之前，使用 MyHash 验证.iso镜像 MD5 完整性

















五险一金：

- 养老保险，医疗保险，失业保险，工伤保险，生育保险
- 住房公积金





犀浦站->都江堰站，24min，10元

黄龙溪古镇，57km

20230929 天府绿道，青龙湖

20231001 洛带古镇



***

待去：

深圳海上世界、世界之窗







[website](http://www.baidu.com)







鱼子西，20元/人清理费，海拔4.3km，高原反应，昼夜温差大，抗风保暖，日出、日落、星空，看雅拉雪山





各位同学，应学院要求，各班（团支部）须围绕“习近平总书记与大学生在一起”主题开展班级（团支部）活动，时间就定在今天18：00，不会占用大家太多时间（5分钟结束），大家有事的话也请麻烦进来挂着听一下（需要截图做会议记录），请各位同学按时参会，谢谢大家。腾讯会议号：568-346-044。





曾志宏：一篇SCI文献在审，团支部组宣委员



## Chapter 2 Experiment

### 2.1 Ubuntu 下 Python 环境搭建

安装位置：

/home/zhihongzeng/anaconda3



/bin jupyter-notebook



修改默认位置

- jupyter-notebook --generate-config
- vim ./home/zhihongzeng/.jupyter/jupyter_notebook_config.py
- ?notebook_dir 即可查找到
- 取消注释 c.NotebookApp.notebook_dir = ''，并添加新的路径







## Chapter 3 中期报告

























全局敏感性分析是研究如何从问题空间的全局视角将系统输出中的不确定性分配到系统输入中的不同不确定性来源。在许多应用中，所讨论的系统涉及一个或一组数学模型，使用计算机软件进行编码，模拟所研究的真实世界系统的功能。这样的数学模型可以是统计的（也称数据驱动的）、直接将输入映射到输出，或者机械的（也称为基于过程的），这样的系统通过解一组微分方程或其他数学方程来控制系统在时间和空间上的表现。

基于方差的全局敏感性分析方法作为一种定量的全局敏感性分析方法，是基于将模型的输出方差分解为增加维度的输入变量之和。然后考虑具体参数引起的方差对模型输出总方差的影响，从而确定每个输入变量的敏感性系数。假设一个数学模型可以表示为：$y=model(X)$



神经网络的结构搜索

最佳匹配滤波器，稀疏滤波器，具有稀疏的结构，每个模块的输入不是连续的

































## Chapter 4 Thesis













在线结构搜索，离线结构搜索策略

-------------------------------------------------------------------------------------------------

组会20231124：

AIC选择的是最大模块数

Neural network Via SA feedback,是一种在线模式反馈

本质=神经网络结构的自动搜索，而应该叫做基于敏感性分析的JPPSOV结构优化/建模

2022年的国内硕士，神经网络学习系统，主题：神经网络，敏感性分析反馈

模块输入减少了，而滤波器性能却更加有效。

\--------------------------------------------------------------------------------------------------





### 公式排版

- A4，21cm*29.7cm
- 新建样式





### 图片

使用emf格式插入word中

python 先生成 svg 格式图片，使用网站 cloudconvert.com/svg-to-emf 转化为 emf 格式







2023年12月7日，我在九教附近拾到一只虎皮鹦鹉，特征：未成年，亲人，不怎么爱飞，颜色整体为白色，局部为黑灰色和浅蓝色，没人陪它玩的时候喜欢一直叫。现暂寄居在9427，状态良好，希望失主在看到信息后，前来认领，联系方式：zhzeng@outlook.com

***

20231208 组会

围绕具体应用

通信信道均衡

输入信号，由特征换成了信号的延时



多元回归

自适应修剪，adaptive filter  via sensitivity feedback 换成在线结构优化

强调在滤波领域内，没有人做过这个工作，务必记住

因为，我们是把神经网络里面的方法拿过来使用，名词必须要改掉，自己造新名词

把应用找准了，依赖应用可以投相应的期刊

指数型非线性系统，做修改，如x(n-1)改成x(n-2)或者x(n-3)等等，这样子多做实验，增加工作量和说服力

如x(n-3)的话，最佳匹配滤波器是一个稀疏滤波器，那么其他方法优化出来的可能是连续的滤波器，优化不成稀疏的滤波器，那就说明我们方法具有新颖性

Hadi

complex value activation function



IPEMF structure 

low complexy even mirror fouringpip 非传播的结构

张师姐的Widely nonlinear quaternion-value second order volterra recursive least quares filter

可做工作，参考蒋师姐文章 non-prgatating pipelined SOV-RLS

Application：用于主动噪声控制，文章已经有了，就拿过来做一个应用

将师姐文章的WNL-QSOVRLS-DCD 应用到 non-prgatating pipelined SOV-RLS

文章命名：widely nonlinear Quaternion non-pagatating pipelined SOV-RLS

或者用于：non-progagation pi'p

分立核 split kernel，实部与虚部并联，使用两个 SAF

实数的组合步长算法应用到负值

频域里面样条自适应滤波就是负数



信道均衡就是处理复数

系统自适应辨识问题，插值平滑预测问题没有人做，是真实系统的近似，

(1) idea 1: apply WNL-QSOVRLS-DCD in [2] to non-propagating pipelined SOV-RLS proposed in [3]

​     the paper can be titled as widely nonlinear Quaternion non-propogating pipelined SOV-RLS

(2) idea 2: use split kernel, such as two SAF that is combined in parallel and process the real part and the imaginary part separtely

(3) motivations:  How is the combination step size algorithm applied to negative values in real numbers? In frequency domain, adaptive spline filtering is used to process negative numbers.

reference：

[1] Low-complexity even mirror fourier adaptive filter for nonlinear active noise control

[2] Widely nonlinear quaternion-valued second-order Volterra recursive least squares filter

[3] non-proPAgating pipelined SOV-RLS ... , to be published by Zhengchang Jiang

***

20240112组会

滤波器三种设计结构：

- 级联结构

- 并行结构

- 流水线结构

通信领域的应用：信道估计，信道均衡，信道估计，干扰消除

针对特定的应用，反过来研究基础理论

高级别期刊（一区）：information science，knowledge -base system，application software computing，Expert system and application

PNIIR，具有两种结构，一种是短期反馈，一种是长期反馈，即最后一个模块的反馈信号有区别

论文题目是最大的亮点

3月份，5月份硕士答辩，一共两次

















### 实验：

#### 第二章：自适应JPPSOV滤波器与敏感性分析方法概述

敏感性分析方法：LSA

1. 扰动法
2. Morris法

GSA:

1. Sobol法
2. EFAST法（FAST与EFAST）
3. ANCOVA（analysis of covariance）法，也叫 SCSA (structual and correlated sensitivity analysis)，Matlab 效果好
4. Delta（distributed-based，moment-independent，PDF（Delta法） 或者 CDF（PAWN法））

#### 第三章：基于JPPSOV自适应滤波器的敏感性分析框架







#### 第四章：基于全局敏感性分析的JPPSOV自适应滤波器结构优化



#### 第五章：基于全局敏感性分析的JPPSOV自适应滤波器结构在线优化

































