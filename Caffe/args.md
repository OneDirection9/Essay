# 命令执行格式

[官方接口文档](http://caffe.berkeleyvision.org/tutorial/interfaces.html)

使用caffe的命令格式如下：

```shell
caffe <command> <args>
```

> 详细的 command, args 选项可以用 `--help` 选项调出。

**Example**

```shell
# train
#!/usr/bin/env sh
./build/tools/caffe train --solver=examples/mnist/lenet_solver/prototxt

# test
./build/tools/caffe.bin test -model exmaples/mnist/lenet_train_test.prototxt -weights examples/mnist/lenet_iter_10000.caffemodel -iterations 100
```


# command

command总共有以下4种：

- `train`: 训练或者finetunue模型；

- `test`: 测试模型；

- `device_query`: 显示GPU信息；

- `time`: 显示程序执行时间。


# arguments

args列举了以下几种：


## train

- `-solver`：**必选参数**。一个protocol buffer类型的文件，即模型的配置文件。如：

   ```
   # ./build/tools/caffe train -solver examples/mnist/lenet_solver.prototxt
   ```

- `-gpu`: 可选参数。该参数用来指定用哪一块gpu运行，根据gpu的id进行选择，如果设置为`-gpu all`则使用所有的gpu运行。如使用第二块gpu运行：

   ```
   # ./build/tools/caffe train -solver examples/mnist/lenet_solver.prototxt -gpu 2
   ```

- `-snapshot`:可选参数。该参数用来从快照（snapshot)中恢复训练。可以在solver配置文件设置快照，保存solverstate。如：

   ```
   # ./build/tools/caffe train -solver examples/mnist/lenet_solver.prototxt -snapshot examples/mnist/lenet_iter_5000.solverstate
   ```

- `-weights`:可选参数。用预先训练好的权重来fine-tuning模型，需要一个caffemodel，不能和`-snapshot`同时使用。如：

   ```
   # ./build/tools/caffe train -solver examples/finetuning_on_flickr_style/solver.prototxt -weights models/bvlc_reference_caffenet/bvlc_reference_caffenet.caffemodel
   ```

- `-iterations`: 可选参数，迭代次数，默认为50。如果在配置文件文件中没有设定迭代次数，则默认迭代50次。

- `-model`:可选参数，定义在protocol buffer文件中的模型。也可以在solver配置文件中指定。

- `-sighup_effect`: 可选参数。用来设定当程序发生挂起事件时，执行的操作，可以设置为`snapshot`, `stop`或`none`, 默认为`snapshot`

- `-sigint_effect`: 可选参数。用来设定当程序发生键盘中止事件时（ctrl+c), 执行的操作，可以设置为`snapshot`, `stop`或`none`, 默认为`stop`


## test

test参数用在测试阶段，用于最终结果的输出，要模型配置文件中我们可以设定需要输入accuracy还是loss. 假设我们要在验证集中验证已经训练好的模型，就可以这样写

   ```
   # ./build/tools/caffe test -model examples/mnist/lenet_train_test.prototxt -weights examples/mnist/lenet_iter_10000.caffemodel -gpu 0 -iterations 100
   ```

这个例子比较长，不仅用到了test参数，还用到了`-model`, `-weights`, `-gpu`和`-iteration`四个参数。意思是利用训练好了的权重（-weight)，输入到测试模型中(-model)，用编号为0的gpu(-gpu)测试100次(-iteration)。

## time

time参数用来在屏幕上显示程序运行时间。如：

```shell
# 这个例子用来在屏幕上显示lenet模型迭代10次所使用的时间。包括每次迭代的forward和backward所用的时间，也包括每层forward和backward所用的平均时间。
./build/tools/caffe time -model examples/mnist/lenet_train_test.prototxt -iterations 10

# 这个例子用来在屏幕上显示lenet模型用gpu迭代50次所使用的时间。
./build/tools/caffe time -model examples/mnist/lenet_train_test.prototxt -gpu 0`

# 利用给定的权重，利用第一块gpu，迭代10次lenet模型所用的时间。
./build/tools/caffe time -model examples/mnist/lenet_train_test.prototxt -weights examples/mnist/lenet_iter_10000.caffemodel -gpu 0 -iterations 10
```


## device_query

```shell
./build/tools/caffe device_query -gpu 0
```

多GPU：
```shell
# 这两个例子表示： 用两块或多块GPU来平行运算，这样速度会快很多。但是如果你只有一块或没有gpu, 就不要加-gpu参数了，加了反而慢。
./build/tools/caffe train -solver examples/mnist/lenet_solver.prototxt -gpu 0,1
./build/tools/caffe train -solver examples/mnist/lenet_solver.prototxt -gpu all
```

最后，在linux下，本身就有一个time命令，因此可以结合进来使用，因此我们运行mnist例子的最终命令是(一块gpu)：

```shell
sudo time ./build/toos/caffe train -solver examples/mnist/lenet_solver.prototxt
```

***

Author: WarmerHan

Time: 2017/06/28
