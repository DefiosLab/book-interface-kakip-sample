# Resnet50のサンプルアプリ

## 環境の準備

### Docker環境の準備

[README](../README.md)を参考にDocker imageの準備を行ってください．

### Dockerの起動

[README](../README.md)を参考にこのリポジトリをマウントした状態でDockerを起動してください．


## ビルド

Docker内の`/drp-ai_tvm/book-interface-kakip-sample`ディレクトリに本ディレクトリが存在することを確認します．
```
# cd /drp-ai_tvm/book-interface-kakip-sample
# ls
app_resnet50_cam
```

`app_resnet50_cam/src`内にビルドディレクトリを作成しビルドを実行します．
```
# cd app_resnet50_cam/src
# mkdir build
# cd build

# cmake -DCMAKE_TOOLCHAIN_FILE=$TVM_ROOT/apps/toolchain/runtime.cmake ..
# make
```
以上により，`build`ディレクトリ内に実行ファイルが作成されます．

```
# ls
app_resnet50_cam  CMakeCache.txt  CMakeFiles  cmake_install.cmake  Makefile
```

参考 : プロファイラを有効にするには[README](../README.md)を参照してください


## モデルのコンパイル

### RZ/V2H向けのコンパイル
以下のコマンドでRZ/V2H向けにコンパイルコンパイルすることができます．
`resnet50_cam_npu`というディレクトリにコンパイル後のファイルが出力されます．

```
# cd $TVM_ROOT/tutorials
# git restore compile_onnx_model_quant.py

# sed -i -e '/# Input shape helper/,/return \[batch_dim\] + \[int(d.get("dimValue")) for d in dim_info\[1::\]\]/d' compile_onnx_model_quant.py
# sed -i -e 's/get_input_shape(model_file, inp)/opts["input_shape"]/g' compile_onnx_model_quant.py
# sed -i -e '/ref_result_output_dir =/,/exist_ok=True)/d' compile_onnx_model_quant.py
# sed -i -e '/flatten().tofile(/d' compile_onnx_model_quant.py
# sed -i -e '/os.path.join(ref_result_output_dir, "input_"/d' compile_onnx_model_quant.py
# sed -i -e 's/ref_result_output_dir/output_dir/g' compile_onnx_model_quant.py
# sed -i -e 's/_fp32//g' compile_onnx_model_quant.py 
# sed -i -e 's/_fp16//g' compile_onnx_model_quant.py
# sed -i -e 's/FORMAT.BGR/FORMAT.YUYV_422/g' compile_onnx_model_quant.py
# sed -i -e 's/480, 640, 3/480, 640, 2/g' compile_onnx_model_quant.py

# python3 compile_onnx_model_quant.py $TRANSLATOR/../onnx_models/Resnet50v1_sparse90.onnx -o resnet50_cam_npu -t $SDK -d $TRANSLATOR -c $QUANTIZER -i input.1 --images $TRANSLATOR/../GettingStarted/tutorials/calibrate_sample/
```

### CPU向けのコンパイル
以下のコマンドでCPUH向けにコンパイルコンパイルすることができます．
`resnet50_cam_cpu`というディレクトリにコンパイル後のファイルが出力されます．

```
# cd $TVM_ROOT/tutorials
# git restore compile_cpu_only_onnx_model.py

# sed -i -e 's/FORMAT.BGR/FORMAT.YUYV_422/g' compile_cpu_only_onnx_model.py
# sed -i -e 's/480, 640, 3/480, 640, 2/g' compile_cpu_only_onnx_model.py

# python3 compile_cpu_only_onnx_model.py $TRANSLATOR/../onnx_models/Resnet50v1_sparse90.onnx -o resnet50_cam_cpu -s 1,3,224,224 -i input.1
```

## 実行ファイルの配置

実行ファイル`app_resnet50_cam/src/build/app_resnet50_cam`を`app_resnet50_cam/`ディレクトリ内にコピーし，Kakipに転送して実行してください．また，モデルは`resnet50_cam`ディレクトリ内に格納してください．

```
$ scp -r app_resnet50_cam <user>@<kakip-ip>:~/
```
```
$ cp -r resnet50_cam_npu resnet50_cam
```

## ViT tinnyの実行

本アプリを用いてViT tinnyモデルも実行することができます．詳細は[ViTの動かし方](./ViT.md)を確認してください．
