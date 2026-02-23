# getmusic
## Shell Script - Downloading Music

Since I started using Arch Linux, I wanted to keep my music local and independent from paid streaming services.

A custom `yt-dlp` configuration is used to reuse common download parameters.

https://github.com/user-attachments/assets/65e4f708-59e5-4b5a-9b56-7bf13850e755

## Features  
  
- Artist tracking via CSV  
- Automatic `/releases` filtering  
- Metadata embedding  
- Archive support  
- Cron-compatible updating  
- Custom output structure

## Folder Structure

```
Music/
└── Artist1/
    ├── Singles/
    │   ├── Song1.m4a
    │   └── Song2.m4a
    ├── Album1/
    │   ├── 01 - Track1.m4a
    │   └── 02 - Track2.m4a
    └── Album2/
        ├── 01 - Track1.m4a
        └── 02 - Track2.m4a
```

## Requirements

- `yt-dlp` - https://github.com/yt-dlp/yt-dlp
- `bash`

## Usage:

The script consists of 3 main modes:

1.  `artist`
2.  `album | single | playlist`
3.  `song`

### yt-dlp Configuration

Note that i used a config for `yt-dlp` for re-purposing the same code when executing anything with the command `yt-dlp`.
```
-f "bestaudio[ext=m4a]/bestaudio" 
--embed-thumbnail 
--add-metadata
--download-archive ~/.config/yt-dlp/archive.txt
```

There are 3 URLs that are used for the download location which you can change:
```
In the update sub mode:
"$HOME/Music/$name/%(album,playlist_title)s/%(track_number,playlist_index)02d - %(title)s.%(ext)s"

In the album | single | playlist mode:
"$HOME/Music/$uploader/%(album,playlist_title)s/%(track_number,playlist_index)02d - %(title)s.%(ext)s"

In the song mode:
"$HOME/Music/$uploader/Singles/%(title)s.%(ext)s"
```

There is a file that is generated as a list for artists:
```
At the top of the script:
"$HOME/.config/yt-dlp/artists.csv"
```

### Mode - Artist:

This is the main mode that i recommend using.
When you want to save artists and download all their releases including singles and albums.
This mode can easily be used as a `cron` job, meaning you can update all the artists's releases and download new ones.

All of the artists are stored in the automatically created file at `$artists_file`.

**Sub mode  - list**
List all of the artists and their corresponding URLs.

Example: 
```
getmusic artist list
```

**Sub mode  - add**
Get a YouTube Music (recommended) URL of the artist's channel as the URL input.

After performing validation checks on the URL, add the artist's name and their URL to the `csv` file.

Example:
```
getmusic artist add <URL>
```

**Sub mode  - remove**
You can remove an artist and their corresponding URL via their index.
First you should list all of the artists and choose the index that is placed at the left.

Example:
1. List the saved artists.
```
getmusic artist list

1 | twenty one pilots | https://music.youtube.com/channel/UCBQZwaNPFfJ1gZ1fLZpAEGw/releases
 2 | Poppy | https://music.youtube.com/channel/UC8JE00xTMBOqKs7o0grFTfQ/releases
 3 | HANABIE. Official YouTube | https://music.youtube.com/channel/UC45-01kcJeBvPczl1_22KXw/releases
```

2. Remove via index.
```
getmusic artist remove 2
```
Removes Poppy.

**Sub mode  - update**
The script iterates through the list of artists, and downloads all of the releases of the URL.
Every URL is being added with `/releases` at the end when doing so, we are selecting official releases that the uploader has deemed as official releases.

Note: Some artists do not have official releases in their YouTube music channel , therefore you can download the albums and singles individually via the mode `album | single | playlist`.

This mode is useful for updating artists with new releases, and can be done via `cron` jobs.

Example:
```
getmusic artist update
```

## Mode - Album / Single / Playlist

When artists do not have official releases, it's possible to download an album or a single or a playlists with this mode.
I recommend using so via URLs that contain `playlist=...`.

Example:
```
getmusic album <URL>
```

## Mode - Song

This mode is intended for downloading a song.
A single on YouTube Music is treated as a playlist (contains `playlist=` in the URL), while an individual song URL does not.

Example: 
```
getmusic song <URL>
```

## Installation:

This is the installation for Linux systems (Educational):
1. Download the file to a folder you rather have as a script folder
2. Make it executable via : `chmod +x getmusic`
3. Add the path of the folder to the `$PATH` variable.
Example:
Since i use `bash`, I edit my Bash configuration file (`.bashrc`).
And add the path of the folder which my script exists at via:
```
export PATH="$HOME/Scripts/download-music:$PATH"
```

