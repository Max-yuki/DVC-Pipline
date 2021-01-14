# DVC-Pipline
try dvc pipline from：https://dvc.org/doc/start/data-pipelines


## Access Data

* Step 1:
> dvc list https://github.com/iterative/dataset-registry get-started

* Step 2:
> dvc import https://github.com/iterative/dataset-registry get-started/data.xml -o data/data.xml

* Step 3:
> git add data/data.xml.dvc data/.gitignore

* Step 4:
> 如果要更新数据至最新版本：dvc update data/data.xml.dvc

