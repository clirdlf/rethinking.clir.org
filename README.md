## Processing Files

After running the script, I just did a regex search/replace in atom with these:

    http://higherlogicdownload.s3.amazonaws.com/CLIR/UploadedImages/[0-9a-fA-F]{8}\-[0-9a-fA-F]{4}\-[0-9a-fA-F]{4}\-[0-9a-fA-F]{4}\-[0-9a-fA-F]{12}/
    https://higherlogicdownload.s3.amazonaws.com/CLIR/UploadedImages/[0-9a-fA-F]{8}\-[0-9a-fA-F]{4}\-[0-9a-fA-F]{4}\-[0-9a-fA-F]{4}\-[0-9a-fA-F]{12}/

to set to `/assets/images/`


# Notes

Removing `<font>` tags:

(<font[^>]*>)|(<\/font>)

Removing `<span>` tags:

(<span[^>]*>)|(<\/span>)
