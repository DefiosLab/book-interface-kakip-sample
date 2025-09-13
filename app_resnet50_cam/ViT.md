# ViTの動かし方

## 環境の準備

### Docker環境の準備

[README](../README.md)を参考にDocker imageの準備を行ってください．

### Dockerの起動

[README](../README.md)を参考にこのリポジトリをマウントした状態でDockerを起動してください．

### Pythonの仮想環境の作成
Dockerが起動できたらPythonの仮想環境を作成します．
```
# apt install -y python3-venv
# python3 -m venv ${TVM_ROOT}/convert/venvs/ViT 
# . ${TVM_ROOT}/convert/venvs/ViT/bin/activate
(ViT) # pip install numpy==1.23.5 pandas==1.3.5 matplotlib==3.7.5 onnx==1.16.2 onnxruntime==1.18.1 onnxsim protobuf==3.20.* timm==1.0.9 torch==2.1.2 torchvision==0.16.2
```

## ONNXファイルのダウンロード

ViT(Vision Transformer)のtinyモデルのONNXファイルをダウンロードします．

```
(ViT) # cd ${TVM_ROOT}/convert
(ViT) # python download_vit_onnx.py \
    -s 1,3,224,224 \
    -n vit_tiny_patch16_224
(ViT) # onnxsim ./output/vit_tiny_patch16_224_onnx/vit_tiny_patch16_224.onnx ./output/vit_tiny_patch16_224_onnx/vit_tiny_patch16_224.onnx 
```

Pythonの仮想環境を終了します．
```
(ViT) # deactivate
```


## モデルのコンパイル

### RZ/V2H向けのコンパイル
以下のコマンドでRZ/V2H向けにコンパイルコンパイルすることができます．
`vit_tiny_onnx_npu`というディレクトリにコンパイル後のファイルが出力されます．
```
# cd $TVM_ROOT/tutorials/
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

# python3 compile_onnx_model_quant.py ${TVM_ROOT}/convert/output/vit_tiny_patch16_224_onnx/vit_tiny_patch16_224.onnx -o vit_tiny_onnx_npu -t $SDK -d $TRANSLATOR -c $QUANTIZER --images $TRANSLATOR/../GettingStarted/tutorials/calibrate_sample/ -f float32 
```

### CPU向けのコンパイル
以下のコマンドでCPUH向けにコンパイルコンパイルすることができます．
`vit_tiny_onnx_cpu`というディレクトリにコンパイル後のファイルが出力されます．
```
# cd $TVM_ROOT/tutorials/
# git restore compile_cpu_only_onnx_model.py

# sed -i -e 's/FORMAT.BGR/FORMAT.YUYV_422/g' compile_cpu_only_onnx_model.py
# sed -i -e 's/480, 640, 3/480, 640, 2/g' compile_cpu_only_onnx_model.py

# python3 compile_cpu_only_onnx_model.py ${TVM_ROOT}/convert/output/vit_tiny_patch16_224_onnx/vit_tiny_patch16_224.onnx -o vit_tiny_onnx_cpu -s 1,3,224,224 -i input
```


## アプリの実行

今回使うViT tinyのモデルはResNet50と同じImageNetで学習されたモデルのため`app_resnet50_cam`でそのまま実行することができます．

まず，RZ/V2H向けにコンパイルしたモデルと，CPU向けにコンパイルしたモデルをKakipに転送します．その後`vit_tiny_onnx_npu`もしくは`vit_tiny_onnx_cpu`を`resnet50_cam`という名前で複製し，実行ファイルのあるディレクトリに格納し，アプリを実行してください．

```
$ scp -r vit_tiny_onnx_* <user>@<kakip-ip>:~/app_resnet50_cam/
```

```
$ cp -r vit_tiny_onnx_npu resnet50_cam
$ ./app_resnet50_cam
```