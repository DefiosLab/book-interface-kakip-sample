# DRP-AI TVM のインストール

## 準備

以下のファイルを作業ディレクトリに用意してください。

- [DRP-AI Translator i8](https://www.renesas.com/software-tool/drp-ai-translator-i8) v1.10以降
- [RZ/V2H AI Software Development Kit](https://www.renesas.com/software-tool/rzv2h-ai-software-development-kit) v5.20以降

※ダウンロードにはRenesasのアカウントが必要です

## Docker imageの作成
Dockerfileをダウンロードします。
```
$ wget https://raw.githubusercontent.com/renesas-rz/rzv_drp-ai_tvm/main/DockerfileV2H -O DockerfileV2H
```

Docker imageをビルドします。
```
$ ls
DockerfileV2H  r20ut5460ej0110-drp-ai-translator-i8.zip  RTK0EF0180F05200SJ.zip
$ unzip r20ut5460ej0110-drp-ai-translator-i8.zip
$ unzip RTK0EF0180F05200SJ.zip
$ mv `find ./ -name "*toolchain*sh"` .
$ docker build -t drp-ai_tvm_v2h_image_${USER} -f Dockerfile* --build-arg PRODUCT=V2H .
```

## コンテナの起動
以下のコマンドにより、ホストの`book-interface-kakip-sample`がDockerコンテナの`/drp-ai_tvm/book-interface-kakip-sample`にマウントされた状態で起動することができます。
Dockerコンテナとホストとの間でのファイルのやり取りに使用してください。
```
$ cd book-interface-kakip-sample
$ docker run --rm -it --name drp-ai_tvm_v2h_container_${USER} -v $(pwd):/drp-ai_tvm/book-interface-kakip-sample drp-ai_tvm_v2h_image_${USER}
```

# プロファイルの取得方法
## プロファイラの実行
DRP-AI TVMのプロファイルング機能はCPU/DRP-AIに割り当てられた各サブグラフの処理時間を計測できる機能です。

プロファイリング機能を使用するには、アプリケーションのコード内の推論を実行する関数`Run()`の代わりに`ProfileRun()`を呼び出します。

それぞれのアプリのコンパイル方法は、[app_resnet50_cam](./app_resnet50_cam/README.md)を参照してください。
```
ret = timespec_get(&start_time, TIME_UTC);
if (0 == ret)
{
    fprintf(stderr, "[ERROR] Failed to get Inference Start Time\n");
    goto err;
}

# runtime.Run();
runtime.ProfileRun("profile_table.txt", "profile.csv");

/*Gets AI Inference End Time*/
ret = timespec_get(&inf_end_time, TIME_UTC);
if ( 0 == ret)
{
    fprintf(stderr, "[ERROR] Failed to Get Inference End Time\n");
    goto err;
}

```

この変更を行ったうえでアプリを実行すると、実行ファイルと同じディレクトリ内に以下のファイルが生成されます。
- `profile_table.txt`
  - CPU/DRP-AIに割り当てられた各サブグラフの処理時間が含まれています
- `profile.csv`
  - `profile_table.txt`と同じ内容がCSV形式で含まれています


## プロファイリングデータの集計
`profile_reader.py`を用いてプロファイリングデータの集計を行うことができます.
スクリプトは以下のデータを表示します。
- CPU/DRP-AIのそれぞれで処理されるサブグラフの数
- CPU/DRP-AIのそれぞれに割り当てられた処理の割合
- CPU/DRP-AIの処理時間
- 推論処理の合計時間

実行方法は以下です。
```
$ profile_reader.py profile_table.txt
```