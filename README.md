# fakezip

todo

## ZIP file format

(from [Wikipedia](https://en.wikipedia.org/wiki/ZIP_(file_format)))

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
