## 4. 语气（情感）语音合成

### POST /emotion-tts
三种情感控制方式（互斥，由 `emotionMode` 选择）：

- **方式 3（默认）**：文本情感，由 QwenEmotion 自动推断

```json
{
  "text": "今天天气真好，我们一起去公园散步吧。",
  "voice": "malele_3",
  "emotionMode": 3,
  "emoText": "开心地说",
  "format": "mp3",
  "bitrate": 64
}
```

- **方式 1**：参考音频情感（`emoAudioPrompt` 为音色 id 或路径）

```json
{
  "text": "今天天气真好。",
  "emotionMode": 1,
  "emoAudioPrompt": "voice_07",
  "emoAlpha": 0.65
}
```

- **方式 2**：8 维情感向量 `[happy, angry, sad, afraid, disgusted, melancholic, surprised, calm]`

```json
{
  "text": "今天天气真好。",
  "emotionMode": 2,
  "emoVector": [0, 0, 0, 0, 0, 0, 0, 1],
  "emoAlpha": 0.65
}
```

**可选参数**：`voice`（音色）、`speed`（语速）、`seed`（固定结果）、`removeSilence`（去静音）、`format`（wav/mp3/ogg/flac，默认 mp3）、`bitrate`（仅 mp3，32~128）。

**响应**：对应格式的二进制音频。

## 5. 音色列表

### GET /voices

获取可用的语音合成音色列表。

**响应**：JSON 数组，每个元素包含 `name`（显示名）与 `id`（合成请求 `voice` 字段使用的音色标识）。

```json
[
  { "name": "乐乐3", "id": "malele_3" },
  { "name": "malele_4", "id": "malele_4" }
]
```