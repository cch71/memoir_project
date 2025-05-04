# Memoir Media Postprocessor

This represents a serverless function that performs an analysis on media after
it has been uploaded into the MemoirUpload or other configured s3 bucket.

This function is triggered by the s3 compatible service letting it know the
bucket and object name to analyze.

When notified this function will:

- Perform a SHA-256 on the media.
- Read Object metadata from Upload bucket. Specifically check is a userid
  is associated with the upload or check mapping in config.
- Check for SHA-256 in the Metadata datastore.
  - If found gets the media id otherwise generates a media id and adds a
    new entry into Metadata datastore.
  - Media not found in Metadata will be associated with user and show up in a
    special virtual 'unsorted' folder for that user.
- If mime_type not set in Metadata, determine mime_type and store.
- Read EXIF data if any and store in Metadata.
- Read Location data if any and store in Metadata.
- Save sizes, etc in Metadata.
- Generate a thumbnail from the media and store in the MemoirThumbnail bucket.
  - Will be named based on the generated media id
  - Will be reduced to 400px on largest dimension keeping aspect ratio.
