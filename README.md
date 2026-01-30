# 持ち込み資材を用いたオフラインインストール
## ユーザ作成
````Bash
sudo useradd -r -s /bin/false -U -m -d /usr/share/ollama ollama
sudo usermod -a -G ollama $(whoami)
````

## 資材展開
````Bash
cd 【資材の置き場所】
cat ollama-models.tar.part-* > ollama-models.tar
sha256sum -c ollama-models.tar.sha256
sudo tar xvf ollama-models.tar -C /
````

## ollama設定
````Bash
sudo touch /etc/systemd/system/ollama.service
sudo vim /etc/systemd/system/ollama.service
````
````Text
[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/usr/bin/ollama serve
User=ollama
Group=ollama
Restart=always
RestartSec=3
Environment="PATH=$PATH"

[Install]
WantedBy=multi-user.target
````

````Bash
sudo systemctl daemon-reload
sudo systemctl enable ollama
sudo systemctl start ollama
sudo systemctl status ollama
````


# 【参考】オンライン環境での資材作成

## OSインストール
wsl --install AlmaLinux-10 --name Alma2

## ollamaインストール
cd 【ollamaの置き場所】
sudo tar xf ollama-linux-amd64.tar.zst -C /usr
ollama serve

## ollama起動確認
別窓で
ollama -v

## ollama設定
sudo useradd -r -s /bin/false -U -m -d /usr/share/ollama ollama
sudo usermod -a -G ollama $(whoami)

sudo touch /etc/systemd/system/ollama.service
sudo vim /etc/systemd/system/ollama.service
---------
[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/usr/bin/ollama serve
User=ollama
Group=ollama
Restart=always
RestartSec=3
Environment="PATH=$PATH"

[Install]
WantedBy=multi-user.target
---------

sudo systemctl daemon-reload
sudo systemctl enable ollama

sudo systemctl start ollama
sudo systemctl status ollama


## モデルのダウンロード
ollama pull gpt-oss:20b
ollama pull qwen2.5:7b
ollama pull phi3:3.8b

## モデルの起動確認
ollama run phi3:3.8b
ollama stop phi3:3.8b
ollama run qwen2.5:7b
ollama stop qwen2.5:7b

## 資材のtar化
cd /mnt/e
sudo tar cvf ollama-models.tar /usr/share/ollama/.ollama /usr/bin/ollama /usr/lib/ollama

## 資材の分割
split -b 20M -a 3 ollama-models.tar ollama-models.tar.part-

## githubにpush


