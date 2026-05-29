# jam-docker
a default configuration running jam & postgres with docker

## Platform Support

[docker images](https://hub.docker.com/r/ffalt/jam/tags) are available as

* amd64
* arm64

## Howto

### Download

download this repo with git (or use the github download button and unzip the downloaded file)

`git clone https://github.com/ffalt/jam-docker.git jam`

navigate to the downloaded/extracted folder

`cd jam`

copy default config files (DO NOT SKIP THIS STEP)

```
cp .env.dist .env
cp storage/data/config/firststart.config.json.dist storage/data/config/firststart.config.json
```

create a folder for the database files

```
mkdir storage/db/postgres
```

### Configuration

Folder & files structure:
```
jam
├── .env                      # Jamserve settings
└── storage                   # runtime data folder
    ├── data                    # where Jam stores its data
    │   ├── config                     # where Jam read its configuration
    │   │   └── firststart.config.json # Default admin user and media source settings
    │   └ ...                          # Other Cache directories 
    ├── db                      # where the Database stores its files
    ├── logs                    # where Jam stores its log files
    └── media                   # where you can store or link your media sources
```

Most of the configuration is already done to match the docker configuration. 

Following important settings have to be changed by you:

### `.env`

Basic Environment Settings

These settings are used on startup, changes require a Docker restart.

```
# Server Domain URL (e.g. https://music.yourdomain.somewhere)
JAM_DOMAIN=http://localhost:4040

# Host address and port the container is exposed on (host-side binding only,
# the container itself always listens on 0.0.0.0:4040)
JAM_HOST=0.0.0.0
JAM_PORT=4040

# Log Level, possible values: 'error' | 'warn' | 'info' | 'debug'
JAM_LOG_LEVEL=info

# Minimum password length
JAM_MIN_PASSWORD_LENGTH=10

# Due to CORS security you MUST name all domains where login with session cookie is allowed
# https://de.wikipedia.org/wiki/Cross-Origin_Resource_Sharing
#  (background: random sites cannot access/create cookies for your domain)
JAM_ALLOWED_COOKIE_DOMAINS=http://localhost:4040

# Unique strings to sign tokens and session cookies - change these!
JAM_JWT_SECRET=keyboard cat is stomping
# Max age for a valid JWT token (e.g. '1 day', '1 month', '1 year', or 0 for no expiration)
JAM_JWT_MAXAGE=1 month
JAM_SESSION_SECRET=keyboard cat is dancing
# Max age for a valid session cookie (same format as JWT_MAXAGE)
JAM_SESSION_MAXAGE=1 month

# If true, session cookies are only available for https, NOT http
JAM_SESSION_COOKIE_SECURE=false

# Set true if you want to use a reverse proxy like nginx
JAM_SESSION_TRUST_PROXY=false

# Database name
JAM_DB_NAME=jam

# Database user name
JAM_DB_USER=jam

# Database user password
JAM_DB_PASSWORD=jam

## Third-Party API Keys (optional)

# AcoustID API key for audio fingerprinting (https://acoustid.org/)
# JAM_ACOUSTID_API_KEY=your-acoustid-api-key

# Last.fm API key for music metadata (https://www.last.fm/api)
# JAM_LASTFM_API_KEY=your-lastfm-api-key

# Enable worker threads for audio processing tasks
# JAM_USE_TASKS=true

```


### `storage/data/config/firststart.config.json`

Media and User Settings only applied once on first start

Jam in Docker expects all path entries to your media in `<location on your system>/jam/storage/media/` to be replaced with `/usr/share/media/`

e.g. `/home/user/jam/storage/media/music/Awesome Collection`

entered as `/usr/share/media/music/Awesome Collection`

These settings are copied to the database on the first run, you can change them within the Admin Section of the front-end.

```json5
/*
  Add Admin user and media folders on first start
 */
{
  /*
    Default Admin user
  */
  "adminUser": {
    "name": "admin",
    /*
      Since the default admin password is stored in clear in this file,
      you MUST change it on first login
    */
    "pass": "your admin password"
  },
  /*
    Default Media folders
    Scan strategies:
    'auto' -- try to figure it out
    'artistalbum' -- artist/album folder structure
    'compilation' -- bunch of compilation folders
    'audiobook' -- bunch of audiobook folders
   */
  "roots": [
    {"name": "Music", "path": "/usr/share/media/music", "strategy": "auto"},
    {"name": "Compilations", "path": "/usr/share/media/compilations", "strategy": "compilation"},
    {"name": "Soundtracks", "path": "path/to/soundtracks", "strategy": "compilation"},
    {"name": "Audiobooks", "path": "path/to/audiobooks", "strategy": "audiobook"}
  ]
}
```

### Build
`docker compose build`

### Start
`docker compose up -d`

### Stop
`docker compose stop`

### Logs
`docker compose logs -f jam`

### Update
```
docker compose pull
docker compose up -d
```

### Production checklist

When deploying with a real domain and HTTPS, make sure to update these settings in `.env`:

- `JAM_DOMAIN` - your public URL (e.g. `https://music.example.com`)
- `JAM_ALLOWED_COOKIE_DOMAINS` - same public URL
- `JAM_SESSION_COOKIE_SECURE=true` - required for HTTPS
- `JAM_SESSION_TRUST_PROXY=true` - required when behind a reverse proxy (e.g. nginx)
- `JAM_JWT_SECRET` and `JAM_SESSION_SECRET` - use long random strings, never the defaults
