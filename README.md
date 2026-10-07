# EXIF Tools

Workflow:

- run duplicate detection with DupeGuru
- import in Darktable
- reject photos
- add geotag
- export from Darktable
- run renamings (rename_by_exif)
- create album(s) if needed in EXIF (batch_edit_exif)
- run reorganize (organize_by_exif)
- run compression (compress_file)
- move to cloud

Tools :

- Batch Edit EXIF
- Organize by EXIF
- Original Name rename ?! -OriginalFileName -DerivedFrom Preserved File Name ?!
  Date_Make-Model_NAME.jpg

## Batch Edit EXIF

A script to batch edit EXIF. Sorry help is in french.

Run `./batch_edit_exif --help`

## Copy GPS coordinates from a photo to videos

Get the photo coordinates in decimal:

```sh
exiftool -n -s3 -GPSLatitude -GPSLongitude photo.jpg
```

Videos (mp4/mov) store GPS in `GPSCoordinates` as `"lat, lon"` (signed decimal, negative for S/W):

```sh
./batch_edit_exif videos/*.mp4 --GPSCoordinates "48.8902, 2.373" --dry-run
```

Photos need `GPSLatitude`/`GPSLongitude` and their `Ref` (same signed value, exiftool deduces N/S and E/W):

```sh
./batch_edit_exif photos/*.jpg --GPSLatitude 48.8902 --GPSLatitudeRef 48.8902 --GPSLongitude -3.5 --GPSLongitudeRef -3.5 --dry-run
```

Or copy directly from the photo with exiftool (no dry-run):

```sh
exiftool -overwrite_original -tagsFromFile photo.jpg '-GPSCoordinates<GPSPosition' videos/*.mp4
```

And bonus ; erase Album value

```sh
exiftool -overwrite_original -Album= photo.jpg
```

## Organize by EXIF

A script to sort file by EXIF, inspired by [elodie](https://github.com/jmathai/elodie).

Run `./organize_by_exif --help`

## Change DateTimeOriginal based on filename

This command change the datetime of all file in the directory based on the filename (YYYYMMDD_HHMMSS\* -> YYYYMMDD HH:MM:SS)

```sh
exiftool -overwrite_original '-DateTimeOriginal<${Filename;m/^(\d{4})(\d{2})(\d{2})_(\d{2})(\d{2})(\d{2})/$1:$2:$3 $4:$5:$6/}' .
```

## Compress videos

`compress_videos` re-encodes every video of a folder (recursively) in AV1 (CPU or GPU) next to the originals with the codec in the name (`video.mov` -> `video.av1.mp4`), deletes the originals with `--delete-original`, overwrites existing outputs with `-y`, skips videos already in HEVC/AV1, keeps dates, GPS and Album. Run `./compress_videos --help` for options and quality equivalences between encoders.

```sh
./compress_videos --source ~/Videos
./compress_videos --source ~/Videos --gpu nvidia --quality-nvenc 32
```

Manual commands:

Re-encode with a modern codec at constant quality (CRF): lower CRF = better quality, bigger file. `-fps_mode passthrough` keeps the variable frame rate of phone videos (otherwise frames get duplicated). `-map_metadata 0 -movflags +use_metadata_tags` keeps dates but not GPS: copy it back with exiftool (see below).

H.265/HEVC (≈ -50% vs H.264, plays almost everywhere except Firefox), CRF 23 to 26:

```sh
ffmpeg -i input.mp4 -c:v libx265 -crf 23 -preset slow -tag:v hvc1 -c:a copy -fps_mode passthrough -map_metadata 0 -movflags +use_metadata_tags output.mp4
```

AV1 (best compression, recent devices and browsers), CRF 30 to 36:

```sh
ffmpeg -i input.mp4 -c:v libsvtav1 -crf 30 -preset 6 -c:a copy -fps_mode passthrough -map_metadata 0 -movflags +use_metadata_tags output.mp4
```

Copy GPS back from the original:

```sh
exiftool -overwrite_original -tagsFromFile input.mp4 -GPSCoordinates output.mp4
```

Compare quality with the original (SSIM, ≥ 0.98 is close to invisible). Frames are matched by index because variable frame rate timestamps don't align:

```sh
ffmpeg -i output.mp4 -i input.mp4 -lavfi "[0:v]settb=1/30,setpts=N[a];[1:v]settb=1/30,setpts=N[b];[a][b]ssim" -f null -
```

Results on a 1080p 14 Mb/s phone video (174 MB):

| Config              | Size  | Gain | SSIM  |
| ------------------- | ----- | ---- | ----- |
| H.265 CRF 23 slow   | 89 MB | -49% | 0.983 |
| H.265 CRF 26 medium | 50 MB | -72% | 0.975 |
| AV1 CRF 30          | 61 MB | -65% | 0.984 |
| AV1 CRF 36          | 38 MB | -78% | 0.980 |
