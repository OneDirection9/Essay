# caffe

[caffe官方文档](https://github.com/BVLC/caffe/wiki/Solver-Prototxt)

caffe网络一般包含两个文件：`*.prototxt`和`*_solver.prototxt`，前面一个里面定义的是网络结构，后面一个定义的是网络学习率等参数。

# solver.prototxt

参数的含义如下：

- **net**：所用的网络的配置文件的路径及名称
   ```
   net: "pose_train_test.prototxt"
   ```

- **base_lr**：基础的学习率
   ```
   base_lr: 0.00001
   ```
   
- **momentum**：动量更新的参数
   ```
   momentum: 0.9
   ```

- **weight_decay**：正则化系数，用于防止过拟合。
   ```
   weight_decay: 0.001
   ```

- **lr_policy**：这个参数代表的是learning rate应该遵守什么样的变化规则，这个参数对应的是字符串，选项及说明如下：

   - `step`: 需要设置一个stepsize参数，返回 `base_lr * gamma ^ ( floor ( iter / stepsize ) )`，iter为当前迭代次数

   - `multistep`: 和step相近，但是需要stepvalue参数，step是均匀等间隔变化，而multistep是根据stepvalue的值进行变化

   - `fixed`: 保持base_lr不变

   - `exp`: 返回 `base_lr * gamma ^ iter`, iter为当前迭代次数

   - `poly`: 学习率进行多项式误差衰减，返回 `base_lr ( 1 - iter / max_iter ) ^ ( power )`
   
   - `sigmoid`: 学习率进行sigmod函数衰减，返回 `base_lr ( 1/ 1＋exp ( -gamma * ( iter - stepsize ) ) )`

- **gamma**：学习率衰减的参数

- **stepsize**：每隔多少次迭代降低学习率

- **display**：每隔多少次迭代显示结果

   ```
   display: 5 # 每隔5次显示迭代结果
   ```
   
- **max_iter**：最大迭代次数

- **snapshot**：快照。每隔多少次迭代保存结果
   ```
   snapshot: 5000 # 每隔5000次保存结果
   ```

- **snapshot_prefix**：保存快照的格式
   ```
   snapshot_prefix: "./caffemodel/pose"
   ```

- **solver_mode**：CPU或者GPU


***

Author: WarmerHan

Time: 2017/06/24
