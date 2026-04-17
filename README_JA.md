# Qwen3-TTS

<br>

⚠️ This is a Japanese-focused fork of QwenLM/Qwen3-TTS.
This repository is NOT an official Qwen repository.
⚠️ このリポジトリは QwenLM/Qwen3-TTS のフォークです。このリポジトリは Qwen の公式リポジトリではありません。

Thanks to the Qwen3-TTS developers and contributors. / Qwen3-TTS 開発者とコントリビューターの皆様に感謝します。

English README: [README.md](README.md)

Supplementary docs: [README_ja_changes.md](README_ja_changes.md), [README_additional.md](README_additional.md)


<p align="center">
    <img src="https://qianwen-res.oss-cn-beijing.aliyuncs.com/Qwen3-TTS-Repo/qwen3_tts_logo.png" width="400"/>
<p>

<p align="center">
&nbsp&nbsp🤗 <a href="https://huggingface.co/collections/Qwen/qwen3-tts">Hugging Face</a>&nbsp&nbsp | &nbsp&nbsp🤖 <a href="https://modelscope.cn/collections/Qwen/Qwen3-TTS">ModelScope</a>&nbsp&nbsp | &nbsp&nbsp📑 <a href="https://qwen.ai/blog?id=qwen3tts-0115">Blog</a>&nbsp&nbsp | &nbsp&nbsp📑 <a href="https://arxiv.org/abs/2601.15621">Paper</a>&nbsp&nbsp
<br>
🖥️ <a href="https://huggingface.co/spaces/Qwen/Qwen3-TTS">Hugging Face Demo</a>&nbsp&nbsp | &nbsp&nbsp 🖥️ <a href="https://modelscope.cn/studios/Qwen/Qwen3-TTS">ModelScope Demo</a>&nbsp&nbsp | &nbsp&nbsp💬 <a href="https://github.com/QwenLM/Qwen/blob/main/assets/wechat.png">WeChat (微信)</a>&nbsp&nbsp | &nbsp&nbsp🫨 <a href="https://discord.gg/CV4E9rpNSD">Discord</a>&nbsp&nbsp | &nbsp&nbsp📑 <a href="https://help.aliyun.com/zh/model-studio/qwen-tts-realtime">API</a>

</p>

Qwen が開発した強力な音声生成機能のシリーズ **Qwen3-TTS** を公開します。音声クローン、音声デザイン、超高品質で人間らしい音声生成、自然言語による音声制御を包括的にサポートし、開発者・ユーザーに対して最も幅広い音声生成機能を提供します。


