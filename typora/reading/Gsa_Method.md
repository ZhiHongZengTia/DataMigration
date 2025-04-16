color

```
# red green blue
color='#F00CC5' 
color='#37F500'
color='#1887F0'
```

Local Sensitivity Analysis：

蒙特卡罗模拟法

eps or svg to emf





SA is defined as 

the study of how the uncertainty in the output of a model( numerical or otherwise) can be apportioned to different sources of uncertainty inthe model output.

1. design the experiment
2. assign density functions or ranges of variation to input factor
3. generate the input vector through the design
4. create the corresponding out distribution
5. assess the corresponding output distribution



### Distribution-based methods

Borgonovo方法中，首先采用核密度估计（KDE）方法求解$f_Y(y)$和$f_{Y|X_i}(y)$，该方法需要确定一个平滑参数和带宽参数

perceptive and insightful comments

| A new uncertainty importance measure                         | first proposed  | Borgonovo2007 |
| ------------------------------------------------------------ | --------------- | ------------- |
| Moment independent importance measures: New results and analytical test cases |                 | Borgonovo2011 |
| Global sensitivity measures from given data                  |                 | Borgonovo2013 |
| A new importance measure for sensitivity analysis            | use CDF firstly | Liu2012       |
|                                                              |                 |               |
|                                                              |                 |               |
|                                                              |                 |               |



The robustness of the SA indices

1. analysis robustness of all indices by error rates
2. analysis robustness of all indices by feature importance

 The efficiency of sensitivity computations



## Feature Seletion

A greedy wrapper method is backwards selection ( known as recursive feature feature elimination or RFE)







