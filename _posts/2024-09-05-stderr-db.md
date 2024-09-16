#Stderr Challenge "2023-12-07-s"

The file has a known md5sum we so we can check we're working on the correct file
```bash
md5sum stderr_2023-12-07-s
```
> 9d4e5f2a2bf815c903a75481a4c066f7

now we know we're working on the correct file we need to know what we're working with so let's run "file" on it to see what we get 



cat stderr_2023-12-07-s | base64 -d | xz -cd | file -
/dev/stdin: SQLite 3.x database, last written using SQLite version 3040001, file counter 6, database pages 2, cookie 0x1, schema 4, UTF-8, version-valid-for 6


we need sqlite3 so
```sudo apt install sqlite3```

we need to write the database (db) to a file 
"cat stderr_2023-12-07-s | base64 -d | xz -cd > stderr_db"

and then check what's in the db with .dump

```sqlite3 stderr_db .dump | head 10``` - which gives us this - IMPORTANT NOTE if the db was 1 millions lines .dump would print all of the data to the terminal so pipe it to head 10 to only show the first 10 lines:
                                                                                          
PRAGMA foreign_keys=OFF;
BEGIN TRANSACTION;
CREATE TABLE codes ( number INTEGER NOT NULL , base64 TEXT );
INSERT INTO codes VALUES(3,'IyMjICMjICAjICAgIyAgICMgIyAgICAgIyAjICMgIyAjIyAgIyAgICMgIwo=');
INSERT INTO codes VALUES(2,'IyAjICMgICAjICAgIyAgICMgIyAgICAgIyAjICMgIyAjICMgIyAgICMgIwo=');
INSERT INTO codes VALUES(1,'IyAjICMjIyAjICAgIyAgICAjICAgICAgIyAjICAjICAjIyAgIyAgICMjIAo=');
INSERT INTO codes VALUES(5,'IyAjICMjIyAjIyMgIyMjICAjICAgICAgIyAjICAjICAjICMgIyMjICMjIAo=');
INSERT INTO codes VALUES(4,'IyAjICMgICAjICAgIyAgICMgIyAgICAgIyMjICMgIyAjICMgIyAgICMgIwo=');
COMMIT;


after many reworkings our sql query and piping looks thusly:

sqlite3 stderr_db ```SELECT base64 FROM codes ORDER BY number  asc' | base64 -d```


SUCCESS!!!


