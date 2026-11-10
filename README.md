# CatchDoms Expired Domains API & MCP Server

CatchDoms exposes an **Expired Domains API** and an **MCP (Model Context Protocol) server** that aggregate **27 sources**: 26 auction and marketplace platforms (Dynadot, GoDaddy, DropCatch, SnapNames, Catched, Gname, Namecheap, NameSilo, Porkbun, Sedo, BloomUp, UKDroplists, DomainLore, Rakko, and the official CZ.NIC, WHC.ca, Internet.ee and Aftermarket.pl registries) plus an aged ccTLD database of **180k+ domains** available at registration price. Authority+ adds a **weekly pending-delete inventory** of domains about to drop. Every domain is enriched with Trust Flow, Citation Flow, Domain Authority, backlinks count, referring domains, and Wayback history.

Use the **REST API** to integrate expired domain data into your tools, scripts, or pipelines. Use the **MCP server** to let Claude, Cursor, Windsurf, or any MCP-compatible AI assistant search expired domains conversationally: find drop-catches, check auction prices, and surface high-value aged domains programmatically.

## Quick Start (Free, no API key needed)

Try it instantly, no signup required:

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
| Aged ccTLD domains (`regfree`, 180k+) | No | No | Yes | Yes |
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

Get available filter options with counts: sources, TLDs, and languages.

*No parameters.*

### `get_stats`

Get platform statistics: total domains, new today, by source, SEO coverage.

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

26 auction and marketplace sources, refreshed daily. Row counts are live measurements taken in production on 2026-11-10:

| Source | `source=` | Type | Listings |
|--------|-----------|------|----------|
| **Dynadot** | `dynadot` | Closeouts (fixed $5-$30) + auctions | 5,234 |
| **GoDaddy** | `godaddy` | Expiring auctions + closeouts | 13,032 |
| **DropCatch** | `dropcatch` | Auctions (Dropped, Pre-Release) | 19,494 |
| **Catched** | `catched` | Auctions + backorder | 496 |
| **Gname** | `gname` | Auctions + buy now | 6,555 |
| **SnapNames** | `snapnames` | Pre-release + closeout | 20,137 |
| **Namecheap** | `namecheap` | Marketplace auctions with pre-filled Ahrefs DR, Majestic TF/CF, Estibot valuation | 146,344 |
| **NameSilo** | `namesilo` | Registrar expired-domain auctions | 1,723 |
| **Porkbun** | `porkbun` | Fixed-price closeouts, no bidding, price descends on a schedule | 14,109 |
| **Sedo** | `sedo` | Expiring domains | 1,188 |
| **UKDroplists** | `ukdroplists` | UK dropping/expired | 37 |
| **DomainLore** | `domainlore` | UK auctions (Spotlight, Hidden Gems, SEO) | 168 |
| **Subreg** | `subreg` | Expiring auctions + closeouts | 600 |
| **WebExpire** | `webexpire` | French .fr auctions | 44 |
| **BloomUp** | `bloomup` | French .fr auctions + buy now | 619 |
| **Park.io** | `parkio` | Premium auctions | 30 |
| **Nicsell** | `nicsell` | European ccTLD auctions (top-auktionen) | 283 |
| **Rakko** | `rakkodomain` | Japanese expired marketplace with pre-filled DR/DA/TF/CF | 1,897 |
| **SEO.Domains** | `seodomains` | Buy now with SEO metrics | 116,449 |
| **NameShift** | `nameshift` | Premium buy-now / make offer | 213,004 |
| **Gnews Domains** | `gnews-domains` | Hand-curated premium news domains, buy-now | 127 |
| **Backorders Domains** | `backorders-domains` | Niche ccTLD auctions (.ie, .si, .tw, .ph, .mx…) | 53 |
| **CZ.NIC** | `cznic` | Official .cz registry auctions on expired domains. No price, no bids, no SEO in the feed | 5,611 |
| **WHC.ca** | `whc` | Canadian .ca domains dropping through CIRA's TBR process, weekly wave, CAD | 8,506 |
| **Internet.ee** | `internet-ee` | Official .ee registry, sealed-bid: no price or bid count is published anywhere | 57 |
| **Aftermarket.pl** | `aftermarket-pl` | Polish marketplace .pl auctions only, not its 468k buy-now listings, PLN | 4,858 |

### Authority: aged ccTLD database (`regfree`)

183,214 aged ccTLD domains across 106 country extensions (.fr, .de, .it, .co.uk, .nl, .id, .kz and dozens more) **available at registration price**. No auction, no bidding war. Same SEO enrichment (TF/CF/DA/backlinks/Wayback) on every entry. Use `source=regfree` from the MCP `search_domains` tool with an Authority API key. The server requires at least one narrowing filter alongside it, **with a non-zero value** (`tld`, `score_min`, `contains`, `language`, `age_min`, `da_min`, `rd_min`, `tf_min`, `cf_min`, `categories`, `snapshots_min`, `has_backlinks`, `has_edu_gov`, `has_gmb`): `score_min=0` or `da_min=0` does not count. The result set is capped at 5,000 rows.

### Authority+: weekly pending-delete inventory

437,634 domains carry a predicted drop date still ahead, across 143 ccTLDs and gTLDs, refreshed weekly (175,636 rows in the batch of 2026-11-09). 866,049 more are classified but have no computable date yet, and the default view returns both, 1,303,683 rows in all; rows whose predicted date has already passed are hidden unless you ask for them. 12 weekly snapshots are retained, 2,142,061 rows in total. Every row is WHOIS-classified (`pendingDelete`, `redemptionPeriod`, `serverHold`, `clientHold`, `transition`, `quarantine`), and the predicted dates that exist fall 5 to 45 days out. ccTLDs come from a weekly OpenINTEL snapshot, gTLDs from a daily feed. Use the `search_pending_delete_domains` tool with an Authority+ API key.

## SEO Metrics

Every domain is enriched with:

- **Quality Score** (0-100): composite score based on age, authority, backlinks, name quality
- **Domain Authority** (0-100): from DataForSEO
- **Trust Flow / Citation Flow** (0-100): from Majestic
- **TTF Topic**: topical category (e.g., `Business/Marketing`)
- **Referring Domains**: unique linking domains count
- **Backlinks**: total backlink count
- **Domain Age**: from Wayback Machine first snapshot
- **Language**: detected from archived content
- **Google Business**: GMB listing detection

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
