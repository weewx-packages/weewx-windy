# Windy station uploader for WeeWX

Send archive weather observations to the Windy.com station API v2 over HTTPS.
This is the station observation service at Windy.com.

This repository mirrors [the upstream source](https://github.com/Jterrettaz/weewx-windy). The maintained
catalog archive is **0.8.post1**, which omits absent sensor values from uploads. Install that fixed archive below;
the mirrored default branch can differ from its repaired payload.

The catalog archive targets WeeWX `>=5.5.2,<6` and Python `>=3.10,<3.15`.

## Install

Use `weectl` from the Python environment running WeeWX. With stock WeeWX 5,
install the published archive:

```sh
weectl extension install https://github.com/weewx-packages/catalog/releases/download/windy/v0.8.post1/windy-0.8.post1.tar.gz --config /path/to/weewx.conf
```

Replace `/path/to/weewx.conf` with your station configuration. You can also
download that URL and pass the local archive to the same command. Use elevated
permissions only when your station files require them.

## Configure station credentials

Register your station at [stations.windy.com](https://stations.windy.com/) and
obtain its station ID and station password. Use those credentials according to
[the Windy station API](https://stations.windy.com/api-reference). No additional
Python packages are required.

```ini
[StdRESTful]
    [[Windy]]
        station_id = YOUR_STATION_ID
        station_password = YOUR_STATION_PASSWORD
```

The installer registers `user.windy.Windy`. Restart WeeWX yourself and check
its log after the next archive record. The destination is
`https://stations.windy.com/api/v2/observation/update`.

## Data and limits

The mapping sends Unix time, Fahrenheit temperature and dewpoint, mph wind and
gust, compass direction, humidity, inHg pressure, hourly rain in inches, UV and
solar radiation when available. The repaired catalog archive omits `None`
values. The mirrored source retains the earlier behavior.

`binding` selects the database binding used for archive augmentation and
defaults to `wx_binding`; it does not switch this uploader to LOOP events.
Uploads occur on archive records. The debug message in `format_url` includes
the station password, so keep debug logs private. The standalone module entry
point sends a sample and is not a configuration check. Verify station access
and accepted observations in your own Windy account.

Copyright 2019-2020 Matthew Wall and 2026 Jacques Terrettaz. Jacques Terrettaz
adapted the uploader for API v2. [GNU GPLv3](license).
