# ViTの動かし方

## 環境の準備

### Docker環境の準備

[README](../README.md)を参考にDocker imageの準備を行ってください。

### Dockerの起動

[README](../README.md)を参考にこのリポジトリをマウントした状態でDockerを起動してください。

### Pythonの仮想環境の作成
Dockerが起動できたらPythonの仮想環境を作成します。
```
# apt install -y python3-venv
# python3 -m venv ${TVM_ROOT}/convert/venvs/ViT 
# . ${TVM_ROOT}/convert/venvs/ViT/bin/activate
(ViT) # pip install numpy==1.23.5 pandas==1.3.5 matplotlib==3.7.5 onnx==1.16.2 onnxruntime==1.18.1 onnxsim protobuf==3.20.* timm==1.0.9 torch==2.1.2 torchvision==0.16.2
```

## ONNXファイルのダウンロード

ViT(Vision Transformer)のtinyモデルのONNXファイルをダウンロードします。

```
(ViT) # cd ${TVM_ROOT}/convert
(ViT) # python download_vit_onnx.py \
    -s 1,3,224,224 \
    -n vit_tiny_patch16_224
(ViT) # onnxsim ./output/vit_tiny_patch16_224_onnx/vit_tiny_patch16_224.onnx ./output/vit_tiny_patch16_224_onnx/vit_tiny_patch16_224.onnx 
```

Pythonの仮想環境を終了します。
```
(ViT) # deactivate
```


## モデルのコンパイル

### RZ/V2H向けのコンパイル
以下のコマンドでRZ/V2H向けにコンパイルコンパイルすることができます。
`vit_tiny_onnx_npu`というフォルダにコンパイル後のファイルが出力されます。
```
# cd $TVM_ROOT/tutorials/

# python3 compile_onnx_model_quant.py ${TVM_ROOT}/convert/output/vit_tiny_patch16_224_onnx/vit_tiny_patch16_224.onnx -o vit_tiny_onnx_npu -t $SDK -d $TRANSLATOR -c $QUANTIZER --images $TRANSLATOR/../GettingStarted/tutorials/calibrate_sample/ -f float32 
```

### CPU向けのコンパイル
```

```


## アプリの実行