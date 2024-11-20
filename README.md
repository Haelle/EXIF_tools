# EXIF Tools

Workflow:
- import in Darktable
- reject photos
- add geotag
- export from Darktable
- run duplicate detection with DupeGuru
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
