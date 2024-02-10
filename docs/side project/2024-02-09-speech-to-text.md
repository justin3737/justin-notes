---
slug: speech-to-text
title: AI 語音轉文字使用 whisper + ChatGPT
authors:
  name: Justin
  url: https://github.com/justin3737
  image_url: https://avatars.githubusercontent.com/u/3293292?s=400&u=0cf29916981c562345a57d34b7baa92e5816c863&v=4
tags: ["python", "whisper", "chatgpt", "side project"]
---

### 原因與訴求

在閒聊時發現同事遇到一些困難點，剛好最近我有在研究生程式 AI 相關議題，剛好有機會可以協助同事提高其生產力，且避免消耗精力去做一些繁瑣的工作：目的與訴求是希望能夠快速整理訪談的逐字稿或會議紀錄。

#### 於是讓我有了些靈感：

使用 `Whisper API` 可以將`Youtube`影片或是任何聲音`mp3`轉回文字檔案，再透過 ChatGPT 做文字的整理重點摘錄，適用於會議紀錄或是獲取新知。

### 使用技術

- Python (Jupyter Notebook)
- Whisper
- ChatGPT

### 開發

指定 ChatGPT Token

```python
TOKEN = ''
```

### 使用 yt-dlp 下載 Youtube 影片

```python
!pip install yt-dlp
```

### 安裝 FFMPEG

```python
// MacOS
$ brew install ffmpeg
```

#### 找到 FFMPEG 位置

```python
$ witch ffmpeg
// /opt/homebrew/bin/ffmpeg
```

### 下載 Youtube 影片並轉為 mp3

```python
import yt_dlp

# 設定選項
ydl_opts = {
    'format': 'bestaudio/best',
    'ffmpeg_location':'/opt/homebrew/bin/ffmpeg', // 需要指定 ffmpeg 路徑
    'outtmpl': '<FileName>',
    'postprocessors': [{
        'key': 'FFmpegExtractAudio',
        'preferredcodec': 'mp3',
        'preferredquality': '192',
    }],
}

# 填入影片的URL
url = 'https://www.youtube.com/watch?v=<Youtube_ID>'

# 建立 yt_dlp 下載器物件
with yt_dlp.YoutubeDL(ydl_opts) as ydl:
    ydl.download([url])
```

### 使用 Whisper API 將音檔轉換成文字

```python
!pip install openai
```

```python
import os
from openai import OpenAI

client = OpenAI(api_key=TOKEN)
audio_file = open("<File_Name>.mp3", "rb")
transcript = client.audio.transcriptions.create(
  model="whisper-1",
  file=audio_file
)
```

### 使用 pydub 分割音檔

為了避免音檔過長需要將其分割

```python
!pip install pydub
```

```python
from pydub import AudioSegment

# 載入 MP3 音檔
sound = AudioSegment.from_file('<File_Name>.mp3', format='mp3')

# 設定每個分割檔案的長度（單位：毫秒）
segment_length = 1000000

# 將音檔分割成多個檔案
for i, chunk in enumerate(sound[::segment_length]):
    # 設定分割檔案的檔名
    chunk.export(f'output_{i}.mp3', format='mp3')

```

### 分割檔案個別產生文字後合併

`transcript_0`儲存第一段文字

```python
import os
import os
from openai import OpenAI

client = OpenAI(api_key=TOKEN)
audio_file = open("output_0.mp3", "rb")
transcript_0 = client.audio.transcriptions.create(
  model="whisper-1",
  file=audio_file
)

```

`transcript_1`儲存第二段文字

```python
audio_file = open("output_1.mp3", "rb")
transcript_1 = client.audio.transcriptions.create(
  model="whisper-1",
  file=audio_file
)
```

將兩段文字合併

```python
transcript = transcript_0.to_dict().get('text') + ' ' + transcript_1.to_dict().get('text')
```

### 使用 ChatGPT API 摘要文章

```python
import os
import openai
openai.api_key = TOKEN

result_ary = []

for t in transcript_ary:
    completion = openai.ChatCompletion.create(
      model="gpt-3.5-turbo",
      messages=[
        {"role": "system", "content": "請你成為文章摘要的小幫手，摘要以下文字，以繁體中文輸出"},
        {"role": "user", "content": t}
      ]
    )

    result_ary.append(completion.choices[0].message)
print(result_ary)
```

### 結論

- 使用 `Whisper API` 可以將`Youtube`影片或是任何聲音`mp3`轉回文字檔案，再透過 ChatGPT 做文字的整理重點摘錄，適用於會議紀錄或是獲取新知。
