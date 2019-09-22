Riddler's Notes #2
==================

**Solution author:** https://twitter.com/s_persianov

**Description:** We intercepted some nettwork traffic which leads us to the Riddler. 
Looks like he likes browsing a lot. Will you help us find the hidden message?

This challenge is about traffic analysis, more specifically a .pcap file.

There is some junk in the provided .pcap file, however after analyzing it several 
ICMP packets come up. These are really huge ICMP packets (~1K).

Let's have a closer look.

Wireshark filter: `icmp and ip.src==192.168.122.22`

One of the payloads is:

```bash
/9j/4AAQSkZJRgABAQEASABIAAD/2wBDABALDA4MChAODQ4SERATGCgaGBYWGDEjJR0oOjM9PDkzODdASFxOQERXRTc4UG1RV19iZ2hnPk1xeXBkeFxlZ2P/2wBDARESEhgVGC8aGi9jQjhCY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2P/wgARCAAUABQDAREAAhEBAxEB/8QAGAABAAMBAAAAAAAAAAAAAAAAAAIDBAX/xAAXAQEBAQEAAAAAAAAAAAAAAAAAAgED/9oADAMBAAIQAxAAAAHgYnk11TGiIz9LAH//xAAaEAABBQEAAAAAAAAAAAAAAAACAAEDEBIg/9oACAEBAAEFAkY5uVuf/8QAGREAAQUAAAAAAAAAAAAAAAAAAQACEBEg/9oACAEDAQE/AUDctz//xAAaEQABBQEAAAAAAAAAAAAAAAACAAEQESBB/9oACAECAQE/AUL3Idz/AP/EABsQAAEEAwAAAAAAAAAAAAAAABEAASExAhAg/9oACAEBAAY/Ak0ksd4y1C+f/8QAGxABAQEAAgMAAAAAAAAAAAAAAREhABAgQWH/2gAIAQEAAT8hNdZ94LiHoToKzmDrCoHfH//aAAwDAQACAAMAAAAQVGoAH//EABkRAAEFAAAAAAAAAAAAAAAAAAEAECAhMf/aAAgBAwEBPxAvDSLY/wD/xAAbEQEBAAIDAQAAAAAAAAAAAAABEQAhECAxQf/aAAgBAgEBPxBYaxS0kZwsLj2I+vx6/wD/xAAdEAEAAQMFAAAAAAAAAAAAAAABEQAhQRAggZGh/9oACAEBAAE/ECCAVuiYojYALk5dGMQKwSgdtWoGeYCcD7t//9k=
```

This looks to be base64 encoding, which is used very often in web requests to replace unwanted characters. We can decode
it and save to a file.

```bash
$ echo "/9j/4AAQSkZJRgABAQEASABIAAD/2wBDABALDA4MChAODQ4SERATGCgaGBYWGDEjJR0oOjM9PDkzODdASFxOQERXRTc4UG1RV19iZ2hnPk1xeXBkeFxlZ2P/2wBDARESEhgVGC8aGi9jQjhCY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2NjY2P/wgARCAAUABQDAREAAhEBAxEB/8QAGAABAAMBAAAAAAAAAAAAAAAAAAIDBAX/xAAXAQEBAQEAAAAAAAAAAAAAAAAAAgED/9oADAMBAAIQAxAAAAHgYnk11TGiIz9LAH//xAAaEAABBQEAAAAAAAAAAAAAAAACAAEDEBIg/9oACAEBAAEFAkY5uVuf/8QAGREAAQUAAAAAAAAAAAAAAAAAAQACEBEg/9oACAEDAQE/AUDctz//xAAaEQABBQEAAAAAAAAAAAAAAAACAAEQESBB/9oACAECAQE/AUL3Idz/AP/EABsQAAEEAwAAAAAAAAAAAAAAABEAASExAhAg/9oACAEBAAY/Ak0ksd4y1C+f/8QAGxABAQEAAgMAAAAAAAAAAAAAAREhABAgQWH/2gAIAQEAAT8hNdZ94LiHoToKzmDrCoHfH//aAAwDAQACAAMAAAAQVGoAH//EABkRAAEFAAAAAAAAAAAAAAAAAAEAECAhMf/aAAgBAwEBPxAvDSLY/wD/xAAbEQEBAAIDAQAAAAAAAAAAAAABEQAhECAxQf/aAAgBAgEBPxBYaxS0kZwsLj2I+vx6/wD/xAAdEAEAAQMFAAAAAAAAAAAAAAABEQAhQRAggZGh/9oACAEBAAE/ECCAVuiYojYALk5dGMQKwSgdtWoGeYCcD7t//9k=" | base64 -d > payload
$ hexdump -C payload | less
$ hexdump -C payload | head -10
00000000  ff d8 ff e0 00 10 4a 46  49 46 00 01 01 01 00 48  |......JFIF.....H|
00000010  00 48 00 00 ff db 00 43  00 10 0b 0c 0e 0c 0a 10  |.H.....C........|
00000020  0e 0d 0e 12 11 10 13 18  28 1a 18 16 16 18 31 23  |........(.....1#|
00000030  25 1d 28 3a 33 3d 3c 39  33 38 37 40 48 5c 4e 40  |%.(:3=<9387@H\N@|
00000040  44 57 45 37 38 50 6d 51  57 5f 62 67 68 67 3e 4d  |DWE78PmQW_bghg>M|
00000050  71 79 70 64 78 5c 65 67  63 ff db 00 43 01 11 12  |qypdx\egc...C...|
00000060  12 18 15 18 2f 1a 1a 2f  63 42 38 42 63 63 63 63  |..../../cB8Bcccc|
00000070  63 63 63 63 63 63 63 63  63 63 63 63 63 63 63 63  |cccccccccccccccc|
*
00000090  63 63 63 63 63 63 63 63  63 63 63 63 63 63 ff c2  |cccccccccccccc..|
```

`ff d8` the well known JPEG file header. Let's run `file` command to be sure.

```bash
$ file payload	
payload: JPEG image data, JFIF standard 1.01, resolution (DPI), density 72x72, segment length 16, progressive, precision 8, 20x20, frames 3
```

File doesn't looks to be corrupted and once opened it contains character `1`, which is the first one in our flag.
Repeat for the rest...

In the end we have: `1337.MD{icmp_tuneLs}`
