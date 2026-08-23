
Based on sqlite 3530300

To build the static binary (libsqlite3.a):

- Download amalgation: (eg. sqlite-amalgamation-3530300.zip)
- Unzip

Build with
```sh
gcc -DSQLITE_DEFAULT_MEMSTATUS=0 -DSQLITE_LIKE_DOESNT_MATCH_BLOBS -DSQLITE_MAX_EXPR_DEPTH=0 -DSQLITE_OMIT_DEPRECATED -DSQLITE_OMIT_PROGRESS_CALLBACK -DSQLITE_OMIT_SHARED_CACHE -DSQLITE_THREADSAFE=0  -O3 -DNDEBUG   -w -MD -MT sqlite3.c.o -MF sqlite3.c.o.d -o sqlite3.c.o -c sqlite3.c
ar qc libsqlite3.a sqlite3.c.o
ranlib libsqlite3.a
```

copy libsqlite3.a in same dir as sqlite3.c2i

