## memo ToDo
- [ ] Installing DRP-AI TVM with Docker
- [ ] app_resnet50_camの中身
  - [ ] コンパイルの方法もいる
- [ ] ProfileRunの話
  - [ ] profile_reader
- [ ] ViTの中身(必要なら)



# DRP-AI TVM のインストール

## 準備

以下のファイルを作業ディレクトリに用意する.

- [DRP-AI Translator i8](https://www.renesas.com/software-tool/drp-ai-translator-i8) v1.10以降
- [RZ/V2H AI Software Development Kit](https://www.renesas.com/software-tool/rzv2h-ai-software-development-kit) v5.20以降

※ダウンロードにはRenesasのアカウントが必要です

## Dockerfileのダウンロード
```
$ wget https://raw.githubusercontent.com/renesas-rz/rzv_drp-ai_tvm/main/DockerfileV2H -O DockerfileV2H
```
memo : Dokerfileもこのリポジトリに含める?

## docker image のビルド
```
$ unzip RTK0EF018?F0??00SJ.zip
$ mv `find ./ -name "*toolchain*sh"` .
$ docker build -t drp-ai_tvm_v2h_image_${USER} -f Dockerfile* --build-arg PRODUCT=V2H .
```

## docker image の実行
以下のコマンドにより, ホストの`$(pwd)/data`がDokerコンテナの`/drp-ai_tvm/data`にマウントされた状態で起動することができます.
Dockerコンテナとホストとの間でのファイルのやり取りに使用してください.
```
$ docker run -it --name drp-ai_tvm_v2h_container_${USER} -v $(pwd)/data:/drp-ai_tvm/data drp-ai_tvm_v2h_image_${USER}
```

# プロファイルの取得方法
## プロファイラの実行
DRP-AI TVMのプロファイルング機能はCPU/DRP-AIに割り当てられた各サブグラフの処理時間を計測できる機能です.

プロファイリング機能を使用するには, アプリケーションのコード内の推論を実行する関数`Run()`の代わりに`ProfileRun()`を呼び出します. 

それぞれのアプリのコンパイル方法は, リンク を参照してください.

```
codeのdiff
```

この変更を行ったうえでアプリを実行すると, 同じディレクトリ内に以下のファイルが生成されます.
- `profile_table.txt`
  - CPU/DRP-AIに割り当てられた各サブグラフの処理時間が含まれています
- `profile.csv`
  - `profile_table.txt`と同じ内容がCSV形式で含まれています


## プロファイリングデータの集計
`profile_reader.py`を用いてプロファイリングデータの集計を行うことができます.
スクリプトは以下のデータを表示します.
- CPU/DRP-AIのそれぞれで処理されるサブグラフの数
- CPU/DRP-AIのそれぞれに割り当てられた処理の割合
- CPU/DRP-AIの処理時間
- 推論処理の合計時間

実行方法は以下です.
```
$ profile_reader.py profile_table.txt
```