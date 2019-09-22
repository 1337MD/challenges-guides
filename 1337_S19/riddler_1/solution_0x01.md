Riddler's Notes #1
=====================

**Solution author:** https://twitter.com/s_persianov

**Description:** Nobody knows who the Riddler is and how he/she sends the commands to the team. 
We were able to find a file (image) at his/her latest known location.  
Our scanners found nothing suspicious except small differences in the binary data. 

Extract the message! Flag looks like: `1337.MD{`*something_random_or_not*`}`

Visually analyzing the images `original.jpg` and `riddler_notes_1.jpg`, we see that the bottom right corner is
kind of corrupted. Bad steganography?

Let's check in `hexdump`.

```bash
$ hexdump -C original.jpg | tail -10
0000c760  17 fd 59 ff 00 f5 bf ef  dd 7b af 7f 91 7f d5 9f  |..Y......{......|
0000c770  ff 00 5b fe fd d7 ba f7  f9 17 fd 59 ff 00 f5 bf  |..[........Y....|
0000c780  ef dd 7b af 7f 91 7f d5  9f ff 00 5b fe fd d7 ba  |..{........[....|
0000c790  f7 f9 17 fd 59 ff 00 f5  bf ef dd 7b af 7f 91 7f  |....Y......{....|
0000c7a0  d5 9f ff 00 5b fe fd d7  ba f7 f9 17 fd 59 ff 00  |....[........Y..|
0000c7b0  f5 bf ef dd 7b af 7f 91  7f d5 9f ff 00 5b fe fd  |....{........[..|
0000c7c0  d7 ba f7 f9 17 fd 59 ff  00 f5 bf ef dd 7b a9 74  |......Y......{.t|
0000c7d0  df 65 e1 af ff 00 8b 47  fc 04 4f a7 f7 82 df f0  |.e.....G..O.....|
0000c7e0  3a 8b eb 6f c7 fc 4f bf  75 ee bf ff d9           |:..o..O.u....|
0000c7ed

$ hexdump -C riddler_notes_1.jpg | tail -10
0000c760  17 fd 59 ff 00 f5 bf ef  dd 7b af 7f 91 7f d5 9f  |..Y......{......|
0000c770  ff 00 5b fe fd d7 ba f7  f9 17 fd 59 ff 00 f5 bf  |..[........Y....|
0000c780  ef dd 7b af 7f 91 7f d5  9f ff 00 5b fe fd d7 ba  |..{........[....|
0000c790  f7 f9 17 fd 59 ff 00 f5  bf ef dd 7b af 7f 91 7f  |....Y......{....|
0000c7a0  d5 9f ff 00 5b fe fd d7  ba f7 f9 17 fd 59 ff 00  |....[........Y..|
0000c7b0  f5 bf ef dd 7b af 7f 91  7f d5 9f ff 00 5b fe fd  |....{........[..|
0000c7c0  d7 ba f7 f9 17 fd 59 ff  00 f5 f0 23 11 b2 d7 c1  |......Y....#....|
0000c7d0  24 e0 56 18 65 5f f2 a8  6a 69 ae 11 6b e1 4e 60  |$.V.e_..ji..k.N`|
0000c7e0  ae ea 5b e5 2d 6f ae 39  da 63 3d ff d9           |..[.-o.9.c=..|
0000c7ed
```

In both case `ff d9` is present, which is the footer and was left intact. So the file is not corrupted,
however the image itself was changed.

Usually in basic Stego bytes addition and subtraction is used. Let's give it a try. Starting with the first different
byte in the sequence:

`bf` and `f0`. `f0 > bf`, so it looks like some extra value was added. Let's subtract:

`f0 - bf = 31`. In ASCII this is character `1`. Looks like the first character of the flag?

Hmm, second pair of bytes is `ef` and `23`. `23` is not bigger than `ef`... So if the algorithm is to use addition,
then we could end up having overflow, because the biggest byte is `0xff` or `255`.

Assuming there was an overflow, let's extract the value using this formula: `value = 23 + (ff - ef)`. Value in this
is `33` in HEX or `51` in decimal, which in ASCII is `3`. Bingo, second character of our flag.

In the end, we obtain: `1337.MD{the_game_is_not_over_yet}`
