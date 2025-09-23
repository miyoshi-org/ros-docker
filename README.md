## 🚀 **DockerでROS/NVIDIA（最新版対応）環境構築マニュアル**

このリポジトリは、**NVIDIA GPU を活用しつつ Docker 上で ROS 環境を構築**する手順をまとめたものです。
**Ubuntu 20.04/22.04**、**Docker Engine 20.10+** に対応しています。

## ✅ **0. NVIDIA Container Toolkit のインストール**

GPU を使用する場合は `nvidia-docker` が必要です。
**CPU のみで動かす場合はスキップ可。**

```bash
# 公式手順: https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html

# 1. パッケージリポジトリを追加
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) 
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list \
    | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

# 2. パッケージ更新
sudo apt-get update

# 3. nvidia-docker2 をインストール
sudo apt-get install -y nvidia-docker2

# 4. Docker デーモンを再起動
sudo systemctl restart docker
```

**動作確認（コンテナ内）例：**

```bash
docker run --rm --gpus all nvidia/cuda:12.2.0-base-ubuntu22.04 nvidia-smi
```

## ✅ **1. ユーザグループへの追加**

```bash
sudo usermod -aG dialout $USER    # シリアル通信が必要なら
sudo usermod -aG docker $USER     # sudo なしで Docker を実行するため
```

> ⚠️ グループ変更後は **ログアウト → 再ログイン** または PC を再起動して反映！

## ✅ **2. GUIアプリケーションの表示許可（必要に応じて）**

X11 を使ってホスト側に GUI を表示する場合：

```bash
xhost +local:root   # 一時的に root の接続を許可
```

作業後のセキュリティ対策：

```bash
xhost -local:root   # 許可を元に戻す
```

> 💡 **Wayland セッションでは注意！**
> X11 セッションに切り替えるか `x11docker` を使用してください。

## ✅ **3. リポジトリのクローン**

```bash
git clone https://github.com/miyoshi-nii/docker-ros.git
cd docker-ros/ros-nvidia
```

## ✅ **4. Dockerイメージのビルド**

```bash
docker compose build
```

* グループ設定が反映されていない場合は `sudo docker compose build` を使ってください。
* `--no-cache` オプションを付けるとキャッシュを使わずにクリーンビルドできます。

## ✅ **5. Dockerコンテナの起動（バックグラウンド）**

```bash
docker compose up -d
```

* `-d` は「detached モード」＝ バックグラウンド起動
* ログを確認したい場合は `docker compose logs -f` を使用

## ✅ **6. コンテナ内に入る**

```bash
docker compose exec miyoshilab bash
```

* `miyoshilab` は `docker-compose.yml` で指定したサービス名です。
* コンテナが複数ある場合は `docker compose ps` で確認。

## ✅ **7. コンテナの停止と削除**

```bash
docker compose down
```

* `down` はコンテナ停止＋ネットワーク・ボリュームの削除。
* コンテナは削除されますが、ビルド済みイメージは残ります。

## 🔍 **💡 便利な Docker Compose オプション**

| オプション        | 内容            |
| ------------ | ------------- |
| `-d`         | バックグラウンド実行    |
| `--build`    | 起動前に必ずビルドを行う  |
| `--no-cache` | キャッシュを使わずにビルド |
| `logs -f`    | リアルタイムでログを見る  |
| `ps`         | 起動中のコンテナ一覧    |
| `down -v`    | ボリュームも削除      |

## 📌 **公式ドキュメント**

* [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)
* [Docker Compose](https://docs.docker.com/compose/)