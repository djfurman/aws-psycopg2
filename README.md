psycopg2 Python Library for AWS Lambda
======================================

This is a custom compiled psycopg2 C library for Python. Due to AWS Lambda
missing the required PostgreSQL libraries in the AMI image, we needed to
compile psycopg2 with the PostgreSQL `libpq.so` library statically linked
libpq library instead of the default dynamic link.


### Install and setup

``` pip install aws-psycopg2 ```

#### Enabling SSL Encyrption in Flight

Current AWS [RDS Combined Certificate Authority 2019](https://s3.amazonaws.com/rds-downloads/rds-combined-ca-bundle.pem) SSL (*.pem) file is now included in this package for easy implementation of `sslmode=verify-ca`.

```python
import os

import psycopg2

conn = psycopg2.connect(
  dbname='yourdb',
  user='dbuser',
  password='notReal',
  host='db.example.org',
  port=5432,
  sslmode='verify-ca',
  sslrootcert=os.path.join(
    os.path.dirname(__file__),
    'rds-combined-ca-bundle.pem'
  ),
)
```

### Source code : https://github.com/AbhimanyuHK/aws-psycopg2 

### Instructions on compiling this package from scratch

Here was the process that was used to build this package. You will need to
perform these steps if you want to build a newer version of the psycopg2
library.

1. Download the
  [PostgreSQL source code](https://ftp.postgresql.org/pub/source/v9.4.3/postgresql-9.4.3.tar.gz) and extract into a directory.
2. Download the
  [psycopg2 source code](http://initd.org/psycopg/tarballs/PSYCOPG-2-6/psycopg2-2.6.1.tar.gz) and extract into a directory.
3. Go into the PostgreSQL source directory and execute the following commands:
  - `./configure --prefix {path_to_postgresql_source} --without-readline --without-zlib`
  - `make`
  - `make install`
4. Go into the psycopg2 source directory and edit the `setup.cfg` file with the following:
  - `pg_config={path_to_postgresql_source/bin/pg_config}`
  - `static_libpq=1`
5. Execute `python setup.py build` in the psycopg2 source directory.

After the above steps have been completed you will then have a build directory
and the custom compiled psycopg2 library will be contained within it. Copy this
directory into your AWS Lambda package and you will now be able to access
PostgreSQL from within AWS Lambda using the psycopg2 library.
