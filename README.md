```pyhon
# Google colab Audio To Srt Whisper.git
```python
# prompt: 使用 openai/whisper-large-v3 模型將音訊檔案轉換為 SRT 字幕 並使用 uploaded = files.upload() 上傳檔案

!pip install pydub
!pip install git+https://github.com/openai/whisper.git

import whisper
from google.colab import files
from pydub import AudioSegment
import time
import random
import threading

# 保持控制台活躍
def keep_alive():
    while True:
        braille_char = chr(random.randint(0x2800, 0x28FF))
        print(braille_char, end=" ", flush=True)
        time.sleep(60)

keep_alive_thread = threading.Thread(target=keep_alive, daemon=True)
keep_alive_thread.start()

# 上傳檔案
uploaded = files.upload()

for fn in uploaded.keys():
    print(f'用戶上傳的檔案 "{fn}" 長度為 {len(uploaded[fn])} 字節')
    audio_file_path = fn

# SRT 時間戳轉換函數
def to_srt_timestamp(time_str):
    seconds = float(time_str)
    milliseconds = round((seconds % 1) * 1000)
    seconds = int(seconds)
    minutes = seconds // 60
    seconds = seconds % 60
    hours = minutes // 60
    minutes = minutes % 60
    return f"{hours:02}:{minutes:02}:{seconds:02},{milliseconds:03}"

# Load Whisper model
model = whisper.load_model("base")

# Transcribe audio
result = model.transcribe(audio_file_path)

# Convert to SRT format
srt_content = ""
for i, segment in enumerate(result['segments']):
    start_time = to_srt_timestamp(segment['start'])
    end_time = to_srt_timestamp(segment['end'])
    text = segment['text']
    srt_content += f"{i+1}\n{start_time} --> {end_time}\n{text}\n\n"

# Save SRT content to a file
with open("transcription.srt", "w") as srt_file:
    srt_file.write(srt_content)

print("Transcription saved as transcription.srt")

# 下載檔案
files.download("transcription.srt")
```