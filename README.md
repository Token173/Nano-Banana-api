# Nano Banana 中转 API：国内直连谷歌图像模型，Nano Banana Pro 与 2 一起用

Nano Banana 是 Google 那一套图像模型的民间叫法，从早期的快速出图到现在支持局部编辑、多图参考、2K 细节，一直是设计师换图最快的一档。真正用起来的问题还是网络：Gemini 的图像端点在国内直连不通。

Token173（https://token173.net）提供国内入口，协议兼容 OpenAI 图像格式，一个 Key 能同时调 Nano Banana 系列和 `gpt-image-2.6-flare`、`gpt-image-2.6-sunburst`，方便对比两边出图风格。

## 可用模型

| 模型 ID | 说明 |
|---|---|
| `nano-banana-pro` | 细节和多图参考能力强，商业素材常用 |
| `nano-banana-2` | 新一代，速度和一致性更好 |
| `gemini-3.8-flash` | 文本模型，配合生成 prompt 或看图描述 |

## 文生图

```bash
curl https://token173.net/v1/images/generations \
  -H "Authorization: Bearer sk-你的令牌" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "nano-banana-pro",
    "prompt": "极简风格的产品海报底图，米白背景，阴影柔和，右侧留白",
    "size": "1024x1536"
  }'
```

```python
from openai import OpenAI
import base64

client = OpenAI(api_key="sk-你的令牌", base_url="https://token173.net/v1")

r = client.images.generate(
    model="nano-banana-2",
    prompt="极简风格的产品海报底图，米白背景，阴影柔和，右侧留白",
    size="1024x1536",
)
open("poster.png", "wb").write(base64.b64decode(r.data[0].b64_json))
```

## 改图：把原图丢进去说改哪里

```python
r = client.images.edit(
    model="nano-banana-pro",
    image=open("poster.png", "rb"),
    prompt="把背景换成雾蓝色，产品保持原样",
)
```

Nano Banana 系列在"只改指定区域、其他不动"这件事上比较听话，做详情页改图比重新生成省事。

## 两个模型怎么分工

| 任务 | 选哪个 |
|---|---|
| 电商主图、产品精修 | `nano-banana-pro`，细节稳 |
| 社媒配图、批量海报 | `nano-banana-2`，速度快、成本低 |
| 需要多张参考图保持风格 | `nano-banana-pro` |
| 出图后要配文案 | 同一个 Key 调 `gemini-3.8-flash` 或 `gpt-6.1-sol` |

## 提示词写法

中文可以直接用，但描述要具体：

> 米白色背景，产品居中偏左，右上方一大块留白放标题，光从左上打下来，柔和阴影，不要出现文字和 logo

比"做个高级感的图"有效得多。需要控制构图比例时，把画面分层说清楚——主体在哪、留白在哪、光从哪来。

## 计费

图像按张计费，见后台模型页的实时单价。账户 2 元 = 1 美元，注册送 0.2 美元。批量跑之前先用最低档跑通流程。

## 常见问题

**Q：和 `gpt-image-2` 比哪个好？**
风格不一样。Nano Banana 偏干净、留白大，适合极简和产品图；`gpt-image-2.6-sunburst` 细节更密，适合复杂场景。建议同一个 prompt 两边各出一张对比。

**Q：能不能出 2K 的图？**
支持，把尺寸提到对应档位即可，单价会高一档。

**Q：生成的人物会不会崩？**
人物面部是各家通病。要求高就把人物交给专门的写实模型，Nano Banana 用来做场景和产品。

**Q：图片能商用吗？**
模型输出可以用，具体授权条款按你所在平台的规定走，品牌 logo、真人肖像这类素材自己把关。

**Q：一次能传几张参考图？**
支持多图参考，具体张数看模型版本，一般 2 到 4 张够用。

## 相关

- 文档站：https://docs.token173.net
- GPT 图像：[gpt-image-api](https://github.com/Token173/gpt-image-api)
- Gemini 文本与多模态：[gemini-api](https://github.com/Token173/gemini-api)
- 视频生成：[veo-proxy-](https://github.com/Token173/veo-proxy-)
