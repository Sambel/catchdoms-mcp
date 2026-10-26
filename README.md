# CatchDoms — Expired Domains API & MCP Server

CatchDoms exposes an **Expired Domains API** and an **MCP (Model Context Protocol) server** that aggregate **22 sources**: 21 auction and marketplace platforms (Dynadot, GoDaddy, DropCatch, SnapNames, Catched, Gname, Namecheap, NameSilo, Sedo, BloomUp, UKDroplists, DomainLore, Rakko, and more) plus an aged ccTLD database of **183,537 domains** available at registration price. Authority+ adds a **weekly pending-delete inventory** of domains about to drop. Every domain is enriched with Trust Flow, Citation Flow, Domain Authority, backlinks count, referring domains, and Wayback history.

Use the **REST API** to integrate expired domain data into your tools, scripts, or pipelines. Use the **MCP server** to let Claude, Cursor, Windsurf, or any MCP-compatible AI assistant search expired domains conversationally — find drop-catches, check auction prices, and surface high-value aged domains programmatically.

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

| Feature | Free | Pro | Authority | Authority+ |
|---------|------|-----|-----------|------------|
| Domains per search | 50 | Unlimited | Unlimited | Unlimited |
| Visible domain names | 10 | All | All | All |
| Trust Flow / Citation Flow | Hidden | Yes | Yes | Yes |
| GMB detection | Hidden | Yes | Yes | Yes |
| `get_domain` tool | No | Yes | Yes | Yes |
| Aged ccTLD domains (`regfree`, 183,537) | No | No | Yes | Yes |
| `search_pending_delete_domains` tool | No | No | No | Yes |
| MCP rate limit | 3 req/min | 15 req/min | 15 req/min | 15 req/min |
| REST API rate limit | n/a | 15 req/min | 60 req/min | 60 req/min |

## Tools

### `search_domains`

Search for domains with filters. Returns up to 100 results sorted by quality.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `source` | string | Filter by platform: `dynadot`, `catched`, `dropcatch`, `godaddy`, `gname`, `snapnames`, `ukdroplists`, `subreg`, `webexpire`, `parkio`, `bloomup`, `seodomains`, `nameshift`, `namesilo`, `namecheap`, `sedo`, `nicsell`, `gnews-domains`, `backorders-domains`, `domainlore`, `rakkodomain`, `regfree` (Authority subscription required), `namepros` (brandable marketplace) |
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

### `search_pending_delete_domains`

**Authority+ only.** Search the weekly pending-delete inventory: domains about to drop, WHOIS-classified, with a predicted drop date.

| Parameter | Type | Description |
|-----------|------|-------------|
| `tld` | string | Filter by TLD (`fr`, `de`, `com`). With or without the dot. Comma-separated for multiple. |
| `whois_status` | string | Comma-separated: `pendingDelete`, `redemptionPeriod`, `serverHold`, `clientHold`, `transition`, `quarantine`. |
| `pipeline_source` | string | `oi-diff` (ccTLDs, weekly) or `czds-daily` (gTLDs, daily). |
| `week_end` | string | Snapshot week (`YYYY-MM-DD`, a Monday) or `all` for the combined view. Default `all`. |
| `drops_within_days` | integer | Only rows dropping within N days (1-60). |
| `contains` | string | Substring the domain name must contain (case-insensitive). |
| `no_hyphens` | boolean | Exclude second-level domains containing a hyphen. |
| `no_digits` | boolean | Exclude second-level domains containing a digit. |
| `include_past_drops` | boolean | Include rows whose drop date has already passed. Default false. |
| `limit` | integer | Results to return (1-100, default 50). For pagination use the REST API at `/api/pending-delete`. |

**Example prompt:**
> Which .io domains drop in the next 10 days, no hyphens, no digits?

## Domain Sources

21 auction and marketplace sources, refreshed daily:

| Source | Type |
|--------|------|
| **Dynadot** | Closeouts (fixed $5-$30) + auctions |
| **GoDaddy** | Expiring auctions + closeouts |
| **DropCatch** | Auctions (Dropped, Pre-Release) |
| **Catched** | Auctions + backorder |
| **Gname** | Auctions + buy now |
| **SnapNames** | Pre-release + closeout |
| **Namecheap** | Marketplace auctions with pre-filled Ahrefs DR, Majestic TF/CF, Estibot valuation |
| **NameSilo** | Registrar expired-domain auctions |
| **Sedo** | Expiring domains |
| **UKDroplists** | UK dropping/expired |
| **DomainLore** | UK auctions (Spotlight, Hidden Gems, SEO) |
| **Subreg** | Expiring auctions + closeouts |
| **WebExpire** | French .fr auctions |
| **BloomUp** | French .fr auctions + buy now |
| **Park.io** | Premium auctions |
| **Nicsell** | European ccTLD auctions (top-auktionen) |
| **Rakko** | Japanese expired marketplace with pre-filled DR/DA/TF/CF |
| **SEO.Domains** | Buy now with SEO metrics |
| **NameShift** | Premium buy-now / make offer |
| **Gnews Domains** | Hand-curated premium news domains, buy-now |
| **Backorders Domains** | Niche ccTLD auctions (.ie, .si, .tw, .ph, .mx…) |

### Authority: aged ccTLD database (`regfree`)

183,537 aged ccTLD domains across 84 country extensions (.fr, .de, .it, .co.uk, .nl, .id, .kz and dozens more) **available at registration price**. No auction, no bidding war. Same SEO enrichment (TF/CF/DA/backlinks/Wayback) on every entry. Use `source=regfree` from the MCP `search_domains` tool with an Authority API key.

### Authority+: weekly pending-delete inventory

205,679 actionable domains in the latest weekly batch, across 144+ ccTLDs and gTLDs, WHOIS-classified (`pendingDelete`, `redemptionPeriod`, `serverHold`, `clientHold`, `transition`, `quarantine`) with predicted drop dates 5 to 45 days out. ccTLDs come from a weekly OpenINTEL snapshot, gTLDs from a daily feed. Use the `search_pending_delete_domains` tool with an Authority+ API key.

## SEO Metrics

Every domain is enriched with:

- **Quality Score** (0-100) — composite score based on age, authority, backlinks, name quality
- **Domain Authority** (0-100) — from DataForSEO
- **Trust Flow / Citation Flow** (0-100) — from Majestic
- **TTF Topic** — topical category (e.g., `Business/Marketing`)
- **Referring Domains** — unique linking domains count
- **Backlinks** — total backlink count
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
  "auction_end_date": "2027-01-15T15:00:00+00:00",
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

CatchDoms also offers a REST API at `https://catchdoms.com/api/domains`, plus `https://catchdoms.com/api/pending-delete` for the Authority+ inventory. [Full documentation](https://catchdoms.com/docs) and an [OpenAPI spec](https://catchdoms.com/openapi.json).

## Links

- **Website:** [catchdoms.com](https://catchdoms.com)
- **API Docs:** [catchdoms.com/docs](https://catchdoms.com/docs)
- **OpenAPI spec:** [catchdoms.com/openapi.json](https://catchdoms.com/openapi.json)
- **Pricing:** [catchdoms.com/pricing](https://catchdoms.com/pricing)

## License

MIT
