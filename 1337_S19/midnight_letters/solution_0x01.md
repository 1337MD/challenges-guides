Midnight letters
================

**Solution author:** https://twitter.com/s_persianov

**Description:** A conspiracy theorist asked you to test his new method of hiding messages. Is it really secure?

Flag looks like: `1337.MD{`*something_random_or_not*`}`

Let's keep it short =)). Three pictures are given. Let's `binwalk` them first. Nothing interesting comes up.

```bash
$ binwalk *
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01
30            0x1E            TIFF image data, little-endian offset of first image directory: 8
432           0x1B0           JPEG image data, JFIF standard 1.01

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01
30            0x1E            TIFF image data, little-endian offset of first image directory: 8
432           0x1B0           JPEG image data, JFIF standard 1.01

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01
30            0x1E            TIFF image data, little-endian offset of first image directory: 8
446           0x1BE           JPEG image data, JFIF standard 1.01
```

It's time to use hexdump and some manual analysis.

```bash
$ hexdump -C ab.jpg | less
00000000  ff d8 ff e0 00 10 4a 46  49 46 00 01 01 01 00 48  |......JFIF.....H|
00000010  00 48 00 00 ff e1 41 b2  45 78 69 66 00 00 49 49  |.H....A.Exif..II|
00000020  2a 00 08 00 00 00 06 00  03 01 03 00 01 00 00 00  |*...............|
00000030  06 00 00 00 1a 01 05 00  01 00 00 00 56 00 00 00  |............V...|
00000040  1b 01 05 00 01 00 00 00  5e 00 00 00 28 01 03 00  |........^...(...|
00000050  01 00 00 00 02 00 00 00  32 01 02 00 14 00 00 00  |........2.......|
00000060  66 00 00 00 69 87 04 00  01 00 00 00 7a 00 00 00  |f...i.......z...|
00000070  fe 00 00 00 48 00 00 00  01 00 00 00 48 00 00 00  |....H.......H...|
00000080  01 00 00 00 32 30 31 39  3a 30 39 3a 30 39 20 32  |....2019:09:09 2|
00000090  31 3a 30 35 3a 34 39 00  04 00 00 90 07 00 04 00  |1:05:49.........|
000000a0  00 00 30 32 33 31 01 91  07 00 04 00 00 00 01 02  |..0231..........|
000000b0  03 00 86 92 07 00 4e 00  00 00 b0 00 00 00 00 a0  |......N.........|
000000c0  07 00 04 00 00 00 30 31  30 30 00 00 00 00 41 53  |......0100....AS|
000000d0  43 49 49 00 00 00 43 72  65 61 74 65 64 20 69 6e  |CII...Created in|
000000e0  20 47 49 4d 50 20 34 39  20 35 31 20 35 31 20 35  | GIMP 49 51 51 5|
000000f0  35 20 34 36 20 37 37 20  36 38 20 31 32 33 20 31  |5 46 77 68 123 1|
00000100  31 36 20 31 30 34 20 31  30 31 20 31 32 31 20 39  |16 104 101 121 9|
00000110  35 20 39 37 20 31 31 34  20 31 30 31 0a 00 00 01  |5 97 114 101....|
```

There are some digits in the comments section of the JPEG file. ASCII? Never hurts to try...

```python
>>> a = "49 51 51 55 46 77 68 123 116 104 101 121 95 97 114 101"
>>> b = a.split(" ")
>>> b
['49', '51', '51', '55', '46', '77', '68', '123', '116', '104', '101', '121', '95', '97', '114', '101']
>>> for e in b:
...     print chr(int(e))
... 
1
3
3
7
.
M
D
{
t
h
e
y
_
a
r
e
```

Heh, first part of the flag. Repeat for the rest of the pictures... 
and we get the flag: `1337.MD{they_are_watching_me_see_you_at_midnight}`
