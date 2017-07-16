rollingdb
==============

This is a time-based rolling object database.  The purpose of the database is to store files
over time and automatically delete the oldest records when the maximum file size is reached.

Using an LMDB database is more efficient for this purpose especially for a large number of files.  
When the database reaches 100k+ files, many file systems tend to become very slow when listing and 
deleting those files.  LMDB, by comparison, stores files quickly regardless of the database size.
The files are chunked into relatively large file sizes (e.g., 1GB), so a large number of files can be 
deleted virtually instantaneously.

Each file is indexed by a UUID that contains an epoch time (in milliseconds).

This is a rolling buffer of lmdb databases that contain the images.  The archive 
is given a maximum size, and a location to store the images.  It writes database files and 
cuts them off at one gigabyte.  As time progresses and the max size is exceeded, the oldest 
database files are deleted.

LMDB is the database engine.  Files are named:
[epoch_start].mdb

key = uuid for image

epoch start is the time for the very first image in the archive

Once a database file reaches 1GB, it is no longer written to, and we start a new one.

On reads:
Request for image site-id-epoch_time.jpg
  - Parse the epoch time
  - Look for the last database that has an epoch time before the image.  open it and read the image.