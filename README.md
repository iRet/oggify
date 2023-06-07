# oggify
Download Spotify tracks to Ogg Vorbis (with a premium account).

This program uses [librespot](https://github.com/librespot-org/librespot),
and as such, requires a Spotify Premium account to use.
It supports downloading single tracks and episodes, but also entire playlists, albums and shows.

## Usage
To download a number of links as `<artist(s)> - <title>.ogg`, run
```
oggify "spotify-premium-user" "spotify-premium-password" < link_list
```
Oggify reads from standard input and looks for a URL or URI in each line,
and checks whether it is a valid Spotify media link. If it is not valid, it will be ignored.

The two formats are those you get with the menu items
"Share → Copy <Media> Link" or "Share → Copy <Media> URI" in the Spotify client,
for example
`open.spotify.com/track/1xPQDRSXDN5QJWm7qHg5Ku`
or
`spotify:track:1xPQDRSXDN5QJWm7qHg5Ku`.

Once you close the standard input or write `"done"` into it,
it will start downloading all tracks and episodes in order of input
into your current working directory.

### Helper script
A second form of invocation of oggify is
```
oggify "spotify-premium-user" "spotify-premium-password" "helper_script" < link_list
```
In this form `helper_script` is invoked for each new track:
```
helper_script "spotify_id" "title" <album> "cover_art_url" "artist1" ["artist2"...] < ogg_stream
```
The script `tag_ogg` in the source tree can be used to automatically add the track information (spotify ID, title, album, artists) as vorbis comments.

The script uses [ogg-image-blobber](https://github.com/twopoint71/ogg-image-blobber) for cover art encoding.

### Converting to MP3 (🤮)
**Don't do that, please.** You will just lose quality. If you want to do it anyway:

Use `oggify` with the `tag_ogg` helper script as described above, then convert with ffmpeg:
```
for ogg in *.ogg; do
	ffmpeg -i "$ogg" -map_metadata 0:s:0 -id3v2_version 3 -codec:a libmp3lame -qscale:a 2 "$(basename "$ogg" .ogg).mp3"
done
```
