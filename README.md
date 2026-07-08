[Bandcamp Scraper](https://apify.com/lulzasaur/bandcamp-scraper?fpr=data)

Scrape Bandcamp, the world's largest independent music platform. Search for albums, tracks, and artists by name or genre. Extract prices, full tracklists, release dates, tags, descriptions, and cover art.

## Features

- **Search by anything** -- artist names, album titles, genres, labels, or keywords
- **4 search types** -- Albums, Tracks, Bands/Artists, or All types
- **Multiple queries** -- Run several searches in a single scrape
- **Full album details** -- Optional deep fetch for tracklists, durations, pricing, descriptions
- **Paginated results** -- Automatically follows search pagination for large result sets
- **Structured data** -- Extracts embedded JSON from `data-tralbum` attributes for maximum accuracy

## Input

| Field | Type | Default | Description |
| --- | --- | --- | --- |
| `searchQueries` | string[] | *required* | Artist names, album titles, or genres to search |
| `maxListings` | integer | `100` | Maximum total results across all queries (1-5000) |
| `searchType` | select | `album` | Search type: `album`, `track`, `band`, or `all` |
| `scrapeDetails` | boolean | `false` | Fetch each album/track page for full tracklist and details |
| `proxyConfiguration` | object |  | Optional proxy configuration |

## Output

Each result contains:

```
{
  "title": "OK Computer",
  "artist": "Radiohead",
  "artistUrl": "https://radiohead.bandcamp.com",
  "albumUrl": "https://radiohead.bandcamp.com/album/ok-computer",
  "genre": "rock",
  "tags": ["rock", "alternative", "electronic"],
  "releaseDate": "16 Jun 1997 00:00:00 GMT",
  "price": 9.99,
  "currency": "USD",
  "format": "digital",
  "trackCount": 12,
  "imageUrl": "https://f4.bcbits.com/img/a1234567890_10.jpg",
  "description": "Album description...",
  "itemType": "album",
  "url": "https://radiohead.bandcamp.com/album/ok-computer",
  "searchQuery": "radiohead",
  "scrapedAt": "2026-04-25T12:00:00.000Z"
}
```

With `scrapeDetails: true`, a `tracks` array is included:

```
{
  "tracks": [
    {
      "trackNumber": 1,
      "title": "Airbag",
      "duration": 281,
      "durationFormatted": "4:41"
    },
    {
      "trackNumber": 2,
      "title": "Paranoid Android",
      "duration": 383,
      "durationFormatted": "6:23"
    }
  ],
  "trackCount": 12
}
```

## Example Inputs

### Search for a specific artist's albums

```
{
  "searchQueries": ["radiohead"],
  "searchType": "album",
  "maxListings": 20
}
```

### Search multiple artists with full details

```
{
  "searchQueries": ["tame impala", "king gizzard", "black midi"],
  "searchType": "album",
  "scrapeDetails": true,
  "maxListings": 50
}
```

### Search for tracks by genre keyword

```
{
  "searchQueries": ["shoegaze", "dream pop", "post-punk"],
  "searchType": "track",
  "maxListings": 200
}
```

### Search for bands/labels

```
{
  "searchQueries": ["sub pop", "merge records"],
  "searchType": "band",
  "maxListings": 30
}
```

### Search all content types

```
{
  "searchQueries": ["ambient electronic"],
  "searchType": "all",
  "scrapeDetails": true,
  "maxListings": 100
}
```

## How It Works

1. Sends search queries to `bandcamp.com/search` with the specified `item_type` filter
2. Parses SSR HTML search results (`.searchresult` elements) for basic album/track/artist data
3. Automatically follows pagination links to get more results
4. If `scrapeDetails` is enabled, visits each album/track page and extracts the embedded `data-tralbum` JSON for full tracklists, precise pricing, release dates, descriptions, and tags
5. Results are pushed to the dataset with PPE charging

## Data Sources

- **Search results**: Server-side rendered HTML with `.searchresult` items
- **Album/track pages**: `data-tralbum` attribute containing complete album data as JSON
- **Fallbacks**: Meta tags (`og:description`, `datePublished`), inline `TralbumData` script, CSS selectors

## Cost

This actor uses pay-per-event pricing at **$0.005 per result**. A typical run of 100 albums costs approximately $0.50. With `scrapeDetails` enabled, runs take longer but cost the same per result.