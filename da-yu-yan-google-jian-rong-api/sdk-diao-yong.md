# SDK 调用

## SDK 调用

## Genai SDK

### 大语言模型

```python
from google import genai
from google.genai import types

client = genai.Client(
    http_options=types.HttpOptions(base_url='https://router.shengsuanyun.com/api'),
    api_key="你的API KEY")

response = client.models.generate_content(
    model="google/gemini-3-flash", contents="Explain how AI works in a few words"
)
print(response.text)
```

### Nano Banana

```python
from google import genai
from google.genai import types
from PIL import Image

client = genai.Client(
    http_options=types.HttpOptions(base_url='https://router.shengsuanyun.com/api'),
    ##海外调用使用以下域名：
    #http_options=types.HttpOptions(base_url='https://ssy-ap.modelspot.ai/api'),
    api_key="你的API KEY")

prompt = ("Create a picture of a nano banana dish in a fancy restaurant with a Gemini theme")
response = client.models.generate_content(
    model="google/gemini-3.1-flash-image-preview",
    contents=[prompt],
)

for part in response.parts:
    if part.text is not None:
        print(part.text)
    elif part.inline_data is not None:
        image = part.as_image()
        image.save("generated_image.png")
```

## AI SDK

```javascript
import { createGoogleGenerativeAI } from '@ai-sdk/google';
import { generateText } from 'ai';

const google = createGoogleGenerativeAI({
  baseURL: 'https://router.shengsuanyun.com/api/v1beta/models',
  apiKey: '你的SDK',
});

const { text } = await generateText({
  model: google('google/gemini-3-flash'),
  prompt: 'Write a vegetarian lasagna recipe for 4 people.',
});

console.log(text);
```
