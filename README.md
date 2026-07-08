[Bandcamp Scraper](https://apify.com/cryptosignals/bandcamp-scraper?fpr=data)

# Bandcamp Scraper

Scrape [Bandcamp](https://bandcamp.com) — the leading platform for independent music — for search results, album details with full tracklists, and artist profiles with complete discographies. No authentication required.

## What is Bandcamp?

Bandcamp is a music platform where independent artists sell directly to fans. It hosts millions of albums and tracks across every genre imaginable, from jazz and electronic to punk and ambient. Artists set their own prices (including "name your price" / free), making it a goldmine for music market research and pricing analysis.

## Features

### Search

Search Bandcamp's catalog by any query — genre, artist name, album title, mood, or keyword. Returns structured results with type classification (album, track, artist).

### Album Details

Get complete album information from any Bandcamp album URL: tracklist with durations, pricing, tags, release date, and cover art.

### Artist Info

Get artist profiles with bio, location, full discography listing, and genre tags from any Bandcamp artist URL.

## Input Schema

```
{
    "action": "search",
    "query": "jazz",
    "maxItems": 20
}
```

### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `action` | string | Yes | `search`, `album`, or `artist` |
| `query` | string | For search | Search query text |
| `url` | string | For album/artist | Bandcamp URL to scrape |
| `maxItems` | integer | No | Max search results (default: 20, max: 200) |

## Output Examples

### Search Results

```
{
    "title": "Oncle Jazz",
    "artist": "Men I Trust",
    "url": "https://menitrust.bandcamp.com/album/oncle-jazz",
    "type": "album",
    "genre": "electronic",
    "releaseDate": "September 13, 2019",
    "thumbnail": "https://f4.bcbits.com/img/a3970465110_7.jpg"
}
```

### Album Details

```
{
    "title": "Oncle Jazz",
    "artist": "Men I Trust",
    "url": "https://menitrust.bandcamp.com/album/oncle-jazz",
    "tracks": [
        {
            "position": 1,
            "title": "Oncle Jazz",
            "url": "https://menitrust.bandcamp.com/track/oncle-jazz",
            "duration": 63.2
        }
    ],
    "trackCount": 24,
    "tags": ["electronic", "indie", "dream pop", "Montreal"],
    "releaseDate": "13 Sep 2019 00:00:00 GMT",
    "price": 0.0,
    "currency": "USD",
    "thumbnail": "https://f4.bcbits.com/img/a3970465110_10.jpg",
    "description": "24 track album"
}
```

### Artist Info

```
{
    "name": "Men I Trust",
    "url": "https://menitrust.bandcamp.com",
    "location": "Montreal, Quebec",
    "bio": "Men I Trust is a Canadian band...",
    "thumbnail": "https://f4.bcbits.com/img/...",
    "albums": [
        {
            "title": "Oncle Jazz",
            "url": "https://menitrust.bandcamp.com/album/oncle-jazz",
            "thumbnail": "https://f4.bcbits.com/img/a3970465110_7.jpg"
        }
    ],
    "albumCount": 6,
    "tags": ["electronic", "indie"]
}
```

## Use Cases

### Music Market Research

Analyze pricing trends across genres. Bandcamp artists set their own prices, making it ideal for studying indie music economics. Track how many albums are free vs. paid, average price points by genre, and pricing strategies for successful artists.

```
{"action": "search", "query": "synthwave", "maxItems": 100}
```

Collect 100 synthwave albums, then analyze the price distribution. Compare against other genres to find pricing sweet spots.

### Playlist Curation and Music Discovery

Build curated playlists by searching for specific moods, genres, or subgenres. Bandcamp's tagging system is community-driven, so you can find extremely niche music.

```
{"action": "search", "query": "lo-fi ambient", "maxItems": 50}
```

Use search results to discover artists, then drill into each with the `artist` action to explore their full catalogs.

### Artist Discovery and A&R

Labels and A&R professionals can use this to discover emerging artists. Search for trending genres, then analyze artist discographies and release patterns.

```
{"action": "artist", "url": "https://artist.bandcamp.com"}
```

Get complete discography, location (for booking/touring logistics), bio, and genre tags. Cross-reference multiple artists to find scenes and clusters.

### Indie Music Pricing Analysis

Study how independent musicians price their work. Bandcamp's "name your price" model creates a unique dataset:

1. Search for a genre: `{"action": "search", "query": "jazz", "maxItems": 200}`
2. Get album details for each result to see actual prices
3. Analyze: What percentage is free? What's the median price? Do established artists charge more?

### Genre and Tag Analysis

Map the Bandcamp genre ecosystem. Artists self-tag their work, creating a folksonomy of music classification:

1. Search across genres to collect tag data
2. Use album details to get full tag lists
3. Build a tag co-occurrence graph to understand genre relationships

### Release Pattern Tracking

Monitor new releases in specific genres or from specific artists. Combine search (for genre-level monitoring) with artist scraping (for per-artist tracking) to build a release calendar.

### Music Data Enrichment

Enrich your existing music database with Bandcamp data. If you have a list of artist names or album titles, use search to find their Bandcamp presence, then get full details.

### Academic Research

Study the independent music ecosystem: geographical distribution of artists, genre evolution over time, pricing dynamics, and the relationship between tags and commercial success.

## Pricing

This actor uses pay-per-event pricing:

- **search-result**: Charged per search result returned
- **album-detail**: Charged per album scraped
- **artist-detail**: Charged per artist profile scraped

## Technical Details

- Uses `httpx` for async HTTP requests with connection pooling
- Parses JSON-LD structured data from album pages for maximum accuracy
- Falls back to HTML parsing via BeautifulSoup4 when structured data is unavailable
- Extracts `data-tralbum` and `data-band` embedded JSON from Bandcamp pages
- No authentication required — all data is from public pages
- Respects Bandcamp's server with sequential requests
- Handles pagination for search results automatically

## Limitations

- Only accesses publicly available information on Bandcamp
- Search results are limited to what Bandcamp's search returns (no API access)
- Rate limiting may apply for very large scrapes — use reasonable `maxItems` values
- Prices shown are the minimum/listed price; "name your price" items show 0.00

## FAQ

**Q: Does this require a Bandcamp account?**
A: No. All data is scraped from public HTML pages.

**Q: How fast is it?**
A: Search returns around 20 results per page load. Album and artist details take one request each. Typical runs complete in seconds.

**Q: Can I search by genre/tag?**
A: Yes — just use genre names as your search query. Bandcamp's search covers titles, artist names, tags, and descriptions.

**Q: What about "name your price" albums?**
A: These show as price 0.0 with a minPrice of 0.0 in the album details.

**Q: Can I get streaming URLs?**
A: The scraper focuses on metadata (titles, prices, tags). Streaming URLs are temporary and expire quickly.

## Getting Started

Install dependencies and run locally:

```
cd bandcamp-scraper
pip install -r requirements.txt
apify run --input '{"action": "search", "query": "jazz", "maxItems": 5}'
```

Or deploy to Apify:

```
apify login
apify push
```

## License

This project is licensed under the Apache License 2.0.

## Using proxies

Bandcamp uses Cloudflare protection that detects and blocks automated traffic patterns from datacenter IPs, returning 403 or challenge pages during bulk scraping. Residential proxies route requests through real ISP addresses that pass Cloudflare's trust checks. [ThorData](https://thordata.partnerstack.com/partner/0a0x4nzh) provides 200M+ residential IPs for reliable Bandcamp data collection at scale.