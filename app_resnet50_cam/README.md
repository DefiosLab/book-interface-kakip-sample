# Resnet50のサンプルアプリ

## ビルド
(readmeへのリンク)を参考に`data`ディレクトリに`app_resnet50_cam`ディレクトリをコピーしてください. その後Dockerを起動します.

以下のコマンドを実行してビルドすることができます.
```
$ cd data/app_resnet50_cam/src
$ mkdir build
$ cd build

$ cmake -DCMAKE_TOOLCHAIN_FILE=$TVM_ROOT/apps/toolchain/runtime.cmake  ..
$ make
```
以上により, `build`ディレクトリ内に実行ファイルが作成されます.


参考 : プロファイラを有効にするには リンク を参照してください

## 実行ファイルの配置

```
cd $TVM_ROOT/../
rm -r sample_resnet50_cam ; mkdir sample_resnet50_cam
cp $TVM_ROOT/obj/build_runtime/$PRODUCT/libtvm_runtime.so sample_resnet50_cam/
cp $TVM_ROOT/apps/exe/synset_words_imagenet.txt sample_resnet50_cam/
cp $TVM_ROOT/how-to/sample_app_v2h/app_resnet50_cam/src/build/app_resnet50_cam sample_resnet50_cam/
cp -r $TVM_ROOT/tutorials/resnet50_cam sample_resnet50_cam/
tar cvfz sample_resnet50.tar.gz sample_resnet50_cam/
```