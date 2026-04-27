# CatchDoms MCP Server

MCP (Model Context Protocol) server for [CatchDoms](https://catchdoms.com) — search expired and auction domains from **16 platforms** plus **100k+ aged ccTLD domains** at registration price, all enriched with SEO metrics.

Use this server to let AI assistants (Claude, Cursor, Windsurf, etc.) search for expired domains, check auction prices, and find high-value domains programmatically.

## Quick Start (Free — no API key needed)

Try it instantly — no signup required:

```json
{
  "mcpServers": {
    "catchdoms": {
      "url": "https://catchdoms.com/mcp/catchdoms/free",
      "transport": "sse"
    }
  }
}
```

**Free tier includes:** Top 50 domains per search, first 10 names visible, basic SEO metrics. Trust Flow, Citation Flow, and GMB detection require Pro.

### Pro (full access)

```json
{
  "mcpServers": {
    "catchdoms": {
      "url": "https://catchdoms.com/mcp/catchdoms",
      "transport": "sse",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

**Get your API key:** [Sign up for free](https://catchdoms.com/register) → [API Settings](https://catchdoms.com/api-access) → [See pricing](https://catchdoms.com/pricing)

| Feature | Free | Pro | Authority |
|---------|------|-----|-----------|
| Domains per search | 50 | Unlimited | Unlimited |
| Visible domain names | 10 | All | All |
| Trust Flow / Citation Flow | Hidden | Yes | Yes |
| GMB detection | Hidden | Yes | Yes |
| `get_domain` tool | No | Yes | Yes |
| Aged domains (regfee, 100k+) | No | No | Yes |
| Rate limit | 3 req/min | 15 req/min | 60 req/min |

## Tools

### `search_domains`

Search for domains with filters. Returns up to 100 results sorted by quality.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `source` | string | Filter by platform: `dynadot`, `catched`, `dropcatch`, `godaddy`, `gname`, `snapnames`, `ukdroplists`, `subreg`, `webexpire`, `parkio`, `bloomup`, `seodomains`, `nameshift`, `nicsell`, `gnews-domains`, `backorders-domains`, `regfree` (Authority only) |
| `tld` | string | Filter by TLD (`.com`, `.fr`, `.de`). Comma-separated for multiple. |
| `score_min` | integer | Minimum quality score (0-100). 50+ = good, 70+ = excellent. |
| `age_min` | integer | Minimum domain age in years (Wayback first snapshot). |
| `language` | string | Detected language code (`EN`, `FR`, `DE`, `ES`, etc.). |
| `contains` | string | Keyword the domain name must contain. |
| `has_bids` | boolean | Only domains with active auction bids. |
| `has_backlinks` | boolean | Only domains with referring domains. |
| `has_gmb` | boolean | Only domains with a Google Business listing. |
| `has_edu_gov` | boolean | Only domains with .edu or .gov backlinks. |
| `da_min` | integer | Minimum Domain Authority (0-100). |
| `rd_min` | integer | Minimum referring domains count. |
| `tf_min` | integer | Minimum Trust Flow (0-100). |
| `cf_min` | integer | Minimum Citation Flow (0-100). |
| `categories` | string | TTF topic categories, comma-separated (e.g., `Business,Health`). |
| `price_min` | number | Minimum price/bid (USD). |
| `price_max` | number | Maximum price/bid (USD). |
| `snapshots_min` | integer | Minimum Wayback Machine snapshots. |
| `limit` | integer | Results to return (1-100, default 50). |

**Example prompt:**
> Find me French .fr domains with a quality score above 50, at least 10 years old, under $500

### `get_domain`

Get full details for a specific domain.

| Parameter | Type | Description |
|-----------|------|-------------|
| `domain_id` | integer | Domain ID |
| `domain_name` | string | Domain name (e.g., `example.com`) |

### `list_filters`

Get available filter options with counts — sources, TLDs, and languages.

*No parameters.*

### `get_stats`

Get platform statistics — total domains, new today, by source, SEO coverage.

*No parameters.*

## Domain Sources

16 auction & marketplace sources, refreshed daily:

| Source | Type |
|--------|------|
| **Dynadot** | Closeouts (fixed $5-$30) + auctions |
| **GoDaddy** | Expiring auctions + closeouts |
| **DropCatch** | Auctions (Dropped, Pre-Release) |
| **Catched** | Auctions + backorder |
| **Gname** | Auctions + buy now |
| **SnapNames** | Pre-release + closeout |
| **UK Backorder** | UK dropping/expired |
| **Subreg** | Expiring auctions |
| **WebExpire** | French .fr auctions |
| **Park.io** | Premium auctions |
| **BloomUp** | French .fr auctions + buy now |
| **SEO.Domains** | Buy now with SEO metrics |
| **NameShift** | Premium buy-now / make offer |
| **Nicsell** | European ccTLD auctions (top-auktionen) |
| **Gnews Domains** | Hand-curated premium news domains, buy-now |
| **Backorders Domains** | Niche ccTLD auctions (.ie, .si, .paris, .tw, .ph, .mx…) |

### Authority-only: Regfee Database

100k+ aged ccTLD domains across 60+ TLDs (.fr, .de, .it, .co.uk, .nl, .id, .kz and dozens more) **available at registration price**. No auction, no bidding war. Same SEO enrichment (TF/CF/DA/backlinks/Wayback) on every entry. Use `source=regfree` from the MCP `search_domains` tool with an Authority API key.

## SEO Metrics

Every domain is enriched with:

- **Quality Score** (0-100) — composite score based on age, authority, backlinks, name quality
- **Domain Authority** (0-100) — from DataForSEO
- **Trust Flow / Citation Flow** (0-100) — from Majestic via SEObserver
- **TTF Topic** — topical category (e.g., `Business/Marketing`)
- **Referring Domains** — unique linking domains count
- **Backlinks** — total backlink count
- **PageRank** (0-10) — Open PageRank
- **Domain Age** — from Wayback Machine first snapshot
- **Language** — detected from archived content
- **Google Business** — GMB listing detection

## Response Format

Each domain includes:

```json
{
  "id": 12345,
  "name": "example.fr",
  "tld": ".fr",
  "source": "webexpire",
  "type": "auction",
  "score": 72,
  "price": 150.00,
  "max_bid": 210.00,
  "bids_count": 4,
  "auction_end_date": "2026-04-07T15:00:00+00:00",
  "age": 19,
  "domain_authority": 28,
  "trust_flow": 22,
  "citation_flow": 16,
  "ttf_topic": "Business/Marketing",
  "referring_domains": 216,
  "backlinks_count": 2000,
  "language": "FR",
  "has_gmb": false,
  "purchase_url": "https://www.webexpire.com/...",
  "purchase_platform": "WebExpire"
}
```

## REST API

CatchDoms also offers a REST API at `https://catchdoms.com/api/domains`. [Full documentation](https://catchdoms.com/api/docs).

## Links

- **Website:** [catchdoms.com](https://catchdoms.com)
- **API Docs:** [catchdoms.com/api/docs](https://catchdoms.com/api/docs)
- **Pricing:** [catchdoms.com/pricing](https://catchdoms.com/pricing)

## License

MIT
