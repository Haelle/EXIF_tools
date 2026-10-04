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

## Organize by EXIF

A script to sort file by EXIF, inspired by [elodie](https://github.com/jmathai/elodie).

Run `./organize_by_exif --help`

## Change DateTimeOriginal based on filename

This command change the datetime of all file in the directory based on the filename (YYYYMMDD_HHMMSS\* -> YYYYMMDD HH:MM:SS)

```sh
exiftool -overwrite_original '-DateTimeOriginal<${Filename;m/^(\d{4})(\d{2})(\d{2})_(\d{2})(\d{2})(\d{2})/$1:$2:$3 $4:$5:$6/}' .
```
