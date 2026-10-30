<p align="center">
  <a href="https://www.piloterr.com"><img src="images/banner.png" alt="Rexel Scraper API by Piloterr – scrape Rexel data as JSON" width="100%"></a>
</p>

# Rexel Scraper API

[![Discord](https://img.shields.io/badge/Discord-Join%20the%20community-5865F2?logo=discord&logoColor=white)](https://discord.gg/CFj3ph8aHU) [![Get API key](https://img.shields.io/badge/Piloterr-Get%20a%20free%20API%20key-111827)](https://app.piloterr.com) [![API docs](https://img.shields.io/badge/Docs-OpenAPI-0EA5E9)](https://app.piloterr.com/openapi) [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

The **Piloterr Rexel Scraper API** lets you extract public data from Rexel as **clean, structured JSON** — no proxies, headless browsers or HTML parsing to maintain. Endpoints available in production:

- **Rexel Product API** — Get a Rexel France product page: references, EAN, descriptions, images and specs via browser rendering.
- **Rexel Search API** — Search Rexel France by keyword, EAN or search URL.

Use it for price monitoring, assortment tracking, competitor analysis, product research or catalog enrichment.

| Endpoint | Path | Credits | What you get |
|---|---|---|---|
| [Rexel Product API](#rexel-product-api) | `/v2/rexel/product` | 2 | Get a Rexel France product page |
| [Rexel Search API](#rexel-search-api) | `/v2/rexel/search` | 2 | Search Rexel France by keyword, EAN or search URL. Returns product cards and pagination via browser rendering. |

## Table of contents

- [How it works](#how-it-works)
- [Rexel Product API](#rexel-product-api)
- [Rexel Search API](#rexel-search-api)
- [Code examples in other languages](#code-examples-in-other-languages)
- [Practical use cases](#practical-use-cases)
- [Why Piloterr?](#why-piloterr)
- [FAQ](#faq)
- [Related scrapers](#related-scrapers)
- [Support](#support)

## How it works

1. **Create a free Piloterr account** and copy your API key from the [dashboard](https://app.piloterr.com).
2. **Send a `GET` (or `POST`) request** to `https://api.piloterr.com/v2/...` with the header `x-api-key: YOUR_API_KEY`.
3. **Receive structured JSON** — Piloterr handles rendering, proxies, retries and parsing.

```bash
export PILOTERR_API_KEY="your-api-key"
pip install requests        # Python
# TypeScript: Node.js 18+ (native fetch), run with `npx tsx file.ts`
```

> Every endpoint accepts both `GET` (query string) and `POST` (JSON body with the same fields).

## Rexel Product API

Get a Rexel France product page: references, EAN, descriptions, images and specs via browser rendering.

`GET | POST https://api.piloterr.com/v2/rexel/product` · **2 credits per request** · [Full reference](https://app.piloterr.com/openapi)

### Request parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `query` | string | ✅ | Rexel France product URL containing `/p/{id}`. |
| `return_page_source` | boolean | — | When `true`, return rendered HTML instead of JSON. |

### Python example

```python
import json
import os
import requests

response = requests.get(
    "https://api.piloterr.com/v2/rexel/product",
    headers={"x-api-key": os.environ["PILOTERR_API_KEY"]},
    params={
        "query": "https://www.rexel.fr/frx/HAGWXF122/p/71742449",
    },
    timeout=60,
)
response.raise_for_status()
data = response.json()

print(json.dumps(data, indent=2, ensure_ascii=False)[:2000])
```

### TypeScript example

```typescript
type RexelProduct = {
  ean?: string;
  url?: string;
  name?: string;
  brand?: string;
  images?: string[];
  rexel_ref?: string;
  product_id?: number;
  breadcrumbs?: unknown;
  description?: string;
  specifications?: {
    gamme?: string;
    intensite?: string;
    type_de_pose?: string;
    tension_nominale?: string;
  };
  manufacturer_ref?: string;
  short_description?: string;
};

const params = new URLSearchParams({
  "query": "https://www.rexel.fr/frx/HAGWXF122/p/71742449",
});

const response = await fetch(`https://api.piloterr.com/v2/rexel/product?${params}`, {
  headers: { "x-api-key": process.env.PILOTERR_API_KEY! },
});
if (!response.ok) throw new Error(`Piloterr error ${response.status}: ${await response.text()}`);

const data = (await response.json()) as RexelProduct;

console.log(JSON.stringify(data, null, 2).slice(0, 2000));
```

<details>
<summary><b>POST variant</b></summary>

```http
POST https://api.piloterr.com/v2/rexel/product
x-api-key: YOUR_API_KEY
Content-Type: application/json

{"query": "https://www.rexel.fr/frx/HAGWXF122/p/71742449"}
```
</details>

### Output example

Example response from the API reference, abridged — real values depend on the page you scrape.

```json
{
  "ean": "3250617191227",
  "url": "https://www.rexel.fr/frx/HAGWXF122/p/71742449",
  "name": "Prise de courant gallery 2P+T double pré cablées",
  "brand": "Hager",
  "images": [
    "https://api.rexel.fr/media/api/fr/content/71742449/EXTRA_LARGE_WEBP"
  ],
  "rexel_ref": "HAGWXF122",
  "product_id": 71742449,
  "breadcrumbs": null,
  "description": "Gallery est l'appareillage modulaire 45x45 pour chantiers résidentiels et tertiaires.",
  "specifications": {
    "gamme": "Gallery",
    "intensite": "16 A",
    "type_de_pose": "encastré",
    "tension_nominale": "250 V"
  },
  "manufacturer_ref": "WXF122",
  "short_description": "La prise de courant double pré cablées 2P+T WXF122 fait partie de la gamme gallery."
}
```

### Response fields

| Field | Type | Description |
|---|---|---|
| `ean` | string |  |
| `url` | string |  |
| `name` | string |  |
| `brand` | string |  |
| `images` | string[] |  |
| `rexel_ref` | string |  |
| `product_id` | integer |  |
| `breadcrumbs` | object |  |
| `description` | string |  |
| `specifications` | object |  |
| `specifications.gamme` | string |  |
| `specifications.intensite` | string |  |
| `specifications.type_de_pose` | string |  |
| `specifications.tension_nominale` | string |  |
| `manufacturer_ref` | string |  |
| `short_description` | string |  |

## Rexel Search API

Search Rexel France by keyword, EAN or search URL. Returns product cards and pagination via browser rendering.

`GET | POST https://api.piloterr.com/v2/rexel/search` · **2 credits per request** · [Full reference](https://app.piloterr.com/openapi)

### Request parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `query` | string | ✅ | Keyword, EAN, or Rexel France search URL. |
| `page` | string | — | Page number (1-based). Page 1 omits `page` in the URL. |
| `return_page_source` | boolean | — | When `true`, return rendered HTML instead of JSON. |

### Python example

```python
import json
import os
import requests

response = requests.get(
    "https://api.piloterr.com/v2/rexel/search",
    headers={"x-api-key": os.environ["PILOTERR_API_KEY"]},
    params={
        "query": "prise",
    },
    timeout=60,
)
response.raise_for_status()
data = response.json()

print(json.dumps(data, indent=2, ensure_ascii=False)[:2000])
```

### TypeScript example

```typescript
type RexelSearch = {
  results?: {
    ean?: unknown;
    url?: string;
    name?: string;
    brand?: string;
    image_url?: string;
    rexel_ref?: string;
    product_id?: number;
    description?: string;
    manufacturer_ref?: string;
  }[];
  pagination?: {
    next?: string;
    page?: number;
    per_page?: number;
    total_pages?: number;
    total_results?: number;
  };
};

const params = new URLSearchParams({
  "query": "prise",
});

const response = await fetch(`https://api.piloterr.com/v2/rexel/search?${params}`, {
  headers: { "x-api-key": process.env.PILOTERR_API_KEY! },
});
if (!response.ok) throw new Error(`Piloterr error ${response.status}: ${await response.text()}`);

const data = (await response.json()) as RexelSearch;

console.log(JSON.stringify(data, null, 2).slice(0, 2000));
```

<details>
<summary><b>POST variant</b></summary>

```http
POST https://api.piloterr.com/v2/rexel/search
x-api-key: YOUR_API_KEY
Content-Type: application/json

{"query": "prise"}
```
</details>

### Output example

Example response from the API reference, abridged — real values depend on the page you scrape.

```json
{
  "results": [
    {
      "ean": null,
      "url": "https://www.rexel.fr/frx/HAGWXF122/p/71742449",
      "name": "Prise de courant gallery 2P+T double pré cablées",
      "brand": "HAGER",
      "image_url": "https://api.rexel.fr/media/api/fr/content/71742449/LARGE_WEBP",
      "rexel_ref": "HAGWXF122",
      "product_id": 71742449,
      "description": "La prise de courant double pré cablées 2P+T WXF122 fait partie de la gamme gallery.",
      "manufacturer_ref": "WXF122"
    }
  ],
  "pagination": {
    "next": "https://www.rexel.fr/frx/search?text=prise&page=2",
    "page": 1,
    "per_page": 24,
    "total_pages": 2262,
    "total_results": 54275
  }
}
```

### Response fields

| Field | Type | Description |
|---|---|---|
| `results[]` | object[] |  |
| `results[].ean` | object |  |
| `results[].url` | string |  |
| `results[].name` | string |  |
| `results[].brand` | string |  |
| `results[].image_url` | string |  |
| `results[].rexel_ref` | string |  |
| `results[].product_id` | integer |  |
| `results[].description` | string |  |
| `results[].manufacturer_ref` | string |  |
| `pagination` | object |  |
| `pagination.next` | string |  |
| `pagination.page` | integer |  |
| `pagination.per_page` | integer |  |
| `pagination.total_pages` | integer |  |
| `pagination.total_results` | integer |  |

## Code examples in other languages

Ready-to-run examples live in the [`examples/`](examples) folder. Each one reads your key from the `PILOTERR_API_KEY` environment variable.

| Language | Product | Search |
|---|---|---|
| cURL (Bash) | [`product`](examples/curl/product.sh) | [`search`](examples/curl/search.sh) |
| PHP | [`product`](examples/php/product.php) | [`search`](examples/php/search.php) |
| C# | [`product`](examples/csharp/Product.cs) | [`search`](examples/csharp/Search.cs) |
| Go | [`product`](examples/go/product.go) | [`search`](examples/go/search.go) |
| Java | [`product`](examples/java/ProductExample.java) | [`search`](examples/java/SearchExample.java) |
| Ruby | [`product`](examples/ruby/product.rb) | [`search`](examples/ruby/search.rb) |
| Node.js | [`product`](examples/nodejs/product.mjs) | [`search`](examples/nodejs/search.mjs) |

## Practical use cases

- **Price monitoring** — Track prices, promotions and availability over time to react to competitors.
- **Assortment & catalog tracking** — Follow new products, delisted items and category changes.
- **Competitor analysis** — Benchmark ranges, brands and positioning across retailers.
- **Product research** — Spot best-sellers and trends from search results.
- **Catalog enrichment** — Pull titles, images and attributes into your PIM or marketplace listings.

## Why Piloterr?

- **No infrastructure** — no proxies, headless browsers or CAPTCHA solving to run yourself.
- **Structured JSON** — stable, documented fields instead of brittle HTML selectors.
- **Pay per request** — transparent credit cost per endpoint, failed requests are retried for you.
- **One API key, hundreds of sources** — Rexel plus many other e-commerce, search and company data endpoints in the [Piloterr library](https://www.piloterr.com/library).
- **Any language** — plain HTTPS + JSON, with examples for Python, TypeScript, cURL, PHP, C#, Go, Java, Ruby and Node.js.

## FAQ

### Is it legal to scrape Rexel?

Collecting publicly available data is generally allowed in many jurisdictions, but rules depend on your country, the data you collect and how you use it. Avoid personal data unless you have a lawful basis, respect the website's terms where they apply, and get legal advice for your specific use case. This is not legal advice.

### Do I need proxies or a headless browser?

No. Piloterr handles rendering, proxy rotation and retries on its side. You only send an HTTPS request with your `x-api-key` header and receive JSON.

### How much does a request cost?

Credits per call: `/v2/rexel/product` **2**, `/v2/rexel/search` **2**. See the [Piloterr website](https://www.piloterr.com) for plans.

### What data does the Rexel API return?

Each endpoint returns structured JSON: **Rexel Product** — `ean`, `url`, `name`, `brand`, `images`, `rexel_ref`; **Rexel Search** — `results[]`, `results[].ean`, `results[].url`, `results[].name`, `results[].brand`, `results[].image_url`.

### How do I paginate Rexel results?

Use the `page` parameter and keep requesting while the response indicates more results.

### Can I get the raw HTML instead of JSON?

Yes. Set `return_page_source=true` and the response becomes `{"result": "..."}` with the page source.

### Which languages are supported?

Any language that can make an HTTP request. This README shows Python and TypeScript; the [`examples/`](examples) folder contains cURL, PHP, C#, Go, Java, Ruby and Node.js.

## Related scrapers

- [About You Scraper API](https://github.com/piloterr/aboutyou-scraper)
- [Action Scraper API](https://github.com/piloterr/action-scraper)
- [Aldi Scraper API](https://github.com/piloterr/aldi-scraper)
- [Alibaba Scraper API](https://github.com/piloterr/alibaba-scraper)
- [AliExpress Scraper API](https://github.com/piloterr/aliexpress-scraper)
- [Allegro Scraper API](https://github.com/piloterr/allegro-scraper)
- [All Piloterr scrapers on GitHub](https://github.com/piloterr)

## Support

- 💬 Join the [Piloterr Discord](https://discord.gg/CFj3ph8aHU) to ask questions and share what you build.
- 📚 Browse the [API reference (OpenAPI)](https://app.piloterr.com/openapi) and the [Piloterr library](https://www.piloterr.com/library).
- 🔑 [Get your API key](https://app.piloterr.com).

---

<p align="center">
  <a href="https://www.piloterr.com">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="images/logo-dark.svg">
      <img src="images/logo.svg" alt="Piloterr" height="32">
    </picture>
  </a>
  <br>
  <sub>Built by <a href="https://www.piloterr.com">Piloterr</a> — web scraping APIs for e-commerce, search, real estate and more, one API key.</sub>
</p>

<sub>Rexel is a trademark of its respective owner. This repository is not affiliated with or endorsed by Rexel. Examples are released under the MIT license.</sub>
