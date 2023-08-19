## Info

Based on https://github.com/AdrienPoupa/docker-compose-nas, but many changes were made:
	- Setup for Usenet, not torrents
	- Setup for LAN
	- No reverse proxy (traefik)
	- No VPN container

You can modify the setup to use torrents, by removing SABnzbd and adding qBittorrent and an indexer like Prowlarr. See https://github.com/AdrienPoupa/docker-compose-nas for the torrent setup.

## Transcoding

Jellyfin requires binded devices for hardware accelerated transcoding. See `docker-compose.yml`, `jellyfin` service. By default it is:

```
devices:
	- /dev/dri/renderD128:/dev/dri/renderD128
	- /dev/dri/card0:/dev/dri/card0
```

You might need to change them to allow hardware acceleration on your machine. See https://jellyfin.org/docs/general/administration/hardware-acceleration.html for more info.

## Instructions

1. Environment variables are in `.env`. Set most of them to your liking. `HOSTNAME` can be also an IP address. API Keys will be generated later, during the setup.

2. `DATA_ROOT` has to be writable by everyone. `sudo chmod -R 777 *` should do the trick.

3. `docker compose up` to start. Homepage is at `http://localhost`.

4. Configure SABnzbd
	- Use IP insead of hostname (`127.0.0.1:8080` if running locally) just for the setup.
	- After going through the guided setup, go to "Config" -> Special and to `host_whitelist (  )` add `..., your_hostname, sabnzbd` (comma separated). "Save Changes" and "Restart SABnzbd". You can see details at https://sabnzbd.org/wiki/extra/hostname-check.html.
	- Get API key from "Config" -> "General", set `SABNZBD_API_KEY` in `.env`.
2. Configure Radarr
	- Go to `http://HOSTNAME:7878`
	- "Settings" -> "Download Clients", add SABnzbd, `http://sabnzbd:8080`, API key from SABnzbd.
	- "Settings" -> "Indexers", add an indexer. I use https://www.nzbgeek.info, get URL and API key from there.
	- "Settings" -> "General", get API key, set `RADARR_API_KEY` in `.env`.
	- "Settings" -> "Media management" -> "Add root folder", `/movies` (will not be possible if `DATA_ROOT` is not writable by everyone)
3. Sonarr
	- Go to `http://HOSTNAME:8989`
	- Same as Radarr, but `/tv` as root folder. Save API key as `SONARR_API_KEY` in `.env`.
4. Jellyfin
	- Go to `http://HOSTNAME:8096`
	- Go through the setup, set username and password (passowrd can be empty).
	- Add media libraries `/movies`, `/tv`
	- "Login" -> "Administration" -> "Dashboard" -> "Advanced" -> "API Keys", add API Key, save to `JELLYFIN_API_KEY` in `.env`.
	- You can also configure Jellyfin to your liking, remember to set up transcoding.
5. Jellyseer
	- Go to `http://HOSTNAME:8096`
	- Sign in with Jellyfin credentials, Jellyfin URL is `http://jellyfin:8096` (use any e-mail address).
	- Sync with Libraries, activate.
    - Add Radarr, `http://radarr`, `7878`, API key from `RADARR_API_KEY`.
	- Add Sonarr, `http://sonarr`, `8989`, API key from `SONARR_API_KEY`.
    - "Settings" -> "General", save API Key as `JELLYSEER_API_KEY` in `.env`.
6. Restart Docker compose. `docker compose down && docker compose up -d`
