# Pika Videos Generation API 对接说明

本文将介绍一种 Pika Videos Generation API 对接说明，它是可以通过输入自定义参数来生成Pika官方的视频。

接下来介绍下 Pika Videos Generation API 的对接说明。

## 申请流程

要使用 API，需要先到 [Pika Videos Generation API](https://surl.id/1uKeB0FZv6) 对应页面申请对应的服务，进入页面之后，点击「Acquire」按钮，如图所示：

![](https://cdn.acedata.cloud/q6ytrc.png)

如果你尚未登录或注册，会自动跳转到登录页面邀请您来注册和登录，登录注册之后会自动返回当前页面。

在首次申请时会有免费额度赠送，可以免费使用该 API。

## 基本使用

首先先了解下基本的使用方式，就是输入提示词 `prompt`、 生成行为 `action`、参考图片 `image_url` 以及模型 `model`，便可获得处理后的结果，首先需要简单地传递一个 `action` 字段，它的值为 `generate`，然后我们还需要输入模型，目前主要有 `2.0` 和 `1.5`，具体的内容如下：

<p><img src="https://cdn.acedata.cloud/owox5a.png" width="500" class="m-auto"></p>

可以看到这里我们设置了 Request Headers，包括：

- `accept`：想要接收怎样格式的响应结果，这里填写为 `application/json`，即 JSON 格式。
- `authorization`：调用 API 的密钥，申请之后可以直接下拉选择。

另外设置了 Request Body，包括：

- `model`：生成视频的模型，主要有`2.0` 和 `1.5`俩种。
- `action`：此次视频生成任务的行为。
- `image_urls`：需要上传的参考图片链接或者Base64编码。
- `prompt`：提示词。
- `callback_url`：需要回调结果的URL。

选择之后，可以发现右侧也生成了对应代码，如图所示：

<p><img src="https://cdn.acedata.cloud/4cfr6o.png" width="500" class="m-auto"></p>

点击「Try」按钮即可进行测试，如上图所示，这里我们就得到了如下结果：

```json
{
  "success": true,
  "task_id": "9ef5c974-b7d9-461a-be36-b2363ddb785c",
  "trace_id": "6eafada2-73ec-4660-a6cb-ab562062bf76",
  "data": [
    {
      "id": "6eb7ade2-6d1b-44a0-a754-948a80e2ef16",
      "video_url": "https://cdn.pika.art/v1/6eb7ade2-6d1b-44a0-a754-948a80e2ef16/moving_seed1548117012.mp4",
      "image_url": "https://cdn.pika.art/v1/6eb7ade2-6d1b-44a0-a754-948a80e2ef16/thumbnail.jpg",
      "duration": 5,
      "state": "succeeded"
    }
  ]
}
```

返回结果一共有多个字段，介绍如下：

- `success`，此时视频生成任务的状态情况。
- `task_id`，此时视频生成任务ID。
- `trace_id`，此时视频生成跟踪ID。
- `data`，此时视频生成任务的结果列表。
	- `id`，此时视频生成任务的视频ID。
	- `image_url`，此时视频生成任务的封面链接。
	- `video_url`，此时视频生成任务的视频链接。
	- `duration`，此时视频生成任务的时长。
	- `state`，此时视频生成任务的状态。

可以看到我们得到了满意的视频信息，我们只需要根据结果中 `data` 的视频链接地址获取生成的Pika视频即可。

另外如果想生成对应的对接代码，可以直接复制生成，例如 CURL 的代码如下：

```shell
curl -X POST 'https://api.acedata.cloud/pika/videos' \
-H 'accept: application/json' \
-H 'authorization: Bearer {token}' \
-H 'content-type: application/json' \
-d '{
  "action": "generate",
  "prompt": "moving",
  "model": "2.0"
}'
```

## 多图创建视频任务

如果想多图创建视频任务，可以将参数 `ingredients` 设置为 `true`， 同时image_url必须传入多个图片链接，此时 `model` 只支持 `2.0`，就可以指定如下内容：

- ingredients：是否启用多图创建视频任务。
- model：此次生成视频任务所采用的模型，多图创建视频任务目前支持者 `2.0`。
- ingredients_mode：此次多图创建视频任务所采用的模式，分别有 `creative` 创意模式 和 `precise` 精确模式。
- image_url：上传多张参考图片链接。

填写样例如下：

<p><img src="https://cdn.acedata.cloud/r2v8b3.png" width="500" class="m-auto"></p>

填写完毕之后自动生成了代码如下：

<p><img src="https://cdn.acedata.cloud/c6aefh.png" width="500" class="m-auto"></p>

对应的代码：

```python
import requests

url = "https://api.acedata.cloud/pika/videos"

headers = {
    "accept": "application/json",
    "authorization": "Bearer {token}",
    "content-type": "application/json"
}

payload = {
    "action": "generate",
    "prompt": "moving",
    "model": "2.0",
    "image_url": ["https://cdn.acedata.cloud/rarbq7.png","https://cdn.acedata.cloud/aeruff.png"],
    "ingredients": True,
    "ingredients_mode": "precise"
}

response = requests.post(url, json=payload, headers=headers)
print(response.text)
```

点击运行，可以发现会立即得到一个结果，如下：

```
{
  "success": true,
  "task_id": "b8170557-7277-4578-b51b-88c74da4e90d",
  "trace_id": "d885146b-0391-4151-9308-57038675b13b",
  "data": [
    {
      "id": "e4692395-080f-4698-9bdf-c5e44542e3d6",
      "video_url": "https://cdn.pika.art/v1/e4692395-080f-4698-9bdf-c5e44542e3d6/moving_seed1906516883.mp4",
      "image_url": "https://cdn.pika.art/v1/e4692395-080f-4698-9bdf-c5e44542e3d6/thumbnail.jpg",
      "duration": 5,
      "state": "succeeded"
    }
  ]
}
```

可以看到，生成的效果是满足多图创建视频的，结果与上文类似。

## 异步回调

由于 Pika Videos Generation API生成的时间相对较长，大约需要 1-2 分钟，如果 API 长时间无响应，HTTP 请求会一直保持连接，导致额外的系统资源消耗，所以本 API 也提供了异步回调的支持。

整体流程是：客户端发起请求的时候，额外指定一个 `callback_url` 字段，客户端发起 API 请求之后，API 会立马返回一个结果，包含一个 `task_id` 的字段信息，代表当前的任务 ID。当任务完成之后，生成视频的结果会通过 POST JSON 的形式发送到客户端指定的 `callback_url`，其中也包括了 `task_id` 字段，这样任务结果就可以通过 ID 关联起来了。

下面我们通过示例来了解下具体怎样操作。

首先，Webhook 回调是一个可以接收 HTTP 请求的服务，开发者应该替换为自己搭建的 HTTP 服务器的 URL。此处为了方便演示，使用一个公开的 Webhook 样例网站 https://webhook.site/，打开该网站即可得到一个 Webhook URL，如图所示：

![](https://cdn.acedata.cloud/cjjfly.png)

将此 URL 复制下来，就可以作为 Webhook 来使用，此处的样例为 `https://webhook.site/3d32690d-6780-4187-a65c-870061e8c8ab`。

接下来，我们可以设置字段 `callback_url` 为上述 Webhook URL，同时填入相应的参数，具体的内容如图所示：

<p><img src="https://cdn.acedata.cloud/m03kta.png" width="500" class="m-auto"></p>

点击运行，可以发现会立即得到一个结果，如下：

```
{
  "task_id": "c372e7bc-1877-4b6d-9732-e1b9e16c06c0"
}
```

稍等片刻，我们可以在 `https://webhook.site/3d32690d-6780-4187-a65c-870061e8c8ab` 上观察到生成歌曲的结果，如图所示：

![](https://cdn.acedata.cloud/3e6u9v.png)

内容如下：

```json
{
    "success": true,
    "task_id": "c372e7bc-1877-4b6d-9732-e1b9e16c06c0",
    "trace_id": "781cc036-926b-44d2-83ea-4045bf8e85d4",
    "data": [
        {
            "id": "458d50a4-43d1-4339-927a-c2f7d43fa570",
            "video_url": "https://cdn.pika.art/v1/458d50a4-43d1-4339-927a-c2f7d43fa570/moving_seed3878215573.mp4",
            "image_url": "https://cdn.pika.art/v1/458d50a4-43d1-4339-927a-c2f7d43fa570/thumbnail.jpg",
            "duration": 5,
            "state": "succeeded"
        }
    ]
}
```

可以看到结果中有一个 `task_id` 字段，其他的字段都和上文类似，通过该字段即可实现任务的关联。


## 错误处理

在调用 API 时，如果遇到错误，API 会返回相应的错误代码和信息。例如：

- `400 token_mismatched`：Bad request, possibly due to missing or invalid parameters.
- `400 api_not_implemented`：Bad request, possibly due to missing or invalid parameters.
- `401 invalid_token`：Unauthorized, invalid or missing authorization token.
- `429 too_many_requests`：Too many requests, you have exceeded the rate limit.
- `500 api_error`：Internal server error, something went wrong on the server.

### 错误响应示例

```json
{
  "success": false,
  "error": {
    "code": "api_error",
    "message": "fetch failed"
  },
  "trace_id": "2cf86e86-22a4-46e1-ac2f-032c0f2a4e89"
}
```

## 结论

通过本文档，您已经了解了如何使用 Pika Videos Generation API 可通过输入提示词以及参考图片来生成视频。希望本文档能帮助您更好地对接和使用该 API。如有任何问题，请随时联系我们的技术支持团队。