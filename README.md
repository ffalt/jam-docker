# jam-docker
a default configuration running jam with elasticsearch with docker

## Howto

### Download

download this repo with git (or use githubs download button and unzip the downloaded file)

`git clone https://github.com/ffalt/jam-docker.git jam`

navigate to the downloaded/extracted folder

`cd jam`

copy default config files (DO NOT SKIP THIS STEP)

`cp jam.env.dist jam.env`
`cp progress.env.dist progress.env`
`cp storage/data/config/firststart.config.js.dist storage/data/config/firststart.config.js`
`cp storage/data/config/jamberry.config.js.dist storage/data/config/jamberry.config.js`

### Configuration

Folder & files structure:
```
jam
├── jam.env                   # Jamserve settings
├── postgres.env              # postgres settings
├── storage                   # runtime data folder
  ├── data                    # where Jam stores its data
  │ └── config                    # where Jam read its configuration
  │ │  ├── firststart.config.js   # Default admin user and media source settings
  │ │  └── jamberry.config.js     # Jamberry front-end settings
  │ └ ...                         # Other Cache directories 
  ├── db                      # where the Database stores its files
  ├── logs                    # where Jam stores its log files
  └── media                   # where you can store or link your media sources
```

Most of the configuration is already done to match the docker configuration. 

Following important settings have to be changed by you:

### `jam.env`

Basic Environment Settings

These settings are used on startup, changes require a Docker restart.

```

# Due to CORS security you MUST name all domains where login with session cookie is allowed
# https://de.wikipedia.org/wiki/Cross-Origin_Resource_Sharing
#  (background: random sites cannot access/create cookies for your domain)
JAM_ALLOWED_COOKIE_DOMAINS=http://localhost:4040

# An unique string for your instance to sign the session cookie (change it!)
# http://www.senchalabs.org/connect/session.html
JAM_JWT_SECRET=keyboard cat is stomping
JAM_SESSION_SECRET=keyboard cat is dancing

# If true, session cookies are only available for https, NOT http
JAM_SESSION_COOKIE_SECURE=false

# Set true if you want to use a reverse proxy like nginx
JAM_SESSION_TRUST_PROXY=false

JAM_DB_NAME=jam
JAM_DB_USER=jam
JAM_DB_PASSWORD=jam

```

### `postgres.env`

Basic Database Settings

These settings are used on startup, changes require a Docker restart.

```
POSTGRES_DB=jam         # Database name, must match JAM_DB_NAME
POSTGRES_USER=jam       # Database user, must match JAM_DB_USER
POSTGRES_PASSWORD=jam   # Databaase password, must match JAM_DB_PASSWORD

```


### `storage/data/config/firststart.config.js`

Media and User Settings only applied once on first start

Jam in Docker expects all path entries to your media in `<location on your system>/jam/data/media/` to be replaced with `/usr/share/media/`

e.g. `/Users/ffalt/projects/jam/docker/run/data/media/Awesome Collection`

entered as `/usr/share/media/music/Awesome Collection`

These settings are copied to the database on the first run, you can change them with in the Admin Section of the front-end.

```
module.exports = {
   /*
      Default Admin user
   */
   adminUser: {
      name: 'admin',
      /* 
         Since the default admin password is stored in clear in this file,
         you MUST change it on first login, in the db only salted hashes of password are stored
      */
      pass: 'admin'   
   },
   /*
      Default Media folders
      Scan strategies:
         'auto' -- try to figure it out
         'artistalbum' -- artist/album folder structure
         'compilation' -- bunch of compilation folders
         'audiobook' -- bunch of audiobook folders
   */
   roots: [
      {name: 'Music', path: '/usr/share/media/music', strategy: 'auto'},
      {name: 'Compilations', path: '/usr/share/media/compilations', strategy: 'compilation'},
      {name: 'Audiobooks', path: '/usr/share/media/audiobooks', strategy: 'audiobook'},
      {name: 'Soundtracks', path: '/usr/share/media/soundtrack', strategy: 'compilation'}
   ]
};

```

### `storage/data/config/jamberry.config.js`

Change the domain name / port you want to use.

These settings will be used by the front-end app.

```
document.jamberry_config = {
    ...
    "fixed": {
        "server": "http://localhost:4040"
    },
};
```

### Build
`docker-compose build`

### Start
`docker-compose up -d`

### Status
`docker ps`

### Stop
`docker-compose stop`
