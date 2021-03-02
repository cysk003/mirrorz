# MirrorZ

Your next MirrorS is not MirrorS, nor MirrorSes, it's MirrorZ.

A final site for Mirror sites.

## Demo

[https://mirrorz.org](https://mirrorz.org). Currently `mirrorz.json` for each Mirror site is real-time or statically generated by their maintainers or by a third party.

Also legacy webpages (for w3m and noscript users) are provided in [https://mirrorz.org/\_/](https://mirrorz.org/_/)

A speed test script [oh-my-mirrorz.py](https://mirrorz.org/oh-my-mirrorz.py) is also provided. See [README](scripts/oh-my-mirrorz/README.md) to more info.

## Intro

Mirror sites are heterogeneous. It is hard for a single mirror to provide all mirrors, so differences occur.

For end users, this is not a good experience as they need to search for available mirrors.

To make things easy, MirrorZ is intended to include all mirrorS, so a unified interface is needed.

## Data Format v1.4 (draft)

A `mirrorz.json` in the following format describes all the data of one mirror site in MirrorZ.

```json
{
  "version": 1.4,
  "site": {
    "url": "https://example.org",
    "logo": "https://example.org/img/logo.svg",
    "logo_darkmode": "https://example.org/img/logo-white.svg",
    "abbr": "EXAMPLE",
    "name": "样例镜像站",
    "homepage": "https://blog.example.org",
    "issue": "https://github.com/example/issues",
    "request": "https://github.com/example/mirror-request",
    "email": "admin@example.com",
    "group": "QQ: 10086 and/or Telegram @something",
    "disk": "may be any string showing usage of disk, e.g. usage",
    "note": "may be any string; like speed limit or connection limit",
    "big": "/speedtest/1000mb.bin"
  },
  "info": [
    {
      "distro": "Debian",
      "category": "os",
            "urls": [
                {
                    "name": "10.7.0 (amd64, CD installer with xfce)",
                    "url": "/debian-cd/current/amd64/iso-cd/debian-10.7.0-amd64-xfce-CD-1.iso"
                }
            ]
    }
  ],
  "mirrors": [
    {
      "cname": "AOSP",
      "desc": "Android 操作系统源代码",
      "url": "/AOSP",
      "status": "S",
      "help": "/help/AOSP/",
      "upstream": "https://android.googlesource.com/mirror/manifest",
      "size": "596G"
    },
    {
      "cname": "AUR",
      "desc": "Arch Linux 用户软件库",
      "url": "https://aur.tuna.tsinghua.edu.cn",
      "status": "S1612195849X1612196849N",
      "help": "/help/AUR/",
      "upstream": "https://aur.archlinux.org/"
    }
  ]
}
```

### Notes

* `version` is optional for version 1.x
* previous versions of this protocol could be found in git history; protocols in v1.x are back-ward compatible
* `site` provides the global info about one mirror site
* only `site.url` and `site.abbr` are mandatory
* `site.logo` should not be of format `ico`. Also, at least 64x64 resolution is required
* `site.logo_darkmode` is used when browser uses dark mode. Note that it should be set only after `site.logo` is set
* `site.url` should not end with slash `/`
* `site.big` should be a valid url to a big file, used by `oh-my-mirrorz.py` for speed testing
* `info` is used for category view
* the name of `info.distro` should be agreed and have a mapping, maintained in `cname.json`
* `mirrors` is used for list view
* the name of `mirrors.cname` should be agreed and have a mapping, maintained in `cname.json`
* `mirrors.desc` may differ for each mirror site since there are `excludes` for some mirror site
* `mirrors.desc` may be empty
* if `mirrors.url` begins with a slash `/`, it should be appended to `site.url` to form a full url
* `mirrors.status` is a concat of strings of pattern `[A-Z](\d+)?`. Only one main status is allowed; the number of auxiliary status is not limited.
  - `S1600000000`: successful. (optional) last successful ended unix timestamp
  - `Y1600000000`: syncing. (optional) start to sync unix timestamp
  - `F1600000000`: failed. (optional) last attempt to sync unix timestamp
  - `P1600000000`: paused. (optional) the unix timetamp sync stopped
  - `U`: unknown
  - `X1600000000`: (auxiliary) next scheduled sync unix timestamp
  - `N1600000000`: (auxiliary) new mirror. (optional) unix timestamp the repo added
  - `O1600000000`: (auxiliary) old successful timestamp, used only when it is syncing or failed
* `mirrors.help` may be empty, or the same rule as `mirrors.url`
* `mirrors.upstream`, `mirrors.size` may be empty

## MirrorZ, Mirror site, Third party

MirrorZ only provides the data format (including `cname.json`), frontend and related scripts (e.g `oh-my-mirrorz.py`). Audit on data format, content (e.g. `cname.json`) and adopted technologies (e.g. using reverse proxy in parsers) can be done by MirrorZ. Monitor and Backend are exprimental features of MirrorZ, and MirrorZ provides no warranty on these services.

The term 'Mirror site' refers to an actual mirror site and/or the corresponding organization.

The term 'Third party' refers to individuals/organizations that contribute to this project. This may overlap with 'Mirror site'.

Data like `mirrorz.json` or `site.json` and parsers in `src/parser` are contributed/maintained by some Third party, and can be placed in this repo for convenience. More specifically, to make parser/data usable by MirrorZ (e.g. data format, CORS policy) is the responsibility of one Third party; also it is optional for one Third party to support some feature like legacy page and oh-my-mirrorz.

## Contributing and Developing

### Third party

For one Third party, it should either add and maintain their url to a mirrorz json file in `src/config/upstream.js` and `src/config/mirrors.js` (legacy page and script support), in the url `https` is needed, and the url should be widely accessible (not limited to campus) as all users of `mirrorz.org` would request that url; or integrate their frontend parser in `src/parser` and `src/Root.jsx`, where the generated `mirrorz.json` is called dynamic `mirrorz.json`, and may generate a static `mirrorz.json` by [debug interface](https://mirrorz.org/debug) in `/static/json/legacy/` and add the corresponding url in `src/config/mirrors.js` for legacy page and script support.

For Third party providing an url to a mirrorz json file, it may also contribute their `mirrorz.json` generating scripts in the directory `scripts`.

Big changes by Third party should be made through PR like adding a new parser/generating script. Small changes like changing content in `site.json` or generating scripts can be done by pushing to master.

### Frontend

One may use

```
yarn --frozen-lockfile
yarn start
```

to start a local server.

To use static files, 404 redirection (emulate Github pages) and legacy pages, one may use

```
yarn --frozen-lockfile
yarn full_start
```

to start a local server. Python3 is required in this case.

### Deployment

For dynamic web page part, only

```
yarn build
```

is required.

However if one also wants to deploy the static web page, one may use

```
yarn build
yarn legacy_build
```

Note that `legacy_build` has dependencies on files `yarn build` has made.

### Misc

Currently `*.json` is ignored in `.gitignore` (not ignored in `/static`). If one wants to commit a json file, they should use `git add -f`.

<!--
 vim: ts=2 sts=2 sw=2
-->
