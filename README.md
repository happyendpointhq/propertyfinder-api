# PropertyFinder API

Real-time access to PropertyFinder UAE property data: 500K+ listings for sale and
rent across Dubai, Abu Dhabi and Sharjah, plus commercial property, off-plan
projects, agents, brokerages, price trends, and historical transactions.
Everything returns structured JSON over a normal REST API, so there is no
scraping, no proxy rotation, and no HTML parsing to maintain.

Built and maintained by [Happy Endpoint](https://happyendpoint.com).

- Subscribe on RapidAPI: https://rapidapi.com/happyendpoint/api/uae-real-estate-property
- Product page: https://happyendpoint.com/library/propertyfinder-api
- Official site: https://uaepropertyapi.com
- Hosted docs: https://happyendpointhq.github.io/propertyfinder-api/
- Postman documentation: https://documenter.getpostman.com/view/56555678/2sBYHVweca
- Postman collection (fork and run): https://www.postman.com/happyendpoints-team/property-finder-api

---

## What you get

PropertyFinder is one of the two dominant property portals in the UAE. This API
exposes its data programmatically:

- Residential listings for sale and rent
- Commercial listings: offices, retail, warehouses, land
- Off-plan and new development projects
- Full property records with images, amenities, specifications, and location
- Agents and brokerages, including direct contact details
- Price trends and community insights by location
- Historical transaction records
- Real estate developers

Coverage is UAE-wide: Dubai, Abu Dhabi, Sharjah, and the northern emirates.
Listings are refreshed daily, and responses are Redis-cached for latency.

---

## Getting started

### 1. Get an API key

Subscribe on RapidAPI. There is a free tier:
https://rapidapi.com/happyendpoint/api/uae-real-estate-property

### 2. Make a request

Every request needs two headers. Start with a location lookup, because the search
endpoints take a location identifier rather than a place name.

```bash
curl "https://uae-real-estate-property.p.rapidapi.com/autocomplete-location?query=dubai%20marina" \
  -H "x-rapidapi-host: uae-real-estate-property.p.rapidapi.com" \
  -H "x-rapidapi-key: YOUR_RAPIDAPI_KEY"
```

### Python

```python
import os

import requests

HOST = "uae-real-estate-property.p.rapidapi.com"
HEADERS = {
    "x-rapidapi-host": HOST,
    "x-rapidapi-key": os.environ["RAPIDAPI_KEY"],
}


def get(path, **params):
    r = requests.get(
        f"https://{HOST}{path}",
        params={k: v for k, v in params.items() if v is not None},
        headers=HEADERS,
        timeout=30,
    )
    r.raise_for_status()
    return r.json()


locations = get("/autocomplete-location", query="dubai marina")
listings = get("/search-buy", location_id="1", price_max=2_000_000)
```

### JavaScript

```javascript
const HOST = 'uae-real-estate-property.p.rapidapi.com';

async function get(path, params = {}) {
  const url = new URL(`https://${HOST}${path}`);
  for (const [k, v] of Object.entries(params)) {
    if (v != null) url.searchParams.set(k, v);
  }

  const res = await fetch(url, {
    headers: {
      'x-rapidapi-host': HOST,
      'x-rapidapi-key': process.env.RAPIDAPI_KEY,
    },
  });
  if (!res.ok) throw new Error(`PropertyFinder API ${res.status}`);
  return res.json();
}

