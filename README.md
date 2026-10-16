# Qwen-Image 3.0 API (`qwen-image-3.0`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-hero-perfume.jpg" width="820" alt="Sample render via the APIMart image API">
</p>

> **from $0.0205712 per image** (1K $0.0205712 · 2K $0.0205712) — `https://api.apimart.ai/v1` OpenAI 호환 엔드포인트 하나면 되고, 월 구독 플랜은 필요 없습니다. *(2026-10-12 기준)*

**[API 키](https://go.apimart.ai/k-1095fb)** · **[가격](https://go.apimart.ai/k-a02abf)** · **[모델 페이지](https://go.apimart.ai/k-5f3f88)** · [⚡ 60초 퀵스타트](#quickstart)

**팀들이 APIMart를 통해 Qwen-Image 3.0(`qwen-image-3.0`)을 쓰는 이유**

- **키 하나로 카탈로그 전체를.** 동일한 `https://api.apimart.ai/v1` base URL과 `Authorization` 헤더로 Qwen-Image 3.0(`qwen-image-3.0`)과 이미지·비디오·언어 모델 300+ 개에 접근할 수 있습니다. 바꾸는 건 `model` 필드뿐이고 클라이언트는 그대로입니다.
- **최소 $1, 종량제.** 구독도, 사전에 규모를 정해야 하는 선불 플랜도 없습니다. $1부터 충전해 호출한 만큼만 쓰면 됩니다.
- **과금 내역은 응답으로 돌아옵니다.** 완료된 모든 작업은 청구 금액(`cost` / `credits_cost`)을 보고하고, `failed`로 표시된 작업은 환불됩니다.
- **비동기 설계.** 제출하고 `task_id`를 받아 `GET /v1/tasks/{id}`를 폴링하면 끝입니다. 배치와 재시도는 별도 통합이 아니라 평범한 큐 작업입니다.

<!-- /conv-kit:v1 -->



Qwen-Image 3.0 does text-to-image generation and editing with 1K/2K output through Alibaba's Qwen image route.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Per-image | `qwen-image-3.0` | per delivered image, by resolution | see the [model page](https://go.apimart.ai/k-5f3f88) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/images/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Output | List price | Effective price |
| --- | --- | --- |
| default | 0.025714 | 0.0205712 |
| 1K | 0.025714 | 0.0205712 |
| 2K | 0.025714 | 0.0205712 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-a02abf) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | what to generate, or what to change when editing |
| `size` | string | `auto` / `1:1` | aspect ratio, or `auto` where the route supports it |
| `resolution` | string | `1K` | output resolution tier — cost scales with it |
| `n` | integer | `1` | number of images per task, where the route allows more than one |
| `image_urls` | string[] | — | reference images as public HTTP(S) URLs or base64 data URLs |
| `nsfw_check` | boolean | `false` | runs `omni-moderation-latest` before submitting |

The table lists the fields accepted by the APIMart `images/generations` endpoint. Check the [model page](https://go.apimart.ai/k-5f3f88) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/images/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"qwen-image-3.0","prompt":"A bamboo forest at sunrise, slow push-in","size":"1:1","resolution":"1K"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/images/generations", headers=HEADERS, timeout=60, json={
    "model": "qwen-image-3.0", "prompt": "A bamboo forest at sunrise, slow push-in",
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["images"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Qwen-Image 3.0 모델 id는 무엇인가요?**

`qwen-image-3.0`입니다. 위의 라우트 표를 참고하세요.

**과금 방식은 어떻게 되나요?**

**from $0.0205712 per image** (1K $0.0205712 · 2K $0.0205712). 완료된 작업은 `cost` 필드에 정확한 금액을 보고하고, 실패한 작업은 환불됩니다.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-1095fb) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [qwen-image-3-0-prompts-ko](https://github.com/biqwozw9wumgqu/qwen-image-3-0-prompts-ko)
- **Same model, other languages** — 9 more language builds: 中文 `qwen-image-3-0-api-cn` · Deutsch [qwen-image-3-0-api-de](https://github.com/m3s4stio47e7/qwen-image-3-0-api-de) · Español `qwen-image-3-0-api-es` · Français [qwen-image-3-0-api-fr](https://github.com/pozqtjechvgye8/qwen-image-3-0-api-fr) · Relay/gateway `qwen-image-3-0-api-gateway` · Bahasa [qwen-image-3-0-api-id](https://github.com/dnu2saanzwwg/qwen-image-3-0-api-id) · 日本語 `qwen-image-3-0-api-ja` · Português `qwen-image-3-0-api-pt` · Русский `qwen-image-3-0-api-ru`
- **More models in this language** — [midjourney-api-ko](https://github.com/ocvewn2-9ueo/midjourney-api-ko) · [gemini-2-5-flash-image-preview-api-ko](https://github.com/uiyflzgo/gemini-2-5-flash-image-preview-api-ko) · [nano-banana-api-ko](https://github.com/l-2jb09sq/nano-banana-api-ko) · [gemini-3-1-flash-image-preview-api-ko](https://github.com/sgc3a85h2-33l/gemini-3-1-flash-image-preview-api-ko) · [nano-banana-2-api-ko](https://github.com/jjb18r/nano-banana-2-api-ko) · [gemini-3-pro-image-preview-api-ko](https://github.com/a-me4/gemini-3-pro-image-preview-api-ko)
- **Topic hubs** — every build of this model, all languages: [qwen-image-3-0](https://github.com/topics/qwen-image-3-0) · [qwen-image-3-0-api](https://github.com/topics/qwen-image-3-0-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-5f3f88> | `apimart.ai/ko/model/qwen-image-3-0` |
| Live pricing | <https://go.apimart.ai/k-a02abf> | `apimart.ai/ko/pricing` |
| Get an API key | <https://go.apimart.ai/k-1095fb> | `apimart.ai/ko/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Qwen-Image 3.0 is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
