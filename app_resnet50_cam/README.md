# Resnet50のサンプルアプリ

## 環境の準備

### Docker環境の準備

[README](../README.md)を参考にDocker imageの準備を行ってください。

### 作業フォルダの準備

`data`フォルダを任意の場所に作成し、本フォルダ(`app_resnet50_cam`)をコピーしてください。

### Dockerの起動

[README](../README.md)を参考に`data`フォルダをマウントした状態でDockerを起動してください。


## ビルド

Docker内の`/drp-ai_tvm/data`フォルダに本フォルダが存在することを確認します。
```
$ cd /drp-ai_tvm/data
$ ls
app_resnet50_cam
```

ビルドフォルダを作成しビルドを実行します。
```
$ mkdir build
$ cd build

$ cmake -DCMAKE_TOOLCHAIN_FILE=$TVM_ROOT/apps/toolchain/runtime.cmake  ..
$ make
```
以上により、`build`フォルダ内に実行ファイルが作成されます。

```
$ ls
app_resnet50_cam  CMakeCache.txt  CMakeFiles  cmake_install.cmake  Makefile
```

参考 : プロファイラを有効にするには[README](../README.md)を参照してください

## 実行ファイルの配置

実行ファイル`app_resnet50_cam/src/build/app_resnet50_cam`を`app_resnet50_cam`フォルダ内にコピーし、Kakipに転送して実行してください。また、モデルは`resnet50_cam`フォルダ内に格納してください。

```
$ scp -r app_resnet50_cam <user>@<kakip-ip>:~/
```
