
## 【预备任务】
1. tensorflow安装

推荐Anaconda（针对自己操作系统和位数下载对应版本）；推荐用conda create创建对应的python环境（注：某些python版本可能不支持tensorflow）；通过pip install来安装tensorflow。

参考：

[tensorflow安装教程](http://www.tensorflownews.com/series/tensorflow-install-tutorial/)

2.keras安装：
在安装 Keras 之前，请安装以下后端引擎之一：TensorFlow，Theano，或者 CNTK。我们推荐 TensorFlow 后端。避免麻烦，安装其中一个后端即可，不需要安装所有后端。
[参考资料](https://keras-zh.readthedocs.io/)

## Task1 快速了解keras (2days)
keras的优势，以及keras模型构建的两种方式，Sequential 模型函数式API。
1. [关于keras模型](https://keras-zh.readthedocs.io/models/about-keras-models/)
2. [Keras FAQ: 常见问题解答](https://keras-zh.readthedocs.io/getting-started/faq/)
3. [Sequential 模型](https://keras-zh.readthedocs.io/models/sequential/)
通过将网络层实例的列表传递给 Sequential 的构造器，来创建一个 Sequential 模型。
4. [model函数API](https://keras-zh.readthedocs.io/models/model/)
函数式 API 是定义复杂模型（如多输出模型、有向无环图，或具有共享层的模型）的方法，利用函数式 API创建一个模型。
5. [常用数据集](https://keras-zh.readthedocs.io/datasets/)(数据集提前下载后续就可以使用)
6. 经典样例（任选两个跑一下，观察代码的实现，都包含有哪些结构，对于一个完整的keras模型有总体的认知，在后续学习中可以进行对应。）


[参考资料：](https://keras-zh.readthedocs.io/why-use-keras/)
