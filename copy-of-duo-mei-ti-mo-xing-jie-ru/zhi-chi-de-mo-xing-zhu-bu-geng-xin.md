# 支持的模型（逐步更新）

## 异步任务类型模型

| 模型ID                                     | 能力类型                     |
| ---------------------------------------- | ------------------------ |
| `kling/kling-v1-6`                       | 文生视频、图生视频、多图参考生视频        |
| `kling/kling-v2`                         | 文生视频、图生视频                |
| `kling/kling-v2-1`                       | 图生视频                     |
| `kling/kling-v2-1-master`                | 文生视频、图生视频                |
| `kling/lipsync`                          | 对口型                      |
| `bytedance/jimeng_t2i_v30`               | 文生图                      |
| `bytedance/jimeng_t2i_v31`               | 文生图                      |
| `bytedance/jimeng_i2i_v30`               | 图生图                      |
| `bytedance/jimeng_ti2v_v30_pro`          | 图生视频、图生视频                |
| `bytedance/doubao-seedance-1.0-pro`      | 文生视频、图生视频                |
| `bytedance/doubao-seedance-1.0-lite-t2v` | 文生视频                     |
| `bytedance/doubao-seedance-1.0-lite-i2v` | 图生视频                     |
| `ali/qwen-image`                         | 文生图                      |
| `google/vedeepseek-r1`                   | 文生视频、图生视频                |
| `minimax/t2v-01-director`                | 文生视频                     |
| `minimax/i2v-01-director`                | 图生视频                     |
| `minimax/s2v-01`                         | 多图参考生视频                  |
| `vidu/vidu-q1`                           | 文生视频、图生视频、首尾帧生视频         |
| `vidu/vidu-2.0`                          | 图生视频、首尾帧生视频、多图参考生视频      |
| `vidu/vidu-1.5`                          | 文生视频、图生视频、首尾帧生视频、多图参考生视频 |
| `openai/sora`                            | 文生视频                     |
| `bytedance/jimeng_v40`                   | 文生图、图生图                  |

## 同步生成类型模型（也支持异步任务接口调用）

| 模型ID                                | 能力类型    |
| ----------------------------------- | ------- |
| `ali/qwen-image-edit`               | 图像编辑    |
| `bytedance/doubao-seedream-4-0`     | 文生图、图生图 |
| `bytedance/doubao-seedream-3.0-t2i` | 文生图     |
| `bytedance/doubao-seededit-3-0-i2i` | 图生图     |
| `ali/qwen-plus-image-preview`       | 文生图、图生图 |
| `openai/gpt-image-1`                | 文生图     |

## 同步类型图片处理模型（也支持异步任务接口调用）

| 模型ID                      | 能力类型       |
| ------------------------- | ---------- |
| `bytedance/image_enhance` | 图像编辑（图像增强） |
| `bytedance/image_upscale` | 图像编辑（图像放大） |
