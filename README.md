
# AI Blank Terminal Prototype

Raspberry Piで動く完全オフラインの「白紙AI端末」プロトタイプ。  
最初は何も入っていない状態から、音声/テキストプロンプトでAIがアプリをゼロから生成・実行します。  
Ollamaを使ってローカルで完結する、OS/アプリ不要のAI-firstデバイス実験です。

## 準備するもの（事前調達）
- Raspberry Pi：Pi 5（8GB RAM以上推奨、Pi 4でも動くが遅め）。Pi Zeroは重すぎて厳しい。
- MicroSDカード（32GB以上、Class 10以上）。
- 電源、HDMIケーブル、キーボード/マウス（初回セットアップ時のみ）。
- ネット接続（Wi-Fiまたは有線）。

## ステップ1: Raspberry Piのセットアップ（30分〜1時間）
1. Raspberry Pi Imager をPCにダウンロード（https://www.raspberrypi.com/software/）。
2. Imager起動 → OS選択 → Raspberry Pi OS (64-bit)（FullまたはLite、初心者はFull推奨）。
3. SDカード挿入 → WRITEでインストール。
4. SDカードをPiに挿して電源オン → 初回セットアップ（言語: Japanese、Wi-Fi設定、パスワード変更）。
5. デスクトップが出たら、ターミナルを開く。

## ステップ2: Ollamaインストール & モデルダウンロード（30分〜1時間）
ターミナルで順番に実行：
```bash
# システムアップデート（念のため）
sudo apt update && sudo apt upgrade -y

# Ollamaインストール
curl -fsSL https://ollama.com/install.sh | sh

# Ollamaサービス起動（バックグラウンド）
ollama serve &

# モデルダウンロード（gemma2:2b推奨、コード生成が強い）
ollama pull gemma2:2b

•  ダウンロードは1〜2GBかかるので時間が必要。
•  確認: ollama run gemma2:2b でチャットテスト（“hello”と入力して応答確認）。Ctrl+Cで終了。
ステップ3: Python環境 & ライブラリインストール（10分）

sudo apt install python3-pip python3-tk python3-venv -y
pip3 install ollama speechrecognition pyaudio pyttsx3

•  pyaudioエラー時は: sudo apt install portaudio19-dev -y してから再インストール。
ステップ4: プロトタイプコード作成 & 実行（10〜20分）
1.  フォルダ作成：
mkdir ai_blank_terminal
cd ai_blank_terminal

2.  コードファイル作成
nano ai_blank_terminal.py

→ ここにメインコードを貼り付け（Tkinter GUI + Ollama連携のスクリプト）。 → Ctrl+O → Enter保存 → Ctrl+X終了。 3. 実行：
python3 ai_blank_terminal.py

•  GUIが起動したらプロンプト入力でテスト。
•  マイク/スピーカー接続で音声テストも可能。
改善・注意ポイント（2026年現在）
1.  おすすめモデル変更（gemma2:2bより高性能なもの）:
	•  ollama pull llama3.2:3b または qwen2.5:3b（コード生成・指示追従が強い）。
2.  Ollamaサービス自動起動:
sudo systemctl enable ollama
sudo systemctl start ollama

3.  メモリ不足対策（swap追
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

4.  熱対策: ファン付きケース（Argon NEOなど）推奨。長時間使用で80〜90℃超えると性能低下。
5.  音声デバイス確認:
arecord -l   # マイク
aplay -l     # スピーカー

現在の技術アプローチ（進化させる場合）
•  モデル: qwen2.5:3b-instruct または llama3.2:3b がバランス良い。
•  Tool Calling活用: execute_python_code、install_pip_package などのツールを与えてアプリ生成を自動化。
•  安全対策: exec/eval制限、コードをファイル出力して確認。
•  フェーズ: Phase1 GUI表示 → Phase2 ReActループ → Phase3 音声+メモリ保持。
まずは軽めのアプリ生成（メモ、ToDo、カレンダーなど）からテストを。
