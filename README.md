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

# Batch Edit EXIF

A script to batch edit EXIF. Sorry help is in french.

Run `./batch_edit_exif --help`

# Organize by EXIF

A script to sort file by EXIF, inspired by [elodie](https://github.com/jmathai/elodie).

Run `./organize_by_exif --help`

# Change DateTimeOriginal based on filename

This command change the datetime of all file in the directory based on the filename (YYYYMMDD_HHMMSS* -> YYYYMMDD HH:MM:SS)

```
exiftool -overwrite_original '-DateTimeOriginal<${Filename;m/^(\d{4})(\d{2})(\d{2})_(\d{2})(\d{2})(\d{2})/$1:$2:$3 $4:$5:$6/}' .
```
