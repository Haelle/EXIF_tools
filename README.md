# Batch Edit EXIF
A script to batch edit EXIF

Sorry help is in french.

Usage: ./batch_edit_exif '<regex_fichiers>' 'EXIF_NAME=nouvelle_valeur' [--dry-run]

Script pour modifier une métadonnée EXIF spécifique pour tous les fichiers correspondant à une regex dans le dossier courant.

Arguments :
  <regex_fichiers>    Expression régulière pour filtrer les fichiers dans le dossier actuel.
  EXIF_NAME=valeur    Nom de la variable EXIF à modifier et la nouvelle valeur à définir.

Options :
  -h, --help          Afficher cette aide et quitter.
  --dry-run           Afficher les modifications qui seraient effectuées sans modifier les fichiers.

Exemple :
  ./batch_edit_exif '.*\.jpg' 'Author=John Doe' --dry-run
  ./batch_edit_exif '.*\.jpg' 'Camera=Robot Royal 36'
  ./batch_edit_exif '.*\.jpg' 'Album=Mon Album'
  ./batch_edit_exif '.*\.jpg' 'Subject=Mon Album'

Ce script nécessite 'exiftool' pour fonctionner.
