# Live API

## Google AI Studio 原生 Live API 支持

本平台支持 Google AI Studio 原生 Live API 协议。您可以直接使用 Google 官方的 `google-genai` SDK 进行调用，只需简单配置 Base URL 即可无缝切换。如果想要获取更多案例，以及用 WebSocket API 格式调用，请参考官方文档： [Get started with Live API](https://ai.google.dev/gemini-api/docs/live?hl=zh-cn\&example=file-stream)

## 1. 环境准备

安装 Google 官方 Python SDK：

```bash
pip install google-genai soundfile librosa
```

## 2. 关键配置说明

在使用官方 SDK 初始化客户端时，需要通过 `http_options` 指定本平台的 API 地址。

**Base URL**: `https://router.shengsuanyun.com/api`

**API Key**: 使用本平台生成的 API Key

**Model**: 支持的模型名称，例如 `ali/qwen-plus-live`

## 3. Python 调用示例

以下代码展示了如何使用官方 SDK 连接到本平台，发送音频数据并接收实时音频响应。

```python
import asyncio
import io
import wave
import librosa
import soundfile as sf
from google import genai
from google.genai import types

# ================= 配置区域 =================
# 1. 替换为您的 API Key
API_KEY = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# 2. 替换为本平台的 Base URL
# 注意：SDK 会自动处理 WebSocket 连接升级
BASE_URL = "https://router.shengsuanyun.com/api"

# 3. 指定模型名称
MODEL = "ali/qwen-plus-live"
# ===========================================

# 配置系统指令和响应模态
CONFIG = {
    "response_modalities": ["AUDIO"],
    "system_instruction": "You are a helpful assistant and answer in a friendly tone.",
}

async def main():
    # 初始化客户端，配置自定义 Base URL
    client = genai.Client(
        api_key=API_KEY,
        http_options=types.HttpOptions(
            base_url=BASE_URL,
        ),
    )

    try:
        print(f"正在连接到 {BASE_URL}...")

        # 建立 Live 连接
        async with client.aio.live.connect(model=MODEL, config=CONFIG) as session:
            print("连接成功！")

            # --- 准备音频输入 (示例：读取本地 wav 文件) ---
            print("正在加载音频文件...")
            # 这里假设您有一个名为 16000.wav 的文件，采样率为 16k
            # 实际使用中可以是麦克风实时流
            buffer = io.BytesIO()
            # 使用 librosa 加载并重采样为 16000Hz
            y, sr = librosa.load("16000.wav", sr=16000)
            sf.write(buffer, y, sr, format='RAW', subtype='PCM_16')
            buffer.seek(0)
            audio_bytes = buffer.read()
            print(f"已加载 {len(audio_bytes)} 字节音频数据")

            # --- 发送音频数据 ---
            print("正在发送音频...")
            await session.send_realtime_input(
                audio=types.Blob(data=audio_bytes, mime_type="audio/pcm;rate=16000")
            )
            print("音频发送完毕，等待响应...")

            # --- 接收并保存音频响应 ---
            # 输出文件设置：单声道, 16位, 24000Hz (Gemini Live 默认输出 24k)
            wf = wave.open("audio_output.wav", "wb")
            wf.setnchannels(1)
            wf.setsampwidth(2)
            wf.setframerate(24000)

            response_count = 0
            async for response in session.receive():
                # 处理音频数据
                if response.data is not None:
                    wf.writeframes(response.data)
                    response_count += 1
                    print(f"收到音频片段 #{response_count}")

                # 处理文本数据 (如果有)
                if hasattr(response, 'text') and response.text:
                    print(f"收到文本响应: {response.text}")

            wf.close()
            print(f"\n完成！共收到 {response_count} 个音频片段。")
            print("响应音频已保存至 audio_output.wav")

    except Exception as e:
        print(f"\n发生错误: {type(e).__name__}: {e}")
        import traceback
        traceback.print_exc()

if __name__ == "__main__":
    asyncio.run(main())
```

## 4. 注意事项

1. **音频格式**：示例中输入音频使用了 `PCM 16bit 16000Hz`，输出音频默认为 `PCM 16bit 24000Hz`。请确保您的音频处理逻辑与此匹配。
2. **网络连接**：SDK 内部会先发起 HTTP 请求获取配置，然后升级到 WebSocket 连接。请确保您的网络环境允许 WebSocket 通信。