## News
* 2026.1.22: 🎉🎉🎉 Qwen3-TTS-Tokenizer-12Hz に基づく [Qwen3-TTS](https://huggingface.co/collections/Qwen/qwen3-tts) シリーズ (0.6B/1.7B) をリリースしました。詳しくは [blog](https://qwen.ai/blog?id=qwen3tts-0115) をご覧ください。

## Contents <!-- omit in toc -->

- [Overview](#overview) — 概要
  - [Introduction](#introduction) — イントロダクション
  - [Model Architecture](#model-architecture) — モデルアーキテクチャ
  - [Released Models Description and Download](#released-models-description-and-download) — 公開モデルの説明とダウンロード
- [Quickstart](#quickstart) — クイックスタート
  - [Environment Setup](#environment-setup) — 環境セットアップ
  - [Python Package Usage](#python-package-usage) — Python パッケージの使い方
    - [Custom Voice Generation](#custom-voice-generate) — カスタムボイス生成
    - [Voice Design](#voice-design) — ボイスデザイン
    - [Voice Clone](#voice-clone) — ボイスクローン
    - [Voice Design then Clone](#voice-design-then-clone) — ボイスデザイン後にクローン
    - [Tokenizer Encode and Decode](#tokenizer-encode-and-decode) — トークナイザのエンコードとデコード
  - [Launch Local Web UI Demo](#launch-local-web-ui-demo) — ローカル Web UI デモの起動
  - [DashScope API Usage](#dashscope-api-usage) — DashScope API の利用
- [vLLM Usage](#vllm-usage) — vLLM の利用
- [Fine Tuning](#fine-tuning) — ファインチューニング
- [Evaluation](#evaluation) — 評価
- [Citation](#citation) — 引用

## Overview
### Introduction

<p align="center">
    <img src="https://qianwen-res.oss-cn-beijing.aliyuncs.com/Qwen3-TTS-Repo/qwen3_tts_introduction.png" width="90%"/>
<p>

Qwen3-TTS は 10 の主要言語（中国語、英語、日本語、韓国語、ドイツ語、フランス語、ロシア語、ポルトガル語、スペイン語、イタリア語）と複数の方言音声プロファイルをカバーし、グローバルな利用ニーズに対応します。さらに、文脈理解が強化されており、指示とテキストの意味に基づいて話し方・速度・感情表現を適応的に制御できます。また、ノイズの多い入力テキストに対する堅牢性も大幅に向上しています。主な特徴は次のとおりです。

* **強力な音声表現**: 自社開発の Qwen3-TTS-Tokenizer-12Hz により、音声信号の効率的な音響圧縮と高次元の意味モデリングを実現します。パラ言語情報や音響環境の特徴を完全に保持し、軽量な非 DiT アーキテクチャによる高速・高忠実度の音声復元を可能にします。
* **汎用エンドツーエンドアーキテクチャ**: 離散マルチコードブック LM アーキテクチャを採用し、完全情報のエンドツーエンド音声モデリングを実現します。従来の LM+DiT 方式の情報ボトルネックやカスケード誤差を回避し、汎用性、生成効率、性能の上限を大幅に向上させます。
* **超低遅延のストリーミング生成**: 革新的な Dual-Track ハイブリッドストリーミング生成アーキテクチャにより、1 つのモデルでストリーミング/非ストリーミングの両方に対応します。1 文字の入力直後に最初の音声パケットを出力でき、エンドツーエンドの合成遅延は最小 97ms と、リアルタイム対話の厳しい要件を満たします。
* **知的テキスト理解と音声制御**: 自然言語の指示に基づく音声生成をサポートし、音色・感情・韻律など多次元の音響属性を柔軟に制御します。テキスト意味理解を深く統合することで、トーン・リズム・感情表現を適応的に調整し、「想像したとおりの音が出る」出力を実現します。


### Model Architecture

<p align="center">
    <img src="https://qianwen-res.oss-cn-beijing.aliyuncs.com/Qwen3-TTS-Repo/overview.png" width="80%"/>
<p>

### Released Models Description and Download

以下は既に公開されている Qwen3-TTS モデルの説明とダウンロード情報です。技術レポートで言及されている他のモデルは、近い将来に公開予定です。用途に合ったモデルを選択してダウンロードしてください。

| Tokenizer Name                      | 説明 |
|---------------------------------|-------------|
| Qwen3-TTS-Tokenizer-12Hz        | 入力音声をコードにエンコードし、音声にデコードできる Qwen3-TTS-Tokenizer-12Hz モデル。 |


| Model | 特徴 | 対応言語 | ストリーミング | 指示制御 |
|---|---|---|---|---|
| Qwen3-TTS-12Hz-1.7B-VoiceDesign | ユーザーの説明に基づいて音声デザインを行う。 | Chinese, English, Japanese, Korean, German, French, Russian, Portuguese, Spanish, Italian | ✅ | ✅ |
| Qwen3-TTS-12Hz-1.7B-CustomVoice | ユーザー指示による音色スタイル制御。性別・年齢・言語・方言の組合せを網羅した 9 種類のプレミアム音色に対応。 | Chinese, English, Japanese, Korean, German, French, Russian, Portuguese, Spanish, Italian | ✅ | ✅ |
| Qwen3-TTS-12Hz-1.7B-Base | ユーザーの音声入力から 3 秒で音声クローン可能なベースモデル。ほかのモデルのファインチューニング (FT) に利用可能。 | Chinese, English, Japanese, Korean, German, French, Russian, Portuguese, Spanish, Italian | ✅ |  |
| Qwen3-TTS-12Hz-0.6B-CustomVoice | 性別・年齢・言語・方言の組合せを網羅した 9 種類のプレミアム音色に対応。 | Chinese, English, Japanese, Korean, German, French, Russian, Portuguese, Spanish, Italian | ✅ |  |
| Qwen3-TTS-12Hz-0.6B-Base | ユーザーの音声入力から 3 秒で音声クローン可能なベースモデル。ほかのモデルのファインチューニング (FT) に利用可能。 | Chinese, English, Japanese, Korean, German, French, Russian, Portuguese, Spanish, Italian | ✅ |  |

qwen-tts パッケージや vLLM でモデルを読み込む際は、モデル名に基づいて自動的に重みがダウンロードされます。実行環境の都合で実行時のダウンロードが難しい場合は、以下のコマンドで重みをローカルに手動ダウンロードできます。

```bash
# Download through ModelScope (recommended for users in Mainland China)
pip install -U modelscope
modelscope download --model Qwen/Qwen3-TTS-Tokenizer-12Hz  --local_dir ./Qwen3-TTS-Tokenizer-12Hz 
modelscope download --model Qwen/Qwen3-TTS-12Hz-1.7B-CustomVoice --local_dir ./Qwen3-TTS-12Hz-1.7B-CustomVoice
modelscope download --model Qwen/Qwen3-TTS-12Hz-1.7B-VoiceDesign --local_dir ./Qwen3-TTS-12Hz-1.7B-VoiceDesign
modelscope download --model Qwen/Qwen3-TTS-12Hz-1.7B-Base --local_dir ./Qwen3-TTS-12Hz-1.7B-Base
modelscope download --model Qwen/Qwen3-TTS-12Hz-0.6B-CustomVoice --local_dir ./Qwen3-TTS-12Hz-0.6B-CustomVoice
modelscope download --model Qwen/Qwen3-TTS-12Hz-0.6B-Base --local_dir ./Qwen3-TTS-12Hz-0.6B-Base

# Download through Hugging Face
pip install -U "huggingface_hub[cli]"
huggingface-cli download Qwen/Qwen3-TTS-Tokenizer-12Hz --local-dir ./Qwen3-TTS-Tokenizer-12Hz
huggingface-cli download Qwen/Qwen3-TTS-12Hz-1.7B-CustomVoice --local-dir ./Qwen3-TTS-12Hz-1.7B-CustomVoice
huggingface-cli download Qwen/Qwen3-TTS-12Hz-1.7B-VoiceDesign --local-dir ./Qwen3-TTS-12Hz-1.7B-VoiceDesign
huggingface-cli download Qwen/Qwen3-TTS-12Hz-1.7B-Base --local-dir ./Qwen3-TTS-12Hz-1.7B-Base
huggingface-cli download Qwen/Qwen3-TTS-12Hz-0.6B-CustomVoice --local-dir ./Qwen3-TTS-12Hz-0.6B-CustomVoice
huggingface-cli download Qwen/Qwen3-TTS-12Hz-0.6B-Base --local-dir ./Qwen3-TTS-12Hz-0.6B-Base
```


## Quickstart

### Environment Setup

Qwen3-TTS を素早く使う最も簡単な方法は、PyPI の `qwen-tts` Python パッケージをインストールすることです。必要なランタイム依存が導入され、公開済みの Qwen3-TTS モデルをロードできます。既存のパッケージとの依存衝突を避けるため、**新しい隔離環境** を推奨します。次のようにクリーンな Python 3.12 環境を作成できます。

```bash
conda create -n qwen3-tts python=3.12 -y
conda activate qwen3-tts
```

次を実行します。

```bash
pip install -U qwen-tts
```

ローカルでコードを開発・修正する場合は、ソースから editable でインストールしてください。

```bash
git clone https://github.com/QwenLM/Qwen3-TTS.git
cd Qwen3-TTS
pip install -e .
```

また、GPU メモリ使用量を減らすために FlashAttention 2 を使うことを推奨します。

```bash
pip install -U flash-attn --no-build-isolation
```

RAM が 96GB 未満で CPU コア数が多い環境では、次を実行します。

```bash
MAX_JOBS=4 pip install -U flash-attn --no-build-isolation
```

FlashAttention 2 と互換性のあるハードウェアが必要です。詳細は [FlashAttention リポジトリ](https://github.com/Dao-AILab/flash-attention) の公式ドキュメントをご確認ください。FlashAttention 2 は `torch.float16` または `torch.bfloat16` でモデルをロードした場合にのみ利用できます。


### Python Package Usage

インストール後、`Qwen3TTSModel` を import してカスタムボイス TTS、ボイスデザイン、ボイスクローンを実行できます。モデル重みは、Hugging Face のモデル ID（推奨）またはダウンロードしたローカルディレクトリを指定できます。以下の `generate_*` 関数では、表示しているパラメータや明示的にドキュメント化されているもの以外にも、Hugging Face Transformers の `model.generate` がサポートする生成 kwargs（例: `max_new_tokens`, `top_p` など）を渡せます。

#### Custom Voice Generate

カスタムボイスモデル（`Qwen3-TTS-12Hz-1.7B/0.6B-CustomVoice`）では、`generate_custom_voice` を呼び出し、文字列またはバッチのリスト、`language`、`speaker`、必要に応じて `instruct` を渡します。`model.get_supported_speakers()` と `model.get_supported_languages()` で、現在のモデルが対応しているスピーカーと言語の一覧を取得できます。

```python
import torch
import soundfile as sf
from qwen_tts import Qwen3TTSModel

model = Qwen3TTSModel.from_pretrained(
    "Qwen/Qwen3-TTS-12Hz-1.7B-CustomVoice",
    device_map="cuda:0",
    dtype=torch.bfloat16,
    attn_implementation="flash_attention_2",
)

# single inference
wavs, sr = model.generate_custom_voice(
    text="其实我真的有发现，我是一个特别善于观察别人情绪的人。",
    language="Chinese", # Pass `Auto` (or omit) for auto language adaptive; if the target language is known, set it explicitly.
    speaker="Vivian",
    instruct="用特别愤怒的语气说", # Omit if not needed.
)
sf.write("output_custom_voice.wav", wavs[0], sr)

# batch inference
wavs, sr = model.generate_custom_voice(
    text=[
        "其实我真的有发现，我是一个特别善于观察别人情绪的人。", 
        "She said she would be here by noon."
    ],
    language=["Chinese", "English"],
    speaker=["Vivian", "Ryan"],
    instruct=["", "Very happy."]
)
sf.write("output_custom_voice_1.wav", wavs[0], sr)
sf.write("output_custom_voice_2.wav", wavs[1], sr)
```

`Qwen3-TTS-12Hz-1.7B/0.6B-CustomVoice` モデルに対応するスピーカー一覧と説明は以下のとおりです。最良の品質を得るため、各スピーカーのネイティブ言語の使用を推奨します。もちろん、各スピーカーはモデルが対応する任意の言語を話せます。

| Speaker | 音声の説明  |  ネイティブ言語 |
| --- | --- | --- |
| Vivian | Bright, slightly edgy young female voice. | Chinese |
| Serena | Warm, gentle young female voice. | Chinese |
| Uncle_Fu | Seasoned male voice with a low, mellow timbre. | Chinese |
| Dylan | Youthful Beijing male voice with a clear, natural timbre. | Chinese (Beijing Dialect) |
| Eric | Lively Chengdu male voice with a slightly husky brightness. | Chinese (Sichuan Dialect) |
| Ryan | Dynamic male voice with strong rhythmic drive. | English |
| Aiden | Sunny American male voice with a clear midrange. | English |
| Ono_Anna | Playful Japanese female voice with a light, nimble timbre. | Japanese |
| Sohee | Warm Korean female voice with rich emotion. | Korean |

#### Voice Design

ボイスデザインモデル（`Qwen3-TTS-12Hz-1.7B-VoiceDesign`）では、`generate_voice_design` を使って対象テキストと自然言語の `instruct` 説明を指定できます。

```python
import torch
import soundfile as sf
from qwen_tts import Qwen3TTSModel

model = Qwen3TTSModel.from_pretrained(
    "Qwen/Qwen3-TTS-12Hz-1.7B-VoiceDesign",
    device_map="cuda:0",
    dtype=torch.bfloat16,
    attn_implementation="flash_attention_2",
)

# single inference
wavs, sr = model.generate_voice_design(
    text="哥哥，你回来啦，人家等了你好久好久了，要抱抱！",
    language="Chinese",
    instruct="体现撒娇稚嫩的萝莉女声，音调偏高且起伏明显，营造出黏人、做作又刻意卖萌的听觉效果。",
)
sf.write("output_voice_design.wav", wavs[0], sr)

# batch inference
wavs, sr = model.generate_voice_design(
    text=[
      "哥哥，你回来啦，人家等了你好久好久了，要抱抱！",
      "It's in the top drawer... wait, it's empty? No way, that's impossible! I'm sure I put it there!"
    ],
    language=["Chinese", "English"],
    instruct=[
      "体现撒娇稚嫩的萝莉女声，音调偏高且起伏明显，营造出黏人、做作又刻意卖萌的听觉效果。",
      "Speak in an incredulous tone, but with a hint of panic beginning to creep into your voice."
    ]
)
sf.write("output_voice_design_1.wav", wavs[0], sr)
sf.write("output_voice_design_2.wav", wavs[1], sr)
```

#### Voice Clone

ボイスクローンモデル（`Qwen3-TTS-12Hz-1.7B/0.6B-Base`）では、音声をクローンして新しい内容を合成するために、参照音声クリップ（`ref_audio`）とその文字起こし（`ref_text`）を指定します。`ref_audio` はローカルファイルパス、URL、base64 文字列、または `(numpy_array, sample_rate)` のタプルを指定できます。`x_vector_only_mode=True` を設定すると話者埋め込みのみを使用し、`ref_text` は不要になりますが、クローン品質が低下する可能性があります。

```python
import torch
import soundfile as sf
from qwen_tts import Qwen3TTSModel

model = Qwen3TTSModel.from_pretrained(
    "Qwen/Qwen3-TTS-12Hz-1.7B-Base",
    device_map="cuda:0",
    dtype=torch.bfloat16,
    attn_implementation="flash_attention_2",
)

ref_audio = "https://qianwen-res.oss-cn-beijing.aliyuncs.com/Qwen3-TTS-Repo/clone.wav"
ref_text  = "Okay. Yeah. I resent you. I love you. I respect you. But you know what? You blew it! And thanks to you."

wavs, sr = model.generate_voice_clone(
    text="I am solving the equation: x = [-b ± √(b²-4ac)] / 2a? Nobody can — it's a disaster (◍•͈⌔•͈◍), very sad!",
    language="English",
    ref_audio=ref_audio,
    ref_text=ref_text,
)
sf.write("output_voice_clone.wav", wavs[0], sr)
```

同じ参照プロンプトを複数回の生成で使い回したい場合（プロンプト特徴の再計算を避けるため）は、`create_voice_clone_prompt` で一度作成し、`voice_clone_prompt` で渡してください。

```python
prompt_items = model.create_voice_clone_prompt(
    ref_audio=ref_audio,
    ref_text=ref_text,
    x_vector_only_mode=False,
)
wavs, sr = model.generate_voice_clone(
    text=["Sentence A.", "Sentence B."],
    language=["English", "English"],
    voice_clone_prompt=prompt_items,
)
sf.write("output_voice_clone_1.wav", wavs[0], sr)
sf.write("output_voice_clone_2.wav", wavs[1], sr)
```

再利用可能なボイスクローンプロンプト、バッチクローン、バッチ推論の例については、[example codes](https://github.com/QwenLM/Qwen3-TTS/blob/main/examples/test_model_12hz_base.py) を参照してください。これらの例と `generate_voice_clone` の説明を併せて、より高度な使い方を検討できます。

#### Voice Design then Clone

設計した声をクローン話者のように再利用したい場合は、(1) **VoiceDesign** モデルでターゲットのペルソナに合う短い参照クリップを生成し、(2) そのクリップを `create_voice_clone_prompt` に渡して再利用可能なプロンプトを作成し、(3) `voice_clone_prompt` を指定して `generate_voice_clone` を呼び出す、という流れが実用的です。複数のセリフにわたって同じキャラクターボイスを保ちたい場合に特に有効です。

```python
import torch
import soundfile as sf
from qwen_tts import Qwen3TTSModel

# create a reference audio in the target style using the VoiceDesign model
design_model = Qwen3TTSModel.from_pretrained(
    "Qwen/Qwen3-TTS-12Hz-1.7B-VoiceDesign",
    device_map="cuda:0",
    dtype=torch.bfloat16,
    attn_implementation="flash_attention_2",
)

ref_text = "H-hey! You dropped your... uh... calculus notebook? I mean, I think it's yours? Maybe?"
ref_instruct = "Male, 17 years old, tenor range, gaining confidence - deeper breath support now, though vowels still tighten when nervous"
ref_wavs, sr = design_model.generate_voice_design(
    text=ref_text,
    language="English",
    instruct=ref_instruct
)
sf.write("voice_design_reference.wav", ref_wavs[0], sr)

# build a reusable clone prompt from the voice design reference
clone_model = Qwen3TTSModel.from_pretrained(
    "Qwen/Qwen3-TTS-12Hz-1.7B-Base",
    device_map="cuda:0",
    dtype=torch.bfloat16,
    attn_implementation="flash_attention_2",
)

voice_clone_prompt = clone_model.create_voice_clone_prompt(
    ref_audio=(ref_wavs[0], sr),   # or "voice_design_reference.wav"
    ref_text=ref_text,
)

sentences = [
    "No problem! I actually... kinda finished those already? If you want to compare answers or something...",
    "What? No! I mean yes but not like... I just think you're... your titration technique is really precise!",
]

# reuse it for multiple single calls
wavs, sr = clone_model.generate_voice_clone(
    text=sentences[0],
    language="English",
    voice_clone_prompt=voice_clone_prompt,
)
sf.write("clone_single_1.wav", wavs[0], sr)

wavs, sr = clone_model.generate_voice_clone(
    text=sentences[1],
    language="English",
    voice_clone_prompt=voice_clone_prompt,
)
sf.write("clone_single_2.wav", wavs[0], sr)

# or batch generate in one call
wavs, sr = clone_model.generate_voice_clone(
    text=sentences,
    language=["English", "English"],
    voice_clone_prompt=voice_clone_prompt,
)
for i, w in enumerate(wavs):
    sf.write(f"clone_batch_{i}.wav", w, sr)
```

#### Tokenizer Encode and Decode

転送や学習などの目的で音声のエンコード/デコードだけを行いたい場合、`Qwen3TTSTokenizer` はパス、URL、numpy の波形、dict/list 形式のペイロードでの encode/decode をサポートします。例:

```python
import soundfile as sf
from qwen_tts import Qwen3TTSTokenizer

tokenizer = Qwen3TTSTokenizer.from_pretrained(
    "Qwen/Qwen3-TTS-Tokenizer-12Hz",
    device_map="cuda:0",
)

enc = tokenizer.encode("https://qianwen-res.oss-cn-beijing.aliyuncs.com/Qwen3-TTS-Repo/tokenizer_demo_1.wav")
wavs, sr = tokenizer.decode(enc)
sf.write("decode_output.wav", wavs[0], sr)
```

異なる入力形式やバッチ利用を含むトークナイザの例については、[example codes](https://github.com/QwenLM/Qwen3-TTS/blob/main/examples/test_tokenizer_12hz.py) を参照してください。これらの例と `Qwen3TTSTokenizer` の説明を併せて、より高度な使い方を検討できます。

### Launch Local Web UI Demo

Qwen3-TTS の Web UI デモを起動するには、`qwen-tts` パッケージをインストールして `qwen-tts-demo` を実行するだけです。詳細な使い方は以下のコマンドで確認できます。

```bash
qwen-tts-demo --help
```

デモを起動するには、次のコマンドを使用します。

```bash
# CustomVoice model
qwen-tts-demo Qwen/Qwen3-TTS-12Hz-1.7B-CustomVoice --ip 0.0.0.0 --port 8000
# VoiceDesign model
qwen-tts-demo Qwen/Qwen3-TTS-12Hz-1.7B-VoiceDesign --ip 0.0.0.0 --port 8000
# Base model
qwen-tts-demo Qwen/Qwen3-TTS-12Hz-1.7B-Base --ip 0.0.0.0 --port 8000
```

その後、`http://<your-ip>:8000` を開くか、VS Code などのツールでポートフォワーディングしてアクセスします。

#### Base Model HTTPS Notes

サーバーをデプロイした後のブラウザのマイク権限問題を避けるため、Base モデルのデプロイでは **HTTPS** で gradio サービスを実行することが推奨/必須です（特にリモートアクセスや最新ブラウザ/ゲートウェイの背後で利用する場合）。`--ssl-certfile` と `--ssl-keyfile` を使って HTTPS を有効にします。まず秘密鍵と自己署名証明書（有効期限 365 日）を生成します。

```bash
openssl req -x509 -newkey rsa:2048 \
  -keyout key.pem -out cert.pem \
  -days 365 -nodes \
  -subj "/CN=localhost"
```

次に HTTPS でデモを起動します。

```bash
qwen-tts-demo Qwen/Qwen3-TTS-12Hz-1.7B-Base \
  --ip 0.0.0.0 --port 8000 \
  --ssl-certfile cert.pem \
  --ssl-keyfile key.pem \
  --no-ssl-verify
```

`https://<your-ip>:8000` を開いて動作を確認します。自己署名証明書なのでブラウザに警告が出ますが正常です。本番では正式な証明書を使用してください。

### DashScope API Usage

Qwen3-TTS をさらに活用するため、より高速かつ効率的な体験として DashScope API を試すことを推奨します。詳細な API 情報やドキュメントは以下をご参照ください。

| API 説明 | API ドキュメント（中国本土） | API ドキュメント（海外） |
|------------------|-----------------------------------|------------------------------------|
| カスタムボイスモデル向け Qwen3-TTS のリアルタイム API | [https://help.aliyun.com/zh/model-studio/qwen-tts-realtime](https://help.aliyun.com/zh/model-studio/qwen-tts-realtime) | [https://www.alibabacloud.com/help/en/model-studio/qwen-tts-realtime](https://www.alibabacloud.com/help/en/model-studio/qwen-tts-realtime) |
| ボイスクローンモデル向け Qwen3-TTS のリアルタイム API | [https://help.aliyun.com/zh/model-studio/qwen-tts-voice-cloning](https://help.aliyun.com/zh/model-studio/qwen-tts-voice-cloning) | [https://www.alibabacloud.com/help/en/model-studio/qwen-tts-voice-cloning](https://www.alibabacloud.com/help/en/model-studio/qwen-tts-voice-cloning) |
| ボイスデザインモデル向け Qwen3-TTS のリアルタイム API | [https://help.aliyun.com/zh/model-studio/qwen-tts-voice-design](https://help.aliyun.com/zh/model-studio/qwen-tts-voice-design) | [https://www.alibabacloud.com/help/en/model-studio/qwen-tts-voice-design](https://www.alibabacloud.com/help/en/model-studio/qwen-tts-voice-design) |


## vLLM Usage

vLLM は Qwen3-TTS を day-0 で公式サポートしています。Qwen3-TTS のデプロイと推論には vLLM-Omni をぜひご利用ください。インストール方法や詳細は [vLLM-Omni 公式ドキュメント](https://docs.vllm.ai/projects/vllm-omni/en/latest/getting_started/quickstart/#installation) を参照してください。現在はオフライン推論のみがサポートされており、オンラインサービングは今後対応予定です。vLLM-Omni は推論速度やストリーミング機能などの領域で Qwen3-TTS のサポートと最適化を継続します。

### Offline Inference
vLLM-Omni を使ってローカルで Qwen3-TTS を推論できます。音声出力を生成するサンプルは [vLLM-Omni リポジトリ](https://github.com/vllm-project/vllm-omni/tree/main/examples/offline_inference/qwen3_tts) にあります。
```bash
# git clone https://github.com/vllm-project/vllm-omni.git

# cd vllm-omni/examples/offline_inference/qwen3_tts

# Run a single sample with CustomVoice task
python end2end.py --query-type CustomVoice

# Batch sample (multiple prompts in one run) with CustomVoice task:
python end2end.py --query-type CustomVoice --use-batch-sample

# Run a single sample with VoiceDesign task
python end2end.py --query-type VoiceDesign

# Batch sample (multiple prompts in one run) with VoiceDesign task:
python end2end.py --query-type VoiceDesign --use-batch-sample

# Run a single sample with Base task in icl mode-tag
python end2end.py --query-type Base --mode-tag icl
```

## Fine Tuning

Qwen3-TTS のファインチューニングの詳細は [Qwen3-TTS-Finetuning](finetuning/) を参照してください。

## Evaluation

評価時はすべてのモデルを `dtype=torch.bfloat16` で推論し、`max_new_tokens=2048` を設定しました。ほかのサンプリングパラメータはチェックポイントの `generate_config.json` の既定値を使用しています。Seed-Test と InstructTTS-Eval のテストセットでは `language="auto"` を設定し、それ以外のテストセットでは対応する `language` を明示的に指定しました。詳細な結果は以下のとおりです。


<details>
<summary>音声生成ベンチマーク</summary>

*Seed-TTS テストセットにおけるゼロショット音声生成。性能は Word Error Rate (WER, ↓) で評価し、低いほど良い。*

<table>
  <thead>
    <tr>
      <th style="text-align: center;">データセット</th>
      <th style="text-align: left;">モデル</th>
      <th colspan="2" style="text-align: center;">性能</th>
    </tr>
    <tr style="border-bottom: 1px solid #ddd; border-top: 1px solid #ddd;">
      <td colspan="4" style="text-align: center;"><em>内容一致性</em></td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="14" style="text-align: center; vertical-align: middle;">SEED<br><em>test-zh</em> | <em>test-en</em></td>
      <td style="text-align: left;">Seed-TTS (Anastassiou et al., 2024)</td>
      <td style="text-align: center;">1.12</td>
      <td style="text-align: center;">2.25</td>
    </tr>
    <tr>
      <td style="text-align: left;">MaskGCT (Wang et al., 2024)</td>
      <td style="text-align: center;">2.27</td>
      <td style="text-align: center;">2.62</td>
    </tr>
    <tr>
      <td style="text-align: left;">E2 TTS (Eskimez et al., 2024)</td>
      <td style="text-align: center;">1.97</td>
      <td style="text-align: center;">2.19</td>
    </tr>
    <tr>
      <td style="text-align: left;">F5-TTS (Chen et al., 2024)</td>
      <td style="text-align: center;">1.56</td>
      <td style="text-align: center;">1.83</td>
    </tr>
    <tr>
      <td style="text-align: left;">Spark TTS (Wang et al., 2025)</td>
      <td style="text-align: center;">1.20</td>
      <td style="text-align: center;">1.98</td>
    </tr>
    <tr>
      <td style="text-align: left;">Llasa-8B (Ye et al., 2025b)</td>
      <td style="text-align: center;">1.59</td>
      <td style="text-align: center;">2.97</td>
    </tr>
    <tr>
      <td style="text-align: left;">KALL-E (Xia et al., 2024)</td>
      <td style="text-align: center;">0.96</td>
      <td style="text-align: center;">1.94</td>
    </tr>
    <tr>
      <td style="text-align: left;">FireRedTTS 2 (Xie et al., 2025)</td>
      <td style="text-align: center;">1.14</td>
      <td style="text-align: center;">1.95</td>
    </tr>
    <tr>
      <td style="text-align: left;">CosyVoice 3 (Du et al., 2025)</td>
      <td style="text-align: center;"><strong>0.71</strong></td>
      <td style="text-align: center;">1.45</td>
    </tr>
    <tr>
      <td style="text-align: left;">MiniMax-Speech (Zhang et al., 2025a)</td>
      <td style="text-align: center;">0.83</td>
      <td style="text-align: center;">1.65</td>
    </tr>
    <tr>
      <td style="text-align: left;">Qwen3-TTS-25Hz-0.6B-Base</td>
      <td style="text-align: center;">1.18</td>
      <td style="text-align: center;">1.64</td>
    </tr>
    <tr>
      <td style="text-align: left;">Qwen3-TTS-25Hz-1.7B-Base</td>
      <td style="text-align: center;">1.10</td>
      <td style="text-align: center;">1.49</td>
    </tr>
    <tr>
      <td style="text-align: left;">Qwen3-TTS-12Hz-0.6B-Base</td>
      <td style="text-align: center;">0.92</td>
      <td style="text-align: center;">1.32</td>
    </tr>
    <tr>
      <td style="text-align: left;">Qwen3-TTS-12Hz-1.7B-Base</td>
      <td style="text-align: center;">0.77</td>
      <td style="text-align: center;"><strong>1.24</strong></td>
    </tr>
  </tbody>
</table>

<br>

*TTS 多言語テストセットにおける多言語音声生成。性能は内容一致性の WER (↓) と話者類似度の Cosine Similarity (SIM, ↑) で評価。*

<table>
  <thead>
    <tr>
      <th rowspan="2" style="text-align: left; vertical-align: bottom;">言語</th>
      <th colspan="2" style="text-align: center;">Qwen3-TTS-25Hz</th>
      <th colspan="2" style="text-align: center;">Qwen3-TTS-12Hz</th>
      <th rowspan="2" style="text-align: center; vertical-align: bottom;">MiniMax</th>
      <th rowspan="2" style="text-align: center; vertical-align: bottom;">ElevenLabs</th>
    </tr>
    <tr>
      <th style="text-align: center;">0.6B-Base</th>
      <th style="text-align: center;">1.7B-Base</th>
      <th style="text-align: center;">0.6B-Base</th>
      <th style="text-align: center;">1.7B-Base</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td colspan="7" style="text-align: center; border-top: 1px solid #ddd; border-bottom: 1px solid #ddd;"><em>内容一致性</em></td>
    </tr>
    <tr>
      <td style="text-align: left;">Chinese</td>
      <td style="text-align: center;">1.108</td>
      <td style="text-align: center;"><strong>0.777</strong></td>
      <td style="text-align: center;">1.145</td>
      <td style="text-align: center;">0.928</td>
      <td style="text-align: center;">2.252</td>
      <td style="text-align: center;">16.026</td>
    </tr>
    <tr>
      <td style="text-align: left;">English</td>
      <td style="text-align: center;">1.048</td>
      <td style="text-align: center;">1.014</td>
      <td style="text-align: center;"><strong>0.836</strong></td>
      <td style="text-align: center;">0.934</td>
      <td style="text-align: center;">2.164</td>
      <td style="text-align: center;">2.339</td>
    </tr>
    <tr>
      <td style="text-align: left;">German</td>
      <td style="text-align: center;">1.501</td>
      <td style="text-align: center;">0.960</td>
      <td style="text-align: center;">1.089</td>
      <td style="text-align: center;">1.235</td>
      <td style="text-align: center;">1.906</td>
      <td style="text-align: center;"><strong>0.572</strong></td>
    </tr>
    <tr>
      <td style="text-align: left;">Italian</td>
      <td style="text-align: center;">1.169</td>
      <td style="text-align: center;">1.105</td>
      <td style="text-align: center;">1.534</td>
      <td style="text-align: center;"><strong>0.948</strong></td>
      <td style="text-align: center;">1.543</td>
      <td style="text-align: center;">1.743</td>
    </tr>
    <tr>
      <td style="text-align: left;">Portuguese</td>
      <td style="text-align: center;">2.046</td>
      <td style="text-align: center;">1.778</td>
      <td style="text-align: center;">2.254</td>
      <td style="text-align: center;">1.526</td>
      <td style="text-align: center;">1.877</td>
      <td style="text-align: center;"><strong>1.331</strong></td>
    </tr>
    <tr>
      <td style="text-align: left;">Spanish</td>
      <td style="text-align: center;">2.031</td>
      <td style="text-align: center;">1.491</td>
      <td style="text-align: center;">1.491</td>
      <td style="text-align: center;">1.126</td>
      <td style="text-align: center;"><strong>1.029</strong></td>
      <td style="text-align: center;">1.084</td>
    </tr>
    <tr>
      <td style="text-align: left;">Japanese</td>
      <td style="text-align: center;">4.189</td>
      <td style="text-align: center;">5.121</td>
      <td style="text-align: center;">6.404</td>
      <td style="text-align: center;">3.823</td>
      <td style="text-align: center;"><strong>3.519</strong></td>
      <td style="text-align: center;">10.646</td>
    </tr>
    <tr>
      <td style="text-align: left;">Korean</td>
      <td style="text-align: center;">2.852</td>
      <td style="text-align: center;">2.631</td>
      <td style="text-align: center;"><strong>1.741</strong></td>
      <td style="text-align: center;">1.755</td>
      <td style="text-align: center;">1.747</td>
      <td style="text-align: center;">1.865</td>
    </tr>
    <tr>
      <td style="text-align: left;">French</td>
      <td style="text-align: center;">2.852</td>
      <td style="text-align: center;"><strong>2.631</strong></td>
      <td style="text-align: center;">2.931</td>
      <td style="text-align: center;">2.858</td>
      <td style="text-align: center;">4.099</td>
      <td style="text-align: center;">5.216</td>
    </tr>
    <tr>
      <td style="text-align: left;">Russian</td>
      <td style="text-align: center;">5.957</td>
      <td style="text-align: center;">4.535</td>
      <td style="text-align: center;">4.458</td>
      <td style="text-align: center;"><strong>3.212</strong></td>
      <td style="text-align: center;">4.281</td>
      <td style="text-align: center;">3.878</td>
    </tr>
    <tr style="border-top: 1px solid #ddd;">
      <td colspan="7" style="text-align: center; border-bottom: 1px solid #ddd;"><em>話者類似度</em></td>
    </tr>
    <tr>
      <td style="text-align: left;">Chinese</td>
      <td style="text-align: center;">0.797</td>
      <td style="text-align: center;">0.796</td>
      <td style="text-align: center;"><strong>0.811</strong></td>
      <td style="text-align: center;">0.799</td>
      <td style="text-align: center;">0.780</td>
      <td style="text-align: center;">0.677</td>
    </tr>
    <tr>
      <td style="text-align: left;">English</td>
      <td style="text-align: center;">0.811</td>
      <td style="text-align: center;">0.815</td>
      <td style="text-align: center;"><strong>0.829</strong></td>
      <td style="text-align: center;">0.775</td>
      <td style="text-align: center;">0.756</td>
      <td style="text-align: center;">0.613</td>
    </tr>
    <tr>
      <td style="text-align: left;">German</td>
      <td style="text-align: center;">0.749</td>
      <td style="text-align: center;">0.737</td>
      <td style="text-align: center;">0.769</td>
      <td style="text-align: center;"><strong>0.775</strong></td>
      <td style="text-align: center;">0.733</td>
      <td style="text-align: center;">0.614</td>
    </tr>
    <tr>
      <td style="text-align: left;">Italian</td>
      <td style="text-align: center;">0.722</td>
      <td style="text-align: center;">0.718</td>
      <td style="text-align: center;">0.792</td>
      <td style="text-align: center;"><strong>0.817</strong></td>
      <td style="text-align: center;">0.699</td>
      <td style="text-align: center;">0.579</td>
    </tr>
    <tr>
      <td style="text-align: left;">Portuguese</td>
      <td style="text-align: center;">0.790</td>
      <td style="text-align: center;">0.783</td>
      <td style="text-align: center;">0.794</td>
      <td style="text-align: center;"><strong>0.817</strong></td>
      <td style="text-align: center;">0.805</td>
      <td style="text-align: center;">0.711</td>
    </tr>
    <tr>
      <td style="text-align: left;">Spanish</td>
      <td style="text-align: center;">0.732</td>
      <td style="text-align: center;">0.731</td>
      <td style="text-align: center;">0.812</td>
      <td style="text-align: center;"><strong>0.814</strong></td>
      <td style="text-align: center;">0.762</td>
      <td style="text-align: center;">0.615</td>
    </tr>
    <tr>
      <td style="text-align: left;">Japanese</td>
      <td style="text-align: center;"><strong>0.810</strong></td>
      <td style="text-align: center;">0.807</td>
      <td style="text-align: center;">0.798</td>
      <td style="text-align: center;">0.788</td>
      <td style="text-align: center;">0.776</td>
      <td style="text-align: center;">0.738</td>
    </tr>
    <tr>
      <td style="text-align: left;">Korean</td>
      <td style="text-align: center;"><strong>0.824</strong></td>
      <td style="text-align: center;">0.814</td>
      <td style="text-align: center;">0.812</td>
      <td style="text-align: center;">0.799</td>
      <td style="text-align: center;">0.779</td>
      <td style="text-align: center;">0.700</td>
    </tr>
    <tr>
      <td style="text-align: left;">French</td>
      <td style="text-align: center;">0.698</td>
      <td style="text-align: center;">0.703</td>
      <td style="text-align: center;">0.700</td>
      <td style="text-align: center;"><strong>0.714</strong></td>
      <td style="text-align: center;">0.628</td>
      <td style="text-align: center;">0.535</td>
    </tr>
    <tr>
      <td style="text-align: left;">Russian</td>
      <td style="text-align: center;">0.734</td>
      <td style="text-align: center;">0.744</td>
      <td style="text-align: center;">0.781</td>
      <td style="text-align: center;"><strong>0.792</strong></td>
      <td style="text-align: center;">0.761</td>
      <td style="text-align: center;">0.676</td>
    </tr>
  </tbody>
</table>

<br>

*Cross-Lingual ベンチマークにおけるクロスリンガル音声生成。性能は Mixed Error Rate（英語は WER、その他は CER、↓）で評価。*

<table>
  <thead>
    <tr>
      <th style="text-align: left;">タスク</th>
      <th style="text-align: center;">Qwen3-TTS-25Hz-1.7B-Base</th>
      <th style="text-align: center;">Qwen3-TTS-12Hz-1.7B-Base</th>
      <th style="text-align: center;">CosyVoice3</th>
      <th style="text-align: center;">CosyVoice2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align: left;">en-to-zh</td>
      <td style="text-align: center;">5.66</td>
      <td style="text-align: center;"><strong>4.77</strong></td>
      <td style="text-align: center;">5.09</td>
      <td style="text-align: center;">13.5</td>
    </tr>
    <tr>
      <td style="text-align: left;">ja-to-zh</td>
      <td style="text-align: center;">3.92</td>
      <td style="text-align: center;">3.43</td>
      <td style="text-align: center;"><strong>3.05</strong></td>
      <td style="text-align: center;">48.1</td>
    </tr>
    <tr>
      <td style="text-align: left;">ko-to-zh</td>
      <td style="text-align: center;">1.14</td>
      <td style="text-align: center;">1.08</td>
      <td style="text-align: center;"><strong>1.06</strong></td>
      <td style="text-align: center;">7.70</td>
    </tr>
    <tr style="border-top: 1px solid #ddd;">
      <td style="text-align: left;">zh-to-en</td>
      <td style="text-align: center;">2.91</td>
      <td style="text-align: center;"><strong>2.77</strong></td>
      <td style="text-align: center;">2.98</td>
      <td style="text-align: center;">6.47</td>
    </tr>
    <tr>
      <td style="text-align: left;">ja-to-en</td>
      <td style="text-align: center;">3.95</td>
      <td style="text-align: center;"><strong>3.04</strong></td>
      <td style="text-align: center;">4.20</td>
      <td style="text-align: center;">17.1</td>
    </tr>
    <tr>
      <td style="text-align: left;">ko-to-en</td>
      <td style="text-align: center;">3.48</td>
      <td style="text-align: center;"><strong>3.09</strong></td>
      <td style="text-align: center;">4.19</td>
      <td style="text-align: center;">11.2</td>
    </tr>
    <tr style="border-top: 1px solid #ddd;">
      <td style="text-align: left;">zh-to-ja</td>
      <td style="text-align: center;">9.29</td>
      <td style="text-align: center;">8.40</td>
      <td style="text-align: center;"><strong>7.08</strong></td>
      <td style="text-align: center;">13.1</td>
    </tr>
    <tr>
      <td style="text-align: left;">en-to-ja</td>
      <td style="text-align: center;">7.74</td>
      <td style="text-align: center;">7.21</td>
      <td style="text-align: center;"><strong>6.80</strong></td>
      <td style="text-align: center;">14.9</td>
    </tr>
    <tr>
      <td style="text-align: left;">ko-to-ja</td>
      <td style="text-align: center;">4.17</td>
      <td style="text-align: center;"><strong>3.67</strong></td>
      <td style="text-align: center;">3.93</td>
      <td style="text-align: center;">5.86</td>
    </tr>
    <tr style="border-top: 1px solid #ddd;">
      <td style="text-align: left;">zh-to-ko</td>
      <td style="text-align: center;">8.12</td>
      <td style="text-align: center;"><strong>4.82</strong></td>
      <td style="text-align: center;">14.4</td>
      <td style="text-align: center;">24.8</td>
    </tr>
    <tr>
      <td style="text-align: left;">en-to-ko</td>
      <td style="text-align: center;">6.83</td>
      <td style="text-align: center;"><strong>5.14</strong></td>
      <td style="text-align: center;">5.87</td>
      <td style="text-align: center;">21.9</td>
    </tr>
    <tr>
      <td style="text-align: left;">ja-to-ko</td>
      <td style="text-align: center;">6.86</td>
      <td style="text-align: center;"><strong>5.59</strong></td>
      <td style="text-align: center;">7.92</td>
      <td style="text-align: center;">21.5</td>
    </tr>
  </tbody>
</table>

<br>

*InstructTTSEval における可制御音声生成。性能は Attribute Perception and Synthesis accuracy (APS)、Description-Speech Consistency (DSD)、Response Precision (RP) で評価。*

<table>
  <thead>
    <tr>
      <th rowspan="2" style="text-align: left; vertical-align: bottom;">タイプ</th>
      <th rowspan="2" style="text-align: left; vertical-align: bottom;">モデル</th>
      <th colspan="3" style="text-align: center;">InstructTTSEval-ZH</th>
      <th colspan="3" style="text-align: center;">InstructTTSEval-EN</th>
    </tr>
    <tr>
      <th style="text-align: center;">APS (↑)</th>
      <th style="text-align: center;">DSD (↑)</th>
      <th style="text-align: center;">RP (↑)</th>
      <th style="text-align: center;">APS (↑)</th>
      <th style="text-align: center;">DSD (↑)</th>
      <th style="text-align: center;">RP (↑)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="5" style="text-align: left; vertical-align: middle;"><em>Target<br>Speaker</em></td>
      <td style="text-align: left;">Gemini-flash</td>
      <td style="text-align: center;">88.2</td>
      <td style="text-align: center;"><strong>90.9</strong></td>
      <td style="text-align: center;"><strong>77.3</strong></td>
      <td style="text-align: center;"><strong>92.3</strong></td>
      <td style="text-align: center;"><strong>93.8</strong></td>
      <td style="text-align: center;"><strong>80.1</strong></td>
    </tr>
    <tr>
      <td style="text-align: left;">Gemini-pro</td>
      <td style="text-align: center;"><strong>89.0</strong></td>
      <td style="text-align: center;">90.1</td>
      <td style="text-align: center;">75.5</td>
      <td style="text-align: center;">87.6</td>
      <td style="text-align: center;">86.0</td>
      <td style="text-align: center;">67.2</td>
    </tr>
    <tr>
      <td style="text-align: left;">Qwen3TTS-25Hz-1.7B-CustomVoice</td>
      <td style="text-align: center;">83.1</td>
      <td style="text-align: center;">75.0</td>
      <td style="text-align: center;">63.0</td>
      <td style="text-align: center;">79.0</td>
      <td style="text-align: center;">82.8</td>
      <td style="text-align: center;">69.3</td>
    </tr>
    <tr>
      <td style="text-align: left;">Qwen3TTS-12Hz-1.7B-CustomVoice</td>
      <td style="text-align: center;">83.0</td>
      <td style="text-align: center;">77.8</td>
      <td style="text-align: center;">61.2</td>
      <td style="text-align: center;">77.3</td>
      <td style="text-align: center;">77.1</td>
      <td style="text-align: center;">63.7</td>
    </tr>
    <tr>
      <td style="text-align: left;">GPT-4o-mini-tts</td>
      <td style="text-align: center;">54.9</td>
      <td style="text-align: center;">52.3</td>
      <td style="text-align: center;">46.0</td>
      <td style="text-align: center;">76.4</td>
      <td style="text-align: center;">74.3</td>
      <td style="text-align: center;">54.8</td>
    </tr>
    <tr style="border-top: 1px solid #ddd;">
      <td rowspan="9" style="text-align: left; vertical-align: middle;"><em>Voice<br>Design</em></td>
      <td style="text-align: left;">Qwen3TTS-12Hz-1.7B-VD</td>
      <td style="text-align: center;"><strong>85.2</strong></td>
      <td style="text-align: center;"><strong>81.1</strong></td>
      <td style="text-align: center;"><strong>65.1</strong></td>
      <td style="text-align: center;">82.9</td>
      <td style="text-align: center;"><strong>82.4</strong></td>
      <td style="text-align: center;"><strong>68.4</strong></td>
    </tr>
    <tr>
      <td style="text-align: left;">Mimo-Audio-7B-Instruct (Zhang et al., 2025b)</td>
      <td style="text-align: center;">75.7</td>
      <td style="text-align: center;">74.3</td>
      <td style="text-align: center;">61.5</td>
      <td style="text-align: center;">80.6</td>
      <td style="text-align: center;">77.6</td>
      <td style="text-align: center;">59.5</td>
    </tr>
    <tr>
      <td style="text-align: left;">VoiceSculptor (Hu et al., 2026)</td>
      <td style="text-align: center;">75.7</td>
      <td style="text-align: center;">64.7</td>
      <td style="text-align: center;">61.5</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">-</td>
    </tr>
    <tr>
      <td style="text-align: left;">Hume</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;"><strong>83.0</strong></td>
      <td style="text-align: center;">75.3</td>
      <td style="text-align: center;">54.3</td>
    </tr>
    <tr>
      <td style="text-align: left;">VoxInstruct (Zhou et al., 2024)</td>
      <td style="text-align: center;">47.5</td>
      <td style="text-align: center;">52.3</td>
      <td style="text-align: center;">42.6</td>
      <td style="text-align: center;">54.9</td>
      <td style="text-align: center;">57.0</td>
      <td style="text-align: center;">39.3</td>
    </tr>
    <tr>
      <td style="text-align: left;">Parler-tts-mini (Lyth & King, 2024)</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">63.4</td>
      <td style="text-align: center;">48.7</td>
      <td style="text-align: center;">28.6</td>
    </tr>
    <tr>
      <td style="text-align: left;">Parler-tts-large (Lyth & King, 2024)</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">60.0</td>
      <td style="text-align: center;">45.9</td>
      <td style="text-align: center;">31.2</td>
    </tr>
    <tr>
      <td style="text-align: left;">PromptTTS (Guo et al., 2023)</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">64.3</td>
      <td style="text-align: center;">47.2</td>
      <td style="text-align: center;">31.4</td>
    </tr>
    <tr>
      <td style="text-align: left;">PromptStyle (Liu et al., 2023)</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">57.4</td>
      <td style="text-align: center;">46.4</td>
      <td style="text-align: center;">30.9</td>
    </tr>
  </tbody>
</table>

<br>

*TTS 多言語テストセットにおけるターゲット話者の多言語音声生成。性能は Word Error Rate (WER, ↓) で評価。*

<table>
  <thead>
    <tr>
      <th rowspan="2" style="text-align: left; vertical-align: bottom;">言語</th>
      <th colspan="2" style="text-align: center;">Qwen3-TTS-25Hz</th>
      <th colspan="2" style="text-align: center;">Qwen3-TTS-12Hz</th>
      <th rowspan="2" style="text-align: center; vertical-align: bottom;">GPT-4o-Audio<br>Preview</th>
    </tr>
    <tr>
      <th style="text-align: center;">0.6B-CustomVoice</th>
      <th style="text-align: center;">1.7B-CustomVoice</th>
      <th style="text-align: center;">0.6B-CustomVoice</th>
      <th style="text-align: center;">1.7B-CustomVoice</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align: left;">Chinese</td>
      <td style="text-align: center;">0.874</td>
      <td style="text-align: center;"><strong>0.708</strong></td>
      <td style="text-align: center;">0.944</td>
      <td style="text-align: center;">0.903</td>
      <td style="text-align: center;">3.519</td>
    </tr>
    <tr>
      <td style="text-align: left;">English</td>
      <td style="text-align: center;">1.332</td>
      <td style="text-align: center;">0.936</td>
      <td style="text-align: center;">1.188</td>
      <td style="text-align: center;"><strong>0.899</strong></td>
      <td style="text-align: center;">2.197</td>
    </tr>
    <tr>
      <td style="text-align: left;">German</td>
      <td style="text-align: center;">0.990</td>
      <td style="text-align: center;"><strong>0.634</strong></td>
      <td style="text-align: center;">2.722</td>
      <td style="text-align: center;">1.057</td>
      <td style="text-align: center;">1.161</td>
    </tr>
    <tr>
      <td style="text-align: left;">Italian</td>
      <td style="text-align: center;">1.861</td>
      <td style="text-align: center;">1.271</td>
      <td style="text-align: center;">2.545</td>
      <td style="text-align: center;">1.362</td>
      <td style="text-align: center;"><strong>1.194</strong></td>
    </tr>
    <tr>
      <td style="text-align: left;">Portuguese</td>
      <td style="text-align: center;">1.728</td>
      <td style="text-align: center;">1.854</td>
      <td style="text-align: center;">3.219</td>
      <td style="text-align: center;">2.681</td>
      <td style="text-align: center;"><strong>1.504</strong></td>
    </tr>
    <tr>
      <td style="text-align: left;">Spanish</td>
      <td style="text-align: center;">1.309</td>
      <td style="text-align: center;">1.284</td>
      <td style="text-align: center;"><strong>1.154</strong></td>
      <td style="text-align: center;">1.330</td>
      <td style="text-align: center;">4.000</td>
    </tr>
    <tr>
      <td style="text-align: left;">Japanese</td>
      <td style="text-align: center;"><strong>3.875</strong></td>
      <td style="text-align: center;">4.518</td>
      <td style="text-align: center;">6.877</td>
      <td style="text-align: center;">4.924</td>
      <td style="text-align: center;">5.001</td>
    </tr>
    <tr>
      <td style="text-align: left;">Korean</td>
      <td style="text-align: center;">2.202</td>
      <td style="text-align: center;">2.274</td>
      <td style="text-align: center;">3.053</td>
      <td style="text-align: center;"><strong>1.741</strong></td>
      <td style="text-align: center;">2.763</td>
    </tr>
    <tr>
      <td style="text-align: left;">French</td>
      <td style="text-align: center;">3.865</td>
      <td style="text-align: center;"><strong>3.080</strong></td>
      <td style="text-align: center;">3.841</td>
      <td style="text-align: center;">3.781</td>
      <td style="text-align: center;">3.605</td>
    </tr>
    <tr>
      <td style="text-align: left;">Russian</td>
      <td style="text-align: center;">6.529</td>
      <td style="text-align: center;"><strong>4.444</strong></td>
      <td style="text-align: center;">5.809</td>
      <td style="text-align: center;">4.734</td>
      <td style="text-align: center;">5.250</td>
    </tr>
  </tbody>
</table>

<br>

*長文音声生成の結果。性能は Word Error Rate (WER, ↓) で評価。*

<table>
  <thead>
    <tr>
      <th style="text-align: center;">データセット</th>
      <th style="text-align: left;">モデル</th>
      <th colspan="2" style="text-align: center;">性能</th>
    </tr>
    <tr style="border-bottom: 1px solid #ddd; border-top: 1px solid #ddd;">
      <td colspan="4" style="text-align: center;"><em>内容一致性</em></td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="5" style="text-align: center; vertical-align: middle;"><em>long-zh</em> | <em>long-en</em></td>
      <td style="text-align: left;">Higgs-Audio-v2 (chunk) (Boson AI, 2025)</td>
      <td style="text-align: center;">5.505</td>
      <td style="text-align: center;">6.917</td>
    </tr>
    <tr>
      <td style="text-align: left;">VibeVoice (Peng et al., 2025)</td>
      <td style="text-align: center;">22.619</td>
      <td style="text-align: center;">1.780</td>
    </tr>
    <tr>
      <td style="text-align: left;">VoxCPM (Zhou et al., 2025)</td>
      <td style="text-align: center;">4.835</td>
      <td style="text-align: center;">7.474</td>
    </tr>
    <tr>
      <td style="text-align: left;">Qwen3-TTS-25Hz-1.7B-CustomVoice</td>
      <td style="text-align: center;"><strong>1.517</strong></td>
      <td style="text-align: center;"><strong>1.225</strong></td>
    </tr>
    <tr>
      <td style="text-align: left;">Qwen3-TTS-12Hz-1.7B-CustomVoice</td>
      <td style="text-align: center;">2.356</td>
      <td style="text-align: center;">2.812</td>
    </tr>
  </tbody>
</table>
</details>


<details>
<summary>音声トークナイザベンチマーク</summary>

*ASR タスクにおける、教師ありセマンティック音声トークナイザの比較。*

<table>
  <thead>
    <tr>
      <th style="text-align: left;">Model</th>
      <th style="text-align: center;">Codebook Size</th>
      <th style="text-align: center;">FPS</th>
      <th style="text-align: center;">C.V. EN</th>
      <th style="text-align: center;">C.V. CN</th>
      <th style="text-align: center;">Fluers EN</th>
      <th style="text-align: center;">Fluers CN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align: left;">S3 Tokenizer(VQ) (Du et al., 2024a)</td>
      <td style="text-align: center;">4096</td>
      <td style="text-align: center;">50</td>
      <td style="text-align: center;">12.06</td>
      <td style="text-align: center;">15.38</td>
      <td style="text-align: center;">-</td>
      <td style="text-align: center;">-</td>
    </tr>
    <tr>
      <td style="text-align: left;">S3 Tokenizer(VQ) (Du et al., 2024a)</td>
      <td style="text-align: center;">4096</td>
      <td style="text-align: center;">25</td>
      <td style="text-align: center;">11.56</td>
      <td style="text-align: center;">18.26</td>
      <td style="text-align: center;">7.65</td>
      <td style="text-align: center;">5.03</td>
    </tr>
    <tr>
      <td style="text-align: left;">S3 Tokenizer(FSQ) (Du et al., 2024a)</td>
      <td style="text-align: center;">6561</td>
      <td style="text-align: center;">25</td>
      <td style="text-align: center;">10.67</td>
      <td style="text-align: center;"><strong>7.29</strong></td>
      <td style="text-align: center;">6.58</td>
      <td style="text-align: center;">4.43</td>
    </tr>
    <tr>
      <td style="text-align: left;">Qwen-TTS-Tokenizer-25Hz (Stage 1)</td>
      <td style="text-align: center;">32768</td>
      <td style="text-align: center;">25</td>
      <td style="text-align: center;"><strong>7.51</strong></td>
      <td style="text-align: center;">10.73</td>
      <td style="text-align: center;"><strong>3.07</strong></td>
      <td style="text-align: center;"><strong>4.23</strong></td>
    </tr>
    <tr>
      <td style="text-align: left;">Qwen-TTS-Tokenizer-25Hz (Stage 2)</td>
      <td style="text-align: center;">32768</td>
      <td style="text-align: center;">25</td>
      <td style="text-align: center;">10.40</td>
      <td style="text-align: center;">14.99</td>
      <td style="text-align: center;">4.14</td>
      <td style="text-align: center;">4.67</td>
    </tr>
  </tbody>
</table>

<br>

*セマンティック関連の音声トークナイザの比較。*

<table>
  <thead>
    <tr>
      <th style="text-align: left;">Model</th>
      <th style="text-align: center;">NQ</th>
      <th style="text-align: center;">Codebook Size</th>
      <th style="text-align: center;">FPS</th>
      <th style="text-align: center;">PESQ_WB</th>
      <th style="text-align: center;">PESQ_NB</th>
      <th style="text-align: center;">STOI</th>
      <th style="text-align: center;">UTMOS</th>
      <th style="text-align: center;">SIM</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align: left;">SpeechTokenizer (Zhang et al., 2023a)</td>
      <td style="text-align: center;">8</td>
      <td style="text-align: center;">1024</td>
      <td style="text-align: center;">50</td>
      <td style="text-align: center;">2.60</td>
      <td style="text-align: center;">3.05</td>
      <td style="text-align: center;">0.92</td>
      <td style="text-align: center;">3.90</td>
      <td style="text-align: center;">0.85</td>
    </tr>
    <tr>
      <td style="text-align: left;">X-codec (Ye et al., 2025a)</td>
      <td style="text-align: center;">2</td>
      <td style="text-align: center;">1024</td>
      <td style="text-align: center;">50</td>
      <td style="text-align: center;">2.68</td>
      <td style="text-align: center;">3.27</td>
      <td style="text-align: center;">0.86</td>
      <td style="text-align: center;">4.11</td>
      <td style="text-align: center;">0.84</td>
    </tr>
    <tr>
      <td style="text-align: left;">X-codec 2 (Ye et al., 2025b)</td>
      <td style="text-align: center;">1</td>
      <td style="text-align: center;">65536</td>
      <td style="text-align: center;">50</td>
      <td style="text-align: center;">2.43</td>
      <td style="text-align: center;">3.04</td>
      <td style="text-align: center;">0.92</td>
      <td style="text-align: center;">4.13</td>
      <td style="text-align: center;">0.82</td>
    </tr>
    <tr>
      <td style="text-align: left;">XY-Tokenizer (Gong et al., 2025)</td>
      <td style="text-align: center;">8</td>
      <td style="text-align: center;">1024</td>
      <td style="text-align: center;">12.5</td>
      <td style="text-align: center;">2.41</td>
      <td style="text-align: center;">3.00</td>
      <td style="text-align: center;">0.91</td>
      <td style="text-align: center;">3.98</td>
      <td style="text-align: center;">0.83</td>
    </tr>
    <tr>
      <td style="text-align: left;">Mimi (Défossez et al., 2024)</td>
      <td style="text-align: center;">16</td>
      <td style="text-align: center;">2048</td>
      <td style="text-align: center;">12.5</td>
      <td style="text-align: center;">2.88</td>
      <td style="text-align: center;">3.42</td>
      <td style="text-align: center;">0.94</td>
      <td style="text-align: center;">3.87</td>
      <td style="text-align: center;">0.87</td>
    </tr>
    <tr>
      <td style="text-align: left;">FireredTTS 2 Tokenizer (Xie et al., 2025)</td>
      <td style="text-align: center;">16</td>
      <td style="text-align: center;">2048</td>
      <td style="text-align: center;">12.5</td>
      <td style="text-align: center;">2.73</td>
      <td style="text-align: center;">3.28</td>
      <td style="text-align: center;">0.94</td>
      <td style="text-align: center;">3.88</td>
      <td style="text-align: center;">0.87</td>
    </tr>
    <tr>
      <td style="text-align: left;">Qwen-TTS-Tokenizer-12Hz</td>
      <td style="text-align: center;">16</td>
      <td style="text-align: center;">2048</td>
      <td style="text-align: center;">12.5</td>
      <td style="text-align: center;"><strong>3.21</strong></td>
      <td style="text-align: center;"><strong>3.68</strong></td>
      <td style="text-align: center;"><strong>0.96</strong></td>
      <td style="text-align: center;"><strong>4.16</strong></td>
      <td style="text-align: center;"><strong>0.95</strong></td>
    </tr>
  </tbody>
</table>

</details>


## Citation

本論文とコードが研究に役立つ場合は、ぜひ :star: を付け、引用 :pencil: をお願いします。

```BibTeX
@article{Qwen3-TTS,
  title={Qwen3-TTS Technical Report},
  author={Hangrui Hu and Xinfa Zhu and Ting He and Dake Guo and Bin Zhang and Xiong Wang and Zhifang Guo and Ziyue Jiang and Hongkun Hao and Zishan Guo and Xinyu Zhang and Pei Zhang and Baosong Yang and Jin Xu and Jingren Zhou and Junyang Lin},
  journal={arXiv preprint arXiv:2601.15621},
  year={2026}
}
```


## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=QwenLM/Qwen3-TTS&type=Date)](https://star-history.com/#QwenLM/Qwen3-TTS&Date)


<br>
