# jam-docker
a default configuration running jam with elasticsearch with docker

## Howto

### Download

download this repo with git (or use githubs download button and unzip the downloaded file)

`git clone https://github.com/ffalt/jam-docker.git jam`

navigate to the downloaded/extracted folder

`cd jam`

copy to the default folder to data (DO NOT SKIP THIS STEP)

`cp -r default-config data/config`

### Configuration

Folder & files structure:
```
jam
├── data
  ├── config                  # where Jam read its configuration
  │ ├── config.js              # Jamserve settings
  │ ├── firststart.config.js   # Default admin user and media source settings
  │ └── jamberry.config.js     # Jamberry front-end settings
  ├── data                    # where Jam stores its data
  ├── db                      # where Elasticsearch stores its data
  ├── logs                    # where Jam stores its log files
  └── media                   # where you can store or link your media sources
```

Most of the configuration is already done to match the docker configuration. 

Following important settings have to be changed by you:

in `jam/data/config/jamberry.config.js`

Change the domain name you want to use

```
document.jamberry_config = {
    ...
    "fixed": {
        "server": "http://localhost:4040"
    },
};
```

in `jam/data/config/config.js`

* Change the cookie domain name you want to use
* Change the cookie https/http setting
* Change cookie & jwt secrets 

```
module.exports = {
   server: {
      ...
      session: {
         ...
         /*
            Due to CORS security you MUST name all domains where login with session cookie is allowed
            https://de.wikipedia.org/wiki/Cross-Origin_Resource_Sharing
            (background: random sites cannot access/create cookies for your domain)
         */
         allowedCookieDomains: ['http://localhost:4040', 'http://0.0.0.0:4040'],
         /*
            An unique string for your instance to sign the session cookie (change it!)
            http://www.senchalabs.org/connect/session.html
         */
         secret: 'keyboard cat is dancing',
         cookie: {
            /*
               If true, session cookies are only available for https, NOT http
            */
            secure: false,
            ...
         }
      }
      jwt: {
         /*
            An unique string for your instance to sign the token (change it!)
         */
         secret: 'keyboard cat is stomping',
         ...
      }
   }
}

```

in `jam/data/config/firststart.config.js`

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

Jam is wired in Docker to expect all path entries to your media in `<location on your system>/jam/data/media/` to be replaced with `/usr/share/media/`

e.g. `/Users/ffalt/projects/jam/docker/run/data/media/Awesome Collection`

entered as `{name: 'Awesome Collection', path: '/usr/share/media/music/Awesome Collection', strategy: 'auto'},`

These settings are copied to the database on the first run, you can change them with in the Admin Section of the front-end.

### Build
`docker-compose build`

### Start
`docker-compose up -d`

### Status
`docker ps`

### Stop
`docker-compose stop`
