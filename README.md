# AI Blank Terminal Prototype

Raspberry Piで動く「白紙AI端末」プロトタイプ。  
最初は何も入ってない状態から、音声/テキストプロンプトでAIがアプリをゼロから生成・実行します。  
完全オフライン・ローカル（Ollama使用）で、OS/アプリ不要のAI-firstデバイス実験です。
OSそのものをAIにした端末(完全オフライン・ローカルでアプリをプロンプト生成するAI端末プロトタイプ。アプリ/OS不要のAI-firstデバイス実験)

Raspberry Piでプロトタイプ作成
準備するもの（事前調達）
•  Raspberry Pi：Pi 5（8GB RAM以上推奨、Pi 4でも動くけど遅め）。Pi Zeroは重すぎて厳しい。
•  MicroSDカード（32GB以上、Class 10以上）。
•  電源、HDMIケーブル、キーボード/マウス（最初だけ）。
•  ネット接続（Wi-Fi or 有線）。

ステップ1: Raspberry Piのセットアップ（30分〜1時間）
1.  Raspberry Pi Imager をPCにダウンロード（公式サイト: raspberrypi.com/software）。
2.  Imager起動 → OS選択 → Raspberry Pi OS (64-bit)（Full or Lite、初心者はFull推奨）。
3.  SDカード挿入 → WRITE押してインストール。
4.  SDカードをPiに挿して電源オン → 初回セットアップ（言語: Japanese、Wi-Fi設定、パスワード変更）。
5.  デスクトップ出たら、ターミナル（黒いアイコン）開く。

ステップ2: Ollamaインストール & モデルダウンロード（30分〜1時間、ネット次第）
ターミナルで順番にコマンド入力（コピペしてEnter）：
# システムアップデート（念のため）
sudo apt update && sudo apt upgrade -y

# Ollamaインストール（1行で終わる）
curl -fsSL https://ollama.com/install.sh | sh

# Ollamaサービス起動（自動でバックグラウンド）
ollama serve &

# モデルダウンロード（gemma2:2bがおすすめ、コード生成強い & Piで現実的に動く）
ollama pull gemma2:2b


•  初回ダウンロードは1〜2GBなので時間かかる（Wi-Fi速いと30分くらい）。
•  確認：ollama run gemma2:2b でチャットテスト（“hello”って打って応答来たらOK）。Ctrl+Cで抜ける。

ステップ3: Python環境 & 必要なライブラリインストール（10分）
sudo apt install python3-pip python3-tk python3-venv -y
pip3 install ollama speechrecognition pyaudio pyttsx3

•  pyaudioでエラー出たら：
sudo apt install portaudio19-dev -y してから再pip。

ステップ4: プロトタイプコード作成（10〜20分）
1.  ターミナルでフォルダ作る：
mkdir ai_blank_terminal
cd ai_blank_terminal

2.  テキストエディタでファイル作成
nano ai_blank_terminal.py

→ 前に出してるコードを全部コピペ（Ctrl+Shift+Vで貼り付け）。
→ Ctrl+O → Enter保存 → Ctrl+X抜ける。
3.  テスト実行：
python3 ai_blank_terminal.py

GUI出て、プロンプト入力して生成できたら成功。
•  マイク/スピーカーつなげて音声テストも。

2.  Raspberry PiにGitインストール & SSHキー設定：
sudo apt install git -y

# SSHキー生成（パスフレーズは空でOK）
ssh-keygen -t ed25519 -C "your_email@example.com"
# Enter連打でデフォルト保存

# 公開キー表示してコピー
cat ~/.ssh/id_ed25519.pub


## インストール
1. Ollamaインストール: curl -fsSL https://ollama.com/install.sh | (sh
2. モデルダウンロード: ollama pull gemma2:2b
3. ライブラリ: pip3 install ollama speechrecognition pyaudio pyttsx3
4. 実行: python3 ai_blank_terminal.py

## 注意
- ollama serve & を先に起動しておく
- Piの熱対策にファンおすすめ

Commit new file。
追加のTips（そのまま使える）
•  エラー出たら：ターミナルでollama serve & を先に起動しといて。
•  Piの熱：長時間動かすと熱いのでファンおすすめ。

改善・注意したいポイント（2026年現在)
1.  おすすめモデル：gemma2:2b はもう少し新しい選択肢がある
	•  gemma2:2b は確かに軽くて動く（Pi 5 8GBで5〜12 token/sくらい）
	•  でも2025年後半〜2026年にかけてより高性能で同等か軽いモデルが人気：
		•  llama3.2:3b または llama3.2:1b（Metaの最新小さいシリーズ）
		•  qwen2.5:3b または qwen2.5:1.5b / 0.5b（速度と品質のバランスが非常に良い）
		•  gemma3:1b または gemma3:4b（Googleの後継、品質向上）
		•  phi-4-mini 系（Microsoftの最新軽量モデル） → おすすめ変更案（どれか1つ選ぶなら）

ollama pull llama3.2:3b
# または品質重視なら
ollama pull qwen2.5:3b
# 超軽くしたいなら
ollama pull qwen2.5:1.5b


gemma2:2bは悪くないけど、上記の方が「コード生成」や「指示に従う力」が全体的に向上しています。
2.  ollama serve & の改善
	•  & でバックグラウンドはOKだけど、ログが見えなくなるのでデバッグしづらい

sudo systemctl enable ollama
sudo systemctl start ollama

→ これで再起動後も自動で立ち上がる（ほとんどの最新ガイドで推奨）
3.  swapの設定を少し入れると安定する（特に3Bクラスモデル） Pi 5 8GBでもモデル読み込み時や長文生成時にメモリ不足で落ちることがある

sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

→ 4GB程度のswapを追加しておくと安心（SSDならもっと大きくてもOK）
4.  熱対策は本当に入れた方がいい
	•  長時間動かすと80〜90℃超え → サーマルスロットリングで性能半減
	•  最低でもファン付きケース（Argon NEO、Ice Towerなど）推奨
	•  またはヒートシンク＋小型ファンでも全然違う
5.  音声ライブラリの注意（pyaudio / pyttsx3）
	•  Raspberry Piのマイク・スピーカー設定が意外とハマる
	•  テスト前に一度
arecord -l    # マイクデバイス確認
aplay -l      # スピーカーデバイス確認
をしてデバイスがあるか確認しておくと後で楽

上記のポイント（特にモデルをllama3.2:3bかqwen2.5:3bに変える＋サービス化＋swap＋ファン）を反映すれば、2026年現在でもかなりイケてるプロトタイプになる。


現在の技術で最も現実的なアプローチ（このコードを進化させる形）
1.  モデル選び（最重要）
	•  最推奨: qwen2.5:3b-instruct または qwen2.5-coder:7b（Ollamaでpull可能） → コード生成・指示追従・tool callingのバランスが2026年現在で抜群に良い
	•  次点: llama3.2:3b（tool callingが安定）
	•  gemma2:2bは軽いけど、複雑なアプリ生成では少し力不足になりがち
2.  Tool Calling / Function Callingをフル活用 Ollamaは2025年頃からtool callingがかなり安定してる（特にqwen系・llama3.2系）。 このAIに以下のツールを与えると劇的に賢くなる：
	•  execute_python_code(code: str) → 生成したコードを安全にeval/exec（制限付きサンドボックス推奨）
	•  install_pip_package(package: str) → 必要ライブラリをその場でpip install
	•  get_current_directory_listing() → ファイル確認
	•  read_file(path: str) / write_file(path: str, content: str)
	•  restart_app() → 生成後にGUI再起動 これで「電話アプリ作って」→ AIが「pjsua2が必要ですね、installします」→ インストール → コード生成 → 実行 という流れができる。
3.  安全ガードレール（超重要）
	•  exec/evalは危険なので、restrictedpythonやastで危険な関数（os.system, subprocessなど）をブロック
	•  生成コードを一旦ファイルに書き出して人間確認（プロトタイプ段階では）
	•  ルート権限は絶対与えない
4.  アーキテクチャ例（シンプル→本格）
	•  Phase 1（今すぐできる）：あなたのTkinter GUIに、プロンプト→ ollama.chat(tools=…) → 生成コードをtext widgetに表示 → 「実行する？」ボタン
	•  Phase 2：ReActループ実装（思考→ツール呼び→観察→思考…）
	•  Phase 3：音声入力＋永続メモリ（会話履歴をRAGみたいに保持）

現実的にはPi単体だと「電話アプリ」「本格地図ナビ」は遅延・品質で厳しい。まずは「メモアプリ」「ToDoリスト」「簡易ブラウザ」「カレンダー」など軽めのアプリ生成からデモを作って、徐々に難易度上げていくのが良い。
