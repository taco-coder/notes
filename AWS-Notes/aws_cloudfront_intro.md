# Cloud Front
Cloudfront is a content delivery network (CDN).

Edge locations act as caches for files that are stored at a different bucket in a different region to save time.

The first user gets latency to download the file from origin to cache.

TTL (time to live) sets how long the file stays cached before being deleted from cache; it's usually 24-48 hours by default (in seconds).

Web distribution type is typically used for websites.

It takes time to create a distribution (about an hour).

Cloudfront retrieves the content to be cached from S3 buckets.

An origin path can be defined if you want to use a specific folder in a bucket; leave the box empty if you want to use the whole bucket.

Under the distribution settings, you can invalidate specific files or file types so it no longer is put on the Edge Location cache if you want some objects to take priority .


