# fakezip

Despite the arrival of new compression formats such as 7Z, XZ or ZSTD, ZIP remains a very popular compression format.
The transfer of a ZIP file can sometimes be forbidden for security reasons by the service used, in particular with password-protected archive.
The Python script `fakezip` allows you to disguise a ZIP archive as a GIF image, while keeping its readability.
It prepend a GIF header to the archive and rewrite its central directory by modifying the registered offsets.



## Usage

```
python fakezip myzipfile
```

> Under Linux, `fakezip` can be made executable and installed in one of the folders of the `PATH`.
> In this case, the command will simply be `fakezip myzipfile`



## Examples

```
$ zip -r9 test.zip fakezip/
  adding: fakezip/ (stored 0%)
  adding: fakezip/header.gif (deflated 17%)
  adding: fakezip/ZIP.internal.layout.svg (deflated 79%)
  adding: fakezip/fakezip (deflated 57%)
```
```
$ ls -lgG test.zip 
-rw-rw-r-- 1 17117 sept.  5 20:53 test.zip

$ zip -Tv test.zip
Archive:  test.zip
    testing: fakezip/                 OK
    testing: fakezip/header.gif       OK
    testing: fakezip/ZIP.internal.layout.svg   OK
    testing: fakezip/fakezip          OK
No errors detected in compressed data of test.zip.
test of test.zip OK

$file test.zip
test.zip: Zip archive data, at least v1.0 to extract
```
```
$ fakezip test.zip
```
```
$ ls -lgG test.zip
-rw-rw-r-- 1 17181 sept.  5 20:23 test.zip

$ zip -Tv test.zip 
Archive:  test.zip
    testing: fakezip/                 OK
    testing: fakezip/header.gif       OK
    testing: fakezip/ZIP.internal.layout.svg   OK
    testing: fakezip/fakezip          OK
No errors detected in compressed data of test.zip.
test of test.zip OK

$ file test.zip
test.zip: GIF image data, version 89a, 1 x 1
```



## ZIP file format

**Local file header**
|      | Bytes | Description                                                      |
|-----:|-------|------------------------------------------------------------------|
| 0    | 4     | Local file header signature **0x04034b50** (or "PK\0x03\0x04")   |
| 4    | 2     | Version needed to extract (minimum)                              |
| 6    | 2     | General purpose bit flag                                         |
| 8    | 2     | Compression method; e.g. none = 0, DEFLATE = 8 (or "\0x08\0x00") |
| 10   | 2     | File last modification time                                      |
| 12   | 2     | File last modification date                                      |
| 14   | 4     | CRC-32 of uncompressed data                                      |
| 18   | 4     | Compressed size (or 0xffffffff for ZIP64)                        |
| 22   | 4     | Uncompressed size (or 0xffffffff for ZIP64)                      |
| 26   | 2     | File name length (n)                                             |
| 28   | 2     | Extra field length (m)                                           |
| 30   | n     | File name                                                        |
| 30+n | m     | Extra field                                                      |

**Central directory file header**
| Offset | Bytes | Description                                                              |
|--------|-------|--------------------------------------------------------------------------|
| 0      | 4     | Central directory file header signature **0x02014b50** (or PK\0x01\0x02) |
| 4      | 2     | Version made by                                                          |
| 6      | 2     | Version needed to extract (minimum)                                      |
| 8      | 2     | General purpose bit flag                                                 |
| 10     | 2     | Compression method                                                       |
| 12     | 2     | File last modification time                                              |
| 14     | 2     | File last modification date                                              |
| 16     | 4     | CRC-32 of uncompressed data                                              |
| 20     | 4     | Compressed size (or 0xffffffff for ZIP64)                                |
| 24     | 4     | Uncompressed size (or 0xffffffff for ZIP64)                              |
| 28     | 2     | File name length (n)                                                     |
| 30     | 2     | Extra field length (m)                                                   |
| 32     | 2     | File comment length (k)                                                  |
| 34     | 2     | Disk number where file starts                                            |
| 36     | 2     | Internal file attributes                                                 |
| 38     | 4     | External file attributes                                                 |
| 42     | 4     | Relative offset of local file header¹                                    |
| 46     | n     | File name                                                                |
| 46+n   | m     | Extra field                                                              |
| 46+n+m | k     | File comment                                                             |

¹ _This is the number of bytes between the start of the first disk on which the file occurs, and the start of the local file header. This allows software reading the central directory to locate the position of the file inside the ZIP file._

**End of central directory record**
| Offset | Bytes | Description                                                                                  |
|--------|-------|----------------------------------------------------------------------------------------------|
| 0      | 4     | End of central directory signature **0x06054b50** (or PK\0x05\0x06)                          |
| 4      | 2     | Number of this disk (or 0xffff for ZIP64)                                                    |
| 6      | 2     | Disk where central directory starts (or 0xffff for ZIP64)                                    |
| 8      | 2     | Number of central directory records on this disk (or 0xffff for ZIP64)                       |
| 10     | 2     | Total number of central directory records (or 0xffff for ZIP64)                              |
| 12     | 4     | Size of central directory (bytes) (or 0xffffffff for ZIP64)                                  |
| 16     | 4     | Offset of start of central directory, relative to start of archive (or 0xffffffff for ZIP64) |
| 20     | 2     | Comment length (n)                                                                           |
| 22     | n     | Comment                                                                                      |

(from [Wikipedia](https://en.wikipedia.org/wiki/ZIP_(file_format)))
