# s3funnel

This project is a clone of <https://github.com/shazow>'s s3funnel from google code: <http://code.google.com/p/s3funnel/>. s3funnel is a multithreaded command line tool for Amazon's Simple Storage Service (S3).

- Written in Python, easy_install the package to install as an egg.
- Supports multithreaded operations for large volumes. Put, get, or delete many items concurrently, using a fixed-size pool of threads.
- Built on workerpool for multithreading and boto for access to the Amazon S3 API.
- Unix-friendly input and output. Pipe things in, out, and all around.

## Usage
    Usage: s3funnel BUCKET OPERATION [OPTIONS] [FILE]...
    
    s3funnel is a multithreaded tool for performing operations on Amazon's S3.
    
    Key Operations:
        DELETE Delete key from the bucket
        GET    Get key from the bucket
        PUT    Put file into the bucket (key is the basename of the path)
        COPY   Copy keys to the bucket from another bucket.
    
    Bucket Operations:
        CREATE Create a new bucket
        DROP   Delete an existing bucket (must be empty)
        LIST   List keys in the bucket. If no bucket is given, buckets will be listed.
    
    
    Options:
      -h, --help            show this help message and exit
      -a AWS_KEY, --aws_key=AWS_KEY
                            Overrides AWS_ACCESS_KEY_ID environment variable
      -s AWS_SECRET_KEY, --aws_secret_key=AWS_SECRET_KEY
                            Overrides AWS_SECRET_ACCESS_KEY environment variable
      -t N, --threads=N     Number of threads to use [default: 1]
      -T SECONDS, --timeout=SECONDS
                            Socket timeout time, 0 is never [default: 0]
      --insecure            Don't use secure (https) connection
      --list-marker=KEY     (`list` only) Start key for list operation
      --list-prefix=STRING  (`list` only) Limit results to a specific prefix
      --list-delimiter=CHAR
                            (`list` only) Treat value as a delimiter for
                            hierarchical listing
      --put-acl=ACL         (`put` only) Set the ACL permission for each file
                            [default: public-read]
      --put-full-path       (`put` only) Use the full given path as the key name,
                            instead of just the basename
      --put-only-new        (`put` only) Only PUT keys which don't already exist
                            in the bucket with the same md5 digest
      --put-header=HEADERS  (`put` only) Add the specified header to the request
      --add-prefix=ADD_PREFIX
                            (`put` and `copy` only) Add specified prefix to keys
                            in destination bucket
      --del-prefix=DEL_PREFIX
                            (`put` and `copy` only) Delete specified prefix from
                            keys in destination bucket
      --source-bucket=SOURCE_BUCKET
                            (`copy` only) Source bucket for files
      --no-ignore-s3fs-dirs
                            (`get` only) Don't ignore s3fs directory objects
      -i FILE, --input=FILE
                            Read one file per line from a FILE manifest
      -v, --verbose         Enable verbose output. Use twice to enable debug
                            output
      --version             Output version information and exit
      --batch-delete=N      (`delete` only) Delete in batches of N
      
## Examples
Note: Appending the -v flag will print useful progress information to stderr. Great for learning the tool.

### Create a bucket
    $ s3funnel mybukkit create
### List existing buckets
    $ s3funnel list
    mybukkit
### Put files in a bucket
    $ touch 1 2 3
    $ s3funnel mybukkit put 1 2 3
### List files in a bucket
    $ s3funnel mybukkit list
### Copy files from a bucket
    $ rm 1 2 3
    $ s3funnel mybukkit get 1 2 3 --threads=2
    $ ls -1
### Copy files from another bucket 
    $ s3funnel mybukkit_copy create
    $ s3funnel mybukkit list | s3funnel mybukkit_copy copy --source-bucket mybukkit --threads=2 
### Empty a bucket
    $ s3funnel mybukkit list | s3funnel mybukkit delete 
    $ s3funnel mybukkit_copy list | s3funnel mybukkit_copy delete --threads=2
### Emtpty a bucket using batch delete
    $ s3funnel mybukkit list | s3funnel mybukkit_copy delete --threads=10 --batch-delete=1000
### Delete a bucket 
    $ s3funnel mybukkit drop
    $ s3funnel mybukkit_copy drop
