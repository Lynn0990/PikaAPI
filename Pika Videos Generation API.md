# Pika Videos Generation API Integration Instructions

This article will introduce the integration instructions for the Pika Videos Generation API, which can generate videos from Pika by inputting custom parameters.

Next, we will introduce the integration instructions for the Pika Videos Generation API.

## Application Process

To use the API, you first need to apply for the corresponding service on the [Pika Videos Generation API](https://surl.id/1uKeB0DsMm) page. After entering the page, click the "Acquire" button, as shown in the image:

![](https://cdn.acedata.cloud/q6ytrc.png)

If you are not logged in or registered yet, you will be automatically redirected to the login page, inviting you to register and log in. After logging in or registering, you will automatically return to the current page.

When applying for the first time, there will be free credits offered, allowing you to use this API for free.

## Basic Usage

First, let's understand the basic usage method, which includes inputting the prompt `prompt`, the action `action`, the reference image `image_url`, and the model `model`, to obtain the processed result. Firstly, you need to pass a simple `action` field with the value set to `generate`, and then we also need to input the model, which currently are mainly `2.0` and `1.5`. The specific content is as follows:

<p><img src="https://cdn.acedata.cloud/owox5a.png" width="500" class="m-auto"></p>

We can see here that we have set up the Request Headers, including:

- `accept`: the format of the response you want to receive; here it is filled in as `application/json`, which is in JSON format.
- `authorization`: the key to call the API, which can be directly selected after application.

Additionally, the Request Body is set up, including:

- `model`: the model for generating the video, mainly `2.0` and `1.5`.
- `action`: the action of this video generation task.
- `image_urls`: the link to the reference images or Base64 encoding to be uploaded.
- `prompt`: the prompt.
- `callback_url`: the URL to receive callback results.

After selection, you will find that corresponding code has also been generated on the right side, as shown in the image:

<p><img src="https://cdn.acedata.cloud/4cfr6o.png" width="500" class="m-auto"></p>

Click the "Try" button to test, as shown in the figure, and here we obtained the following result:

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

The returned result contains several fields, described as follows:

- `success`: the status of the video generation task.
- `task_id`: the ID of the video generation task.
- `trace_id`: the tracking ID of the video generation task.
- `data`: the result list of the video generation task.
  - `id`: the ID of the video from the generation task.
  - `image_url`: the cover link of the video generation task.
  - `video_url`: the video link of the generation task.
  - `duration`: the duration of the video generation task.
  - `state`: the status of the video generation task.

We can see that we have obtained satisfactory video information; we just need to retrieve the generated Pika video based on the video link address in `data`.

Additionally, if you want to generate the corresponding integration code, you can directly copy it, for example, the CURL code is as follows:

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

## Multi-Image Video Task Creation

If you want to create a video task from multiple images, you can set the `ingredients` parameter to `true`, and the `image_url` must contain multiple image links. At this time, `model` only supports `2.0`, allowing you to specify the following content:

- ingredients: whether to enable multi-image video task creation.
- model: the model used for this video generation task; currently, multi-image video task creation supports `2.0`.
- ingredients_mode: the mode used for this multi-image video task, either `creative` for creative mode or `precise` for precise mode.
- image_url: upload links for multiple reference images.

The sample code is filled out as follows:

<p><img src="https://cdn.acedata.cloud/r2v8b3.png" width="500" class="m-auto"></p>

Once filled in, the code is automatically generated as follows:

<p><img src="https://cdn.acedata.cloud/c6aefh.png" width="500" class="m-auto"></p>

Corresponding code:

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

When you click run, you can see that you will immediately get a result as follows:

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

You can see that the generated effect meets the needs of multi-image video creation, and the result is similar to that above.

## Asynchronous Callback

Since the video generation time for the Pika Videos Generation API is relatively long, approximately 1-2 minutes, if the API does not respond for a long time, the HTTP request will maintain the connection, leading to additional system resource consumption. Hence, this API also provides support for asynchronous callbacks.

The overall process is: when the client initiates a request, an additional `callback_url` field is specified. After the client initiates the API request, the API will immediately return a result, including a `task_id` field that represents the current task ID. Once the task is completed, the results of the generated video will be sent to the client's specified `callback_url` in the form of a POST JSON, which also includes the `task_id` field, allowing the task results to be linked by ID.
Next, let's understand how to operate specifically through examples.

First of all, the Webhook callback is a service that can receive HTTP requests, and developers should replace this with the URL of their own built HTTP server. Here, for the sake of convenience in demonstration, we use a public Webhook sample site https://webhook.site/. Opening this website will provide a Webhook URL as shown in the image:

![](https://cdn.acedata.cloud/cjjfly.png)

Copy this URL, and it can be used as a Webhook. The sample here is `https://webhook.site/3d32690d-6780-4187-a65c-870061e8c8ab`.

Next, we can set the field `callback_url` to the above Webhook URL, while filling in the corresponding parameters, as shown in the image:

<p><img src="https://cdn.acedata.cloud/m03kta.png" width="500" class="m-auto"></p>

Clicking run, we can immediately get a result as follows:

```
{
  "task_id": "c372e7bc-1877-4b6d-9732-e1b9e16c06c0"
}
```

After a moment, we can observe the generated song's results at `https://webhook.site/3d32690d-6780-4187-a65c-870061e8c8ab`, as shown in the image:

![](https://cdn.acedata.cloud/3e6u9v.png)

The content is as follows:

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

We can see that there is a `task_id` field in the result, and the other fields are similar to the above text. This field can be used to link tasks.

## Error Handling

When calling the API, if an error occurs, the API will return the corresponding error code and message. For example:

- `400 token_mismatched`: Bad request, possibly due to missing or invalid parameters.
- `400 api_not_implemented`: Bad request, possibly due to missing or invalid parameters.
- `401 invalid_token`: Unauthorized, invalid or missing authorization token.
- `429 too_many_requests`: Too many requests, you have exceeded the rate limit.
- `500 api_error`: Internal server error, something went wrong on the server.

### Error Response Example

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

## Conclusion

Through this document, you have learned how to use the Pika Videos Generation API to generate videos via input prompt words and reference images. We hope this document helps you better integrate and use this API. If you have any questions, please feel free to contact our technical support team.