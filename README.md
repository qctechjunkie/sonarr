# Sonarr Docker Stack

TV series collection manager that automatically searches, downloads, and organizes your TV show library.

## Quick Start

```bash
cp .env.example .env
# Edit .env to match your system
docker-compose up -d
```

Access the web interface at: http://localhost:8989

## What is Sonarr?

Sonarr is a PVR for Usenet and BitTorrent users. It can:
- **Monitor** multiple RSS feeds for new episodes and grab, sort, and rename them
- **Automatically upgrade** quality when better versions become available
- **Track** upcoming episodes and missing episodes from your collection
- **Integrate** with popular download clients and media servers

## Configuration

### Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `PUID` | 1000 | User ID for file permissions |
| `PGID` | 1000 | Group ID for file permissions |
| `TZ` | America/New_York | Timezone |
| `SONARR_PORT` | 8989 | Web interface port |

### Directory Structure

```
sonarr/
├── config/      # Sonarr configuration and database (auto-created)
├── tv/          # TV show library (auto-created)
└── downloads/   # Download client output (auto-created)
```

**Important**: The `downloads` directory should be the same path used by your download client.

## Initial Setup

### 1. Access Web Interface
Navigate to http://localhost:8989 and complete the setup wizard.

### 2. Configure Download Client
Go to **Settings → Download Clients** and add your download client:

**For qBittorrent:**
- **Name**: qBittorrent
- **Host**: `qbittorrent` (if using Docker networks) or `localhost`
- **Port**: `9865`
- **Username/Password**: As configured in qBittorrent
- **Category**: `sonarr` (optional but recommended)

### 3. Configure Indexers
Go to **Settings → Indexers** and add indexers manually, or use Prowlarr for automatic sync.

**Manual Configuration:**
- Add public/private torrent trackers
- Add usenet indexers
- Configure API keys and authentication

**Prowlarr Integration:**
- Indexers will sync automatically from Prowlarr
- No manual configuration needed

### 4. Configure Media Management
Go to **Settings → Media Management**:
- **Episode Naming**: Configure your preferred naming scheme
- **Folders**: Enable season folders for better organization
- **Importing**: Configure how Sonarr handles completed downloads

### 5. Add Root Folder
Go to **Settings → Media Management → Root Folders**:
- Add `/tv` as your root folder

## Series Management

### Adding Series
1. Go to **Series → Add New Series**
2. Search for the show you want to add
3. Configure:
   - **Root Folder**: `/tv`
   - **Quality Profile**: Choose appropriate quality
   - **Language Profile**: Select preferred language
   - **Series Type**: Standard, Daily, or Anime
   - **Season Folder**: Recommended for organization

### Monitoring Options
- **All Episodes**: Monitor all episodes (past and future)
- **Future Episodes**: Only monitor new episodes going forward  
- **Missing Episodes**: Monitor only episodes you don't have
- **Existing Episodes**: Monitor only episodes you already have

## Integration with Other Services

### qBittorrent Setup
1. In qBittorrent, create a category called `sonarr`
2. Set automatic category assignment in Sonarr download client settings
3. Ensure download path is accessible to both containers

### Prowlarr Integration
1. In Prowlarr, add Sonarr as an application
2. Use Sonarr's API key (found in Settings → General)
3. Indexers will sync automatically

### Plex/Jellyfin/Emby Integration
- Point your media server to the same `/tv` directory
- Sonarr will automatically trigger library updates after import

## Quality Profiles

Create custom quality profiles in **Settings → Profiles → Quality Profiles**:
- **HD-1080p**: For high-quality shows
- **HD-720p**: For smaller file sizes
- **SD**: For older shows or limited bandwidth

## Series Types

**Standard**: Most TV shows (weekly episodes)
**Daily**: News, talk shows (daily episodes)
**Anime**: Japanese animation (special episode numbering)

## Custom Formats

Use Custom Formats in **Settings → Custom Formats** to prefer/avoid specific releases:
- **Scene/P2P Groups**: Prefer trusted release groups
- **Streaming Services**: Target specific streaming sources
- **HDR/DV**: Target specific HDR formats

## Troubleshooting

### Episodes Not Downloading
- Check **Activity** tab for search results
- Verify series monitoring is enabled
- Check indexer connectivity in **Settings → Indexers**
- Review episode status in series details

### Files Not Importing
- Verify download client category matches Sonarr settings
- Check file permissions (PUID/PGID)
- Ensure download and TV paths are accessible
- Review **Activity → Manual Import** for stuck files

### Missing Episodes
- Check **Wanted → Missing** tab
- Verify episode air dates and availability
- Manual search for specific episodes
- Check if episodes exist on your indexers

### Season/Episode Numbering Issues
- Verify series type (Standard/Daily/Anime)
- Check TheTVDB for correct episode information
- Use manual import for problematic episodes
- Consider scene mapping for non-standard numbering

## Security Notes

- Secure your API key and don't share it publicly
- Use strong authentication for private indexers
- Keep Sonarr updated for security patches
- Consider using a VPN for additional privacy

## Backup

Important directories to backup:
- `./config` - Contains all Sonarr settings, database, and metadata

```bash
tar -czf sonarr-backup-$(date +%Y%m%d).tar.gz config/
```

## Performance Tips

- Use SSD storage for Sonarr database and temporary processing
- Configure appropriate retention policies for history
- Use quality profiles to limit unnecessary downloads
- Monitor disk space usage regularly
- Consider using season packs for completed seasons

## Updates

```bash
docker-compose pull
docker-compose up -d
docker image prune
```

## Common File Naming Schemes

**Standard**: `{Series Title} - S{season:00}E{episode:00} - {Episode Title} {Quality Full}`
- Example: `Breaking Bad - S01E01 - Pilot Bluray-1080p.mkv`

**Daily**: `{Series Title} - {Air Date} - {Episode Title} {Quality Full}`
- Example: `The Daily Show - 2023-10-15 - Guest Interview Bluray-1080p.mkv`

**Anime**: `{Series Title} - S{season:00}E{episode:00} - {Episode Title} {Quality Full}`
- Example: `Attack on Titan - S01E01 - To You, in 2000 Years Bluray-1080p.mkv`