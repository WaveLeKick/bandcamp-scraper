[Bandcamp Scraper](https://apify.com/marielise.dev/bandcamp-scraper?fpr=data)

Bandcamp Music Scraper is an Apify Actor that extracts comprehensive music data from any Bandcamp artist or label page. It scrapes album metadata, track listings, audio preview URLs, artist profiles, pricing information, and embedded videos. The scraper supports both standard Bandcamp subdomains (e.g., `https://artist.bandcamp.com/`) and custom domain Bandcamp sites. Built with Crawlee and Playwright for reliable browser-based extraction with automatic retries and error recovery.

## Features

- **Album metadata** -- title, artist name, artwork URL, release date, genre tags, pricing, and download availability
- **Track listings** -- individual track title, duration in seconds, track number, and direct track page URL
- **Audio preview URLs** -- MP3 128kbps streaming preview URLs from Bandcamp's CDN for each track
- **Artist/label profiles** -- biography, geographic location, profile image, genre tags, and social media links (website, Facebook, Instagram, Twitter/X, YouTube)
- **Embedded videos** -- YouTube, Vimeo, and Bandcamp video URLs found on album pages
- **Custom domain support** -- works with Bandcamp sites using custom domains, not just `*.bandcamp.com`
- **Configurable depth** -- choose exactly which data to extract to optimize run time and cost
- **Automatic retries** -- built on Crawlee with 3 automatic retries per request for reliability
- **Proxy support** -- optional Apify Proxy integration for bypassing rate limits

## What Data Can You Extract?

### Album Data

| Field | Type | Description |
| --- | --- | --- |
| title | string | Album title |
| artist | string | Artist or label name |
| url | string | Album page URL on Bandcamp |
| imageUrl | string | Album artwork image URL |
| releaseDate | string | Release or publish date |
| price | object | Price with `amount` (number) and `currency` (string) |
| downloadAvailable | boolean | Whether the album offers free or name-your-price download |
| tags | array | Genre and style tags |
| tracks | array | Track listing (when Fetch Track Listings is enabled) |
| videos | array | Embedded video URLs (when Fetch Embedded Videos is enabled) |

### Track Data

| Field | Type | Description |
| --- | --- | --- |
| title | string | Track title |
| duration | number | Duration in seconds |
| trackNumber | number | Position in the album |
| url | string | Track page URL |
| previewUrl | string | MP3 128kbps stream URL (when Fetch Audio Previews is enabled) |

### Artist/Label Data

| Field | Type | Description |
| --- | --- | --- |
| name | string | Artist or label display name |
| bio | string | Biography text |
| location | string | Geographic location |
| imageUrl | string | Profile image URL |
| tags | array | Associated genre tags |
| socialLinks | object | Website, Facebook, Instagram, Twitter/X, YouTube URLs |

## How to Use

1. Open the Bandcamp Music Scraper on Apify Store
2. Enter the Bandcamp artist or label URL (e.g., `https://wardruna.bandcamp.com/`)
3. Select which data you want to extract using the checkboxes
4. Click **Start** to run the scraper
5. Download your results in JSON, CSV, Excel, or other formats from the Dataset tab

### Input Parameters

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| url | string | (required) | Bandcamp artist or label URL |
| fetchAlbums | boolean | true | Extract album metadata from the /music page |
| fetchTracks | boolean | false | Extract track listings from each album (requires fetchAlbums) |
| fetchAudioPreviews | boolean | false | Extract MP3 preview URLs for each track (requires fetchTracks) |
| fetchArtistInfo | boolean | false | Extract artist/label profile information |
| fetchVideos | boolean | false | Extract embedded videos from album pages |
| maxAlbums | integer | 50 | Maximum number of albums to process (0 = unlimited) |
| proxyConfiguration | object | none | Optional Apify Proxy configuration |

### Example Input

```
{
    "url": "https://wardruna.bandcamp.com/",
    "fetchAlbums": true,
    "fetchTracks": true,
    "fetchArtistInfo": true,
    "maxAlbums": 10
}
```

## Output Example

Each album is saved as a separate dataset record for easy filtering and export. Here is an example of a single album record:

```
{
    "dataType": "album",
    "title": "Kvitravn",
    "artist": "Wardruna",
    "url": "https://wardruna.bandcamp.com/album/kvitravn",
    "imageUrl": "https://f4.bcbits.com/img/a3832456789_10.jpg",
    "releaseDate": "22 Jan 2021 00:00:00 GMT",
    "price": {
        "amount": 10.0,
        "currency": "USD"
    },
    "downloadAvailable": false,
    "tags": ["nordic folk", "viking", "norse", "norway"],
    "tracks": [
        {
            "title": "Synkverving",
            "duration": 320.5,
            "trackNumber": 1,
            "url": "https://wardruna.bandcamp.com/track/synkverving",
            "previewUrl": "https://t4.bcbits.com/stream/abc123/mp3-128/1234567890"
        }
    ],
    "videos": []
}
```

When artist info is enabled, a separate artist record is also included:

```
{
    "dataType": "artist",
    "name": "Wardruna",
    "bio": "Wardruna is a Norwegian musical group founded by Einar Selvik...",
    "location": "Bergen, Norway",
    "imageUrl": "https://f4.bcbits.com/img/0012345678_10.jpg",
    "tags": ["nordic folk", "ambient", "viking"],
    "socialLinks": {
        "website": "https://wardruna.com",
        "facebook": "https://facebook.com/wardruna",
        "instagram": "https://instagram.com/wardruna_official",
        "twitter": null,
        "youtube": "https://youtube.com/wardruna"
    }
}
```

## Pricing

This Actor uses **Pay Per Event** pricing. You are charged only for the data you extract:

| Event | Price |
| --- | --- |
| Album scraped | $0.01 per album |
| Artist profile scraped | $0.005 per profile |

**Example costs:**

- Scraping 10 albums from one artist: ~$0.10
- Scraping 50 albums with artist info: ~$0.505
- Basic album list (no tracks) for 20 albums: ~$0.20

There are no monthly fees. You pay only for what you use, and platform compute costs are included in the per-event price.

## Use Cases

- **Music cataloging** -- Build a database of an artist's full discography with metadata, pricing, and artwork
- **Price monitoring** -- Track album pricing changes over time for music marketplaces or comparison tools
- **Music discovery** -- Collect genre tags and related artist data for music recommendation engines
- **Playlist generation** -- Extract track listings and audio preview URLs to create sample playlists
- **Data journalism** -- Analyze pricing trends, release patterns, and genre distributions across Bandcamp artists
- **Label analysis** -- Scrape all releases from a record label to understand their catalog and pricing strategy
- **Social media research** -- Collect artist social links and bios for artist outreach or marketing campaigns

## How It Works

1. **Home page validation** -- The Actor navigates to the provided URL and verifies it is a valid Bandcamp artist or label page
2. **Artist info extraction** -- If enabled, extracts profile data from the page sidebar (name, bio, location, social links, tags)
3. **Album discovery** -- Navigates to the `/music` page and discovers all album URLs from the music grid
4. **Album processing** -- For each album (up to the maxAlbums limit), visits the album page and extracts metadata from the embedded `TralbumData` JavaScript object, which contains reliable structured data directly from Bandcamp's backend
5. **Track extraction** -- Parses track information including title, duration, track number, and MP3 preview stream URLs
6. **Video extraction** -- Scans for embedded iframe elements from YouTube, Vimeo, and Bandcamp's own video player
7. **Dataset output** -- Each album and the artist profile are pushed as separate records to the Apify Dataset for easy export

## Technical Details

- Built with **Crawlee** PlaywrightCrawler for managed browser automation with automatic retries
- Uses **Playwright** with Chromium in headless mode
- Extracts data from Bandcamp's embedded `TralbumData` JavaScript object for maximum reliability
- Falls back to DOM scraping when structured data is unavailable
- Sequential processing (maxConcurrency: 1) to respect Bandcamp's rate limits
- Configurable proxy support via Apify Proxy for geo-restricted content

## FAQ

**Q: Does this work with custom domain Bandcamp sites?**
A: Yes. The scraper accepts any valid URL that hosts a Bandcamp artist or label page, including custom domains.

**Q: Why does enabling track listings increase run time?**
A: When track listings are enabled, the Actor must visit each individual album page to extract track data from the embedded JavaScript. Without tracks, it can extract basic album info directly from the music grid page in a single request.

**Q: What happens if an album page fails to load?**
A: The Actor retries each request up to 3 times automatically. If all retries fail, it records an error entry in the dataset and continues processing the remaining albums.

**Q: Can I scrape multiple artists in one run?**
A: Currently, each run processes one artist or label URL. To scrape multiple artists, run the Actor multiple times or use Apify's scheduling and orchestration features.

**Q: What format are audio preview URLs?**
A: Preview URLs point to MP3 files encoded at 128kbps, served from Bandcamp's CDN (`t4.bcbits.com`). These are the same previews that play on the Bandcamp website.

**Q: Is there a limit on the number of albums?**
A: You can set the maxAlbums parameter (default: 50, max: 500) to control how many albums are processed. Set it to 0 for unlimited.