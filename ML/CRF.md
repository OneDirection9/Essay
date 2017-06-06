NLP领域很出名的一个人Christopher Manning。

[CS109：概率论](https://web.stanford.edu/class/archive/cs/cs109/cs109.1166//handouts/overview.html)

[CS224N：NLP](http://web.stanford.edu/class/cs224n/syllabus.html)

# 条件随机场(CRF)

## crfpp使用

1. [crfpp的安装](http://midday.me/article/94d6bd4973264e1a801f8445904a810d)
    
    如果出现 Python.h: no such file or directory 错误。解决办法：

    ``` shell
    # For apt (ubuntu, debian...):
    sudo apt-get install python-dev  # for python2.x installs
    sudo apt-get install python3-dev  # for python3.x installs

    # For yum (centos, redhat, fedora...):
    sudo yum install python-devel
    ```
    
2. [crfpp的使用](http://www.52nlp.cn/%E4%B8%AD%E6%96%87%E5%88%86%E8%AF%8D%E5%85%A5%E9%97%A8%E4%B9%8B%E5%AD%97%E6%A0%87%E6%B3%A8%E6%B3%954)

## crfpp特征

[参考链接](http://hjdai.blogspot.com/2006/12/howto-use-crf.html)

模板文件示例如下：

```
# Unigram

    U00:%x[-2,0]

    U01:%x[-1,0]

    U02:%x[0,0]

    U03:%x[1,0]

    U04:%x[2,0]

    U05:%x[-2,0]/%x[-1,0]/%x[0,0]

    U06:%x[-1,0]/%x[0,0]/%x[1,0]

    U07:%x[0,0]/%x[1,0]/%x[2,0]

    U08:%x[-1,0]/%x[0,0]

    U09:%x[0,0]/%x[1,0]

 

    # Bigram

    B
```

在特征模板文件中，每一行(如U00:%x[-2,0]）代表一个特征，而宏“%x[行位置,列位置]”则代表了相对于当前指向的token的行偏移和列的绝对位置(下标都从0开始)。

```
1.    毎 k B
2.    日 k I
3.    新 k I < == 扫描到这一行，代表当前位置
4.    聞 k I
5.    社 k I
6.    特 k B
7.    別 k I
8.    顧 k B
9.    問 k I
10.   ４ n B
```

CRF\+\+里将特征分成两种类型，一种是Unigram的，“U”起头，另外一种是Bigram的，“B”起头。对于Unigram的特征，假如一个特征模板是“U01:%x[-1,0]”, CRF++会自动的生成一组特征函数(func1… funcN) 集合:

```
func1 = if (output = B and feature=”U01:日”) return 1 else return 0

func2 = if (output = I and feature=”U01:日”) return 1 else return 0

….

funcXX = if (output = B and feature=”U01:問”) return 1 else return 0

funcXY = if (output = I and feature=”U01:問”) return 1 else return 0
```

生成的特征函数的数目 = (L*N)，其中L是输出的类型的个数，这里是B，I这两个tag，N是通过模板扩展出来的所有单个字符串（特征）的个数，这里指的是在扫描所有训练集的过程中找到的日文字（特征）。

用來描述 bigram feature 的 template。藉由這個 template，最自動產生目前的輸出 token 和 前一個輸出 token（bigram）的結合。這種類型的 template 會產生總共 (L * L * N) 不同的 feature，L 是輸出的 class 數目，N 是被這個 template 產生的 unique feature 總數。當 class 的數量越大，這種 template 會產生大量的不同 feature，因此可能造成在 training 和 testing 上的效能低落。

***

Author: Zhipeng Han

Time: 2017/06/01
