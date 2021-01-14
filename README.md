# DVC-Pipline
try dvc pipline from：https://dvc.org/doc/start/data-pipelines


## Install 
> pip install -r src/requirements.txt

## Access Data

* Step 1:
> dvc list https://github.com/iterative/dataset-registry get-started

* Step 2:
> dvc import https://github.com/iterative/dataset-registry get-started/data.xml -o data/data.xml

* Step 3:
> git add data/data.xml.dvc data/.gitignore

* Step 4:
> 如果要更新数据至最新版本：dvc update data/data.xml.dvc

## Data Pipline

* Step 1: prepare
> dvc run -n prepare -p prepare.seed,prepare.split -d src/prepare.py -d data/data.xml -o data/prepared python src/prepare.py data/data.xml

* Step 2: features
> dvc run -n featurize -p featurize.max_features,featurize.ngrams -d src/featurization.py -d data/prepared -o data/features python src/featurization.py data/prepared data/features

* Step 3: train  
如果不想执行dvc 命令，这直接按照`yaml`语言，编辑`dvc.yaml`文件.
编辑完后，执行以下命令运行：
> dvc repro

注意：在以上的`step`中，有些参数值，配置在了`params.yaml`文件中。如果想调整参数值，可以直接编辑`params.yaml`，然后再次运行`dvc repro`命令。例如改变`train`中的`n_estimators`为`100`， 这时`dvc repro`只会执行`train`步骤,其他步骤因为没有改变所以不会重新执行。如果再把`train`中的`n_estimators`改为原来的`50`，那么执行`dvc repro`时，并不会重新执行`train`步骤，因为`dvc`有缓存机制。

* Step 4: Visualize

建立了管道之后，我们需要一种了解其结构的好方法。 看到连接阶段的图形会有所帮助。 DVC让您无需离开终端就可以做到这一点！
执行就可以看到：
> dvc dag

+-------------------+  
| data\data.xml.dvc |  
+-------------------+  
          *  
          *  
          *  
     +---------+  
     | prepare |  
     +---------+  
          *  
          *  
          *  
    +-----------+  
    | featurize |  
    +-----------+  
          *  
          *  
          *  
      +-------+  
      | train |  
      +-------+  

## Experiments
使用DVC跟踪模型实验和不同实验下的模型指标的评估。
 * Step 1: evaluate  
 > dvc run -n evaluate -d src/evaluate.py -d model.pkl -d data/features -M scores.json --plots-no-cache prc.json python src/evaluate.py model.pkl data/features scores.json prc.json


 * Step 2: 进行实验  
 > 调整下参数：featurize的max_features改为1500， ngrams改为2。然后执行dvc repro.

 * Step 3：diff  
首先使用命令查看调整前后参数的变化：
> dvc params diff
调整参数后，模型的评估指标，例如AUC、recall等都会变化。 而通过以下命令查看调整前后的比较：
> dvc metrics diff  
当然也可以可视化：
> dvc plots diff -x recall -y precision





