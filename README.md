# assets

## edge-tts


### cli

```bash
# 安装 edge-tts
pip install edge-tts

# 列出 edge-tts 支持的语音
edge-tts --list-voices

# 合成语音： "你好啊，我是智能语音助手"
# 保存到文件： audio__zh-CN-XiaoyiNeural__1.mp3
edge-tts --voice zh-CN-XiaoyiNeural --text "你好啊，我是智能语音助手" --write-media audio__zh-CN-XiaoyiNeural__1.mp3

# 调整合成语音的语速
edge-tts --voice zh-CN-XiaoyiNeural --rate=-50% --text "你好啊，我是智能语音助手" --write-media audio__zh-CN-XiaoyiNeural__2.mp3

# 调整语音的音量。
edge-tts --voice zh-CN-XiaoyiNeural --volume=-50% --text "你好啊，我是智能语音助手" --write-media audio__zh-CN-XiaoyiNeural__3.mp3

```

### python

#### Python 脚本语音合成

```python
import asyncio  

import edge_tts  

TEXT = "你好哟，我是智能语音助手，小伊"  
VOICE = "zh-CN-XiaoyiNeural"  
OUTPUT_FILE = f"./audio__{VOICE}__1.mp3"  

async def _main() -> None:     
    communicate = edge_tts.Communicate(TEXT, VOICE)  
    await communicate.save(OUTPUT_FILE)  


if __name__ == "__main__":  
    asyncio.run(_main())
```

异步模式调用communicate实例的save方法，就可以并发异步生成语音合成的音频文件:

```python
import asyncio  
import random  

import edge_tts  
from edge_tts import VoicesManager  

TEXT = "中文语音测试"  
OUTPUT_FILE = f"./audio__{VOICE}__2.mp3"  



async def _main() -> None:  
    voices = await VoicesManager.create()  
    voice = voices.find(Gender="Female", Language="zh")  

    OUTPUT_FILE = f"./audio__{voice}__2.mp3"  

    communicate = edge_tts.Communicate(TEXT, random.choice(voice)["Name"])  
    await communicate.save(OUTPUT_FILE)  


if __name__ == "__main__":  
    asyncio.run(_main())
```


将语音流和字幕同步进行生成:

```python
import asyncio  

import edge_tts  

TEXT = "这里是语音流测试"  
VOICE = "zh-CN-XiaoyiNeural"  
OUTPUT_FILE = f"./audio__{VOICE}__3.mp3"  
WEBVTT_FILE = f"./audio__{VOICE}__3_text.vtt"  


async def _main() -> None:  
    communicate = edge_tts.Communicate(TEXT, VOICE)  
    submaker = edge_tts.SubMaker()  
    with open(OUTPUT_FILE, "wb") as file:  
        async for chunk in communicate.stream():  
            if chunk["type"] == "audio":  
                file.write(chunk["data"])  
            elif chunk["type"] == "WordBoundary":  
                submaker.create_sub((chunk["offset"], chunk["duration"]), chunk["text"])  

    with open(WEBVTT_FILE, "w", encoding="utf-8") as file:  
        file.write(submaker.generate_subs())  


if __name__ == "__main__":  
    asyncio.run(_main())
```

https://github.com/agermanidis/autosub.git