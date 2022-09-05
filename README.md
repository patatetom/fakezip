# fakezip

todo

## ZIP file format

(from [Wikipedia](https://en.wikipedia.org/wiki/ZIP_(file_format)))

**Local file header**
| Offset | Bytes | Description                                                        |
|--------|-------|--------------------------------------------------------------------|
| 0      | 4     | Local file header signature 0x04034b50 (or PK♥♦ or "PK\0x03\0x04") |
| 4      | 2     | Version needed to extract (minimum)                                |
| 6      | 2     | General purpose bit flag                                           |
| 8      | 2     | Compression method; e.g. none = 0, DEFLATE = 8 (or "\0x08\0x00")   |
| 10     | 2     | File last modification time                                        |
| 12     | 2     | File last modification date                                        |
| 14     | 4     | CRC-32 of uncompressed data                                        |
| 18     | 4     | Compressed size (or 0xffffffff for ZIP64)                          |
| 22     | 4     | Uncompressed size (or 0xffffffff for ZIP64)                        |
| 26     | 2     | File name length (n)                                               |
| 28     | 2     | Extra field length (m)                                             |
| 30     | n     | File name                                                          |
| 30+n   | m     | Extra field                                                        |
