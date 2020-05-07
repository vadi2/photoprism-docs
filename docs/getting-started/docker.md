# Running PhotoPrism with Docker

These instructions are for users who don't like Docker Compose for any reason and prefer pure Docker instead. If you are not
sure, try using [Docker Compose](docker-compose.md) first.

### Step 1: Start the server ###

Open a terminal and run this command after replacing `~/originals` with
the folder containing your photos:

```
docker run -d \
  --name photoprism \
  -p 2342:2342 \
  -v ~/originals:/photoprism/originals \
  photoprism/photoprism
```

This is a simplified configuration compared to our [Docker Compose](docker-compose.md) example:

* The *import* folder is not mounted so that importing is not possible
* The thumbnail *cache* and *database* will get lost when restarting as their folders are not mounted to a permanent volume

The default port 2342 and other configuration values can be changed as needed. Adding the `:ro` flag to a volume 
mounts it read only. 

!!! info
    Your image files won't be deleted, modified or moved. We might later update metadata in 
    [XMP sidecar files](https://www.adobe.com/products/xmp.html) to
    sync with Adobe Lightroom.
    A JPEG representation might be created for RAW, HEIF, TIFF, PNG, BMP and GIF images in order to render 
    thumbnails and display them in a browser. You can enable read-only mode to prevent this completely, but you will also lose the functionality.

Now open http://localhost:2342/ in a Web browser to see the user interface. The default password is "photoprism".

### Step 2: Index your library ###

Go to Library in our Web UI to start indexing or importing.
Alternatively, you can run this command in a terminal to index all files in your *originals* folder:

```
docker exec -ti photoprism photoprism index -c
```

The `-c` flag will automatically create JPEGs from other file types when needed to display them in a browser.
They will be stored in the same folder next to the original using the best possible quality. 
Converting is currently not possible in read-only mode.

!!! tip
    `index --all` will re-index existing files, for example after updates.

Photos will become visible one after another. You can watch the indexer working in the terminal, or the logs tab in Library.

### Step 3: When you're done... ###

You can stop the server and start it again using the following commands:

```
docker stop photoprism
docker start photoprism
```

To remove the container completely:

```
docker rm -f photoprism
```