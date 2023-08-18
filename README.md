

0. `/mnt/media` has to be writable by everyone

`sudo chmod -R 777 *`

1. Configure SABnzbd
	- Use IP insead of hostname (`127.0.0.1:8000` if running locally)
	- Add `MY_HOSTNAME, sabnzbd` to whitelist: https://sabnzbd.org/wiki/extra/hostname-check.html
	- Get API key from "General", set `SABNZBD_API_KEY` in `.env`
2. Configure Radarr
	- Settings -> Download Clients, Add SABnzbd
	- Settings -> Indexers, Add https://www.nzbgeek.info
	- Settings -> General, get API key, save
	- Settings -> Media management -> Add root folder "/movies"
3. Sonarr
	- Same as Radarr, but "/tv" as root folder
4. Jellyfin
	- Setup
	- Add media libraries `/movies`, `/tv`
	- Login, Administration -> dashboard, "Advanced, API Keys", Add Api key, save
5. Jellyseer
	- Sign in withj Jellyfin
	- Jellyfin URL: http://jellyfin:8096
	- any email addres, username, password from jellyfin
	- Sync Libraries, activate
    - Add Radarr, http://radarr, API key
    - Settings -> General API Key

