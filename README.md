# Google colab Audio To Srt Whisper.git
```py
# prompt: 使用 openai/whisper-large-v3 模型將音訊檔案轉換為 SRT 字幕 並使用 uploaded = files.upload() 上傳檔案

!pip install git+https://github.com/openai/whisper.git
import whisper
from google.colab import files

uploaded = files.upload()

for fn in uploaded.keys():
  print(f'User uploaded file "{fn}" with length {len(uploaded[fn])} bytes')
  audio_file_path = fn

model = whisper.load_model("large-v3")
result = model.transcribe(audio_file_path)

# 助手函數將秒數轉換為 SRT 時間戳格式
def to_srt_timestamp(time_str):
    seconds = float(time_str)
    milliseconds = round((seconds % 1) * 1000)
    seconds = int(seconds)
    minutes = seconds // 60
    seconds = seconds % 60
    hours = minutes // 60
    minutes = minutes % 60
    return f"{hours:02}:{minutes:02}:{seconds:02},{milliseconds:03}"

# 創建SRT文件內容
srt_content = ""
for i, segment in enumerate(result["segments"]):
    start_time = segment["start"]
    end_time = segment["end"]
    text = segment["text"]

    start_time_str = "{:.3f}".format(start_time)
    end_time_str = "{:.3f}".format(end_time)

    srt_content += f"{i+1}\n"
    srt_content += f"{to_srt_timestamp(start_time_str)} --> {to_srt_timestamp(end_time_str)}\n"
    srt_content += f"{text}\n\n"


# 保存 SRT 文件
with open("output.srt", "w") as f:
    f.write(srt_content)

files.download("output.srt")
```
[github openai whisper](https://github.com/openai/whisper)