const listings = await get('/search-buy', { location_id: '1', price_max: 2000000 });
```

---

## Endpoints

### Locations and market data

| Endpoint | Returns |
|---|---|
| `GET /autocomplete-location` | Location search. Returns the identifiers other endpoints need |
| `GET /price-trend-of-location` | Price trends over time for a location |
| `GET /property-insight` | Market insight for a property or area |

### Residential listings

| Endpoint | Returns |
|---|---|
| `GET /search-buy` | Properties for sale |
| `GET /search-rent` | Properties for rent |
| `GET /search-new-projects` | Off-plan and new development projects |
| `GET /property-details` | Full record for a single property |

### Commercial listings

| Endpoint | Returns |
|---|---|
| `GET /search-commercial-buy` | Commercial property for sale |
| `GET /search-commercial-rent` | Commercial property for rent |

### Agents and brokerages

| Endpoint | Returns |
|---|---|
| `GET /search-agents` | Agents matching a search |
| `GET /search-agents-detailed` | Agents with extended profile data |
| `GET /agent-properties` | All listings from one agent |
| `GET /search-brokers` | Brokerages matching a search |
| `GET /search-brokers-detailed` | Brokerages with extended profile data |
| `GET /broker-properties` | All listings from one brokerage |
| `GET /real-estate-developer` | Real estate developers |

### Transactions

| Endpoint | Returns |
|---|---|
| `GET /get-transactions` | Historical property transactions |

---

## Using this API from Claude, Cursor, or another MCP client

RapidAPI hosts an MCP server, so you can query this API from an AI assistant
without writing any code:

```json
{
  "mcpServers": {
    "PropertyFinder UAE": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.rapidapi.com",
        "--header",
        "x-api-host: uae-real-estate-property.p.rapidapi.com",
        "--header",
        "x-api-key: YOUR_RAPIDAPI_KEY"
      ]
    }
  }
}
```

| Client | Config path |
|---|---|
| Claude Desktop (macOS) | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Claude Desktop (Windows) | `%APPDATA%\Claude\claude_desktop_config.json` |
| Cursor | `~/.cursor/mcp.json` |
| Claude Code | `.mcp.json` in your project root |

---

## What people build with this

- Property portals and listing aggregators
- Investment dashboards tracking price per sqm by community
- Comparison tools across Dubai neighbourhoods
- Agent and brokerage directories
- Lead generation platforms
- CRM integrations that keep listing data in sync
- Market research on off-plan supply and handover timelines

---

## FAQ

### Does PropertyFinder have an official public API?

No. PropertyFinder does not publish a public API for listing data. This API is
built and maintained by Happy Endpoint to provide that access.

### Is there a free tier?

Yes. RapidAPI hosts a free plan with a monthly request quota, enough to prototype
against.

### How is this different from scraping?

Scraping a property portal means maintaining CSS selectors that break on every
redesign, rotating proxies, handling bot challenges, and accepting that it may
breach the site's terms. This is a REST API returning JSON with a stable
contract.

### How do I get a location identifier?

Call `/autocomplete-location` with the area name. The search endpoints take that
identifier rather than a plain place name. A wrong identifier tends to return a
different area rather than an error, so confirm it rather than guessing.

### What is the difference between listings and transactions?

Listings are asking prices, what sellers currently want. Transactions are what
property actually sold for. For valuation and yield work, use
`/get-transactions`.

### Can I get bulk historical data instead of calling the API?

Yes. Happy Endpoint sells a PropertyFinder UAE historical transactions dataset of
120K+ records as a one-off file. See
[happyendpoint.com/datasets](https://happyendpoint.com/datasets) or email
happyendpointhq@gmail.com.

### Do you cover Bayut as well?

Yes, separately. See [bayut-api](https://github.com/happyendpointhq/bayut-api).
Most people building UAE market coverage use both, since listing inventory only
partly overlaps between the two portals.

---

## Related repos

- [bayut-api](https://github.com/happyendpointhq/bayut-api) - the other major UAE portal
- [uae-real-estate-data-guide](https://github.com/happyendpointhq/uae-real-estate-data-guide) - every route to UAE property data, including alternatives to us
- [bayut-api-python-examples](https://github.com/happyendpointhq/bayut-api-python-examples) - runnable Python patterns that transfer to this API
- [dubai-rental-yield-calculator](https://github.com/happyendpointhq/dubai-rental-yield-calculator) - yield analysis on UAE property data
- [dubai-property-price-tracker](https://github.com/happyendpointhq/dubai-property-price-tracker) - scheduled price monitoring

---

## Disclaimer

Happy Endpoint is an independent provider. This project is **not affiliated
with, endorsed by, sponsored by, or connected to** any of the websites,
platforms, retailers, or marketplaces referenced here or reachable through the
underlying APIs.

All product names, brands, trademarks, and registered trademarks are the
property of their respective owners. Any reference to them is descriptive only,
to identify the subject matter of the data, and does not imply any association
or endorsement.

Users are responsible for ensuring their use of any data complies with
applicable laws and the terms of service of the relevant source.

---

## About Happy Endpoint

[Happy Endpoint](https://happyendpoint.com) builds and maintains real-time data
APIs for property portals, retailers, and marketplaces. All APIs are available on
RapidAPI with a free tier.

- Catalogue: [happyendpoint.com/library](https://happyendpoint.com/library)
- Datasets: [happyendpoint.com/datasets](https://happyendpoint.com/datasets)
- Documentation: [docs.happyendpoint.com](https://docs.happyendpoint.com)
- Contact: happyendpointhq@gmail.com

## Licence

MIT. See [LICENSE](LICENSE).
