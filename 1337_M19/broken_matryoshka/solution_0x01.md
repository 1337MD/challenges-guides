Broken Matryoshka
=================

**Solution author:** https://twitter.com/1337Moldova

**Description:** Analyze and repair `broken_matryoshka` file. Extract the secret. Flag looks like: `1337.MD{`*something_random_or_not*`}`

First let's check what type of file we have to deal with.

![image](https://user-images.githubusercontent.com/51073630/58405347-230fd780-805f-11e9-99b6-accd5f76662d.png)

Looks like `file` command couldn't find anything about this file. `binwalk` to the rescue?

![image](https://user-images.githubusercontent.com/51073630/58405470-65391900-805f-11e9-9f76-1eeb4ca4d1ce.png)

Based on what we've got, it looks like `binwalk` was able to find only some footer of a ZIP file. To analyze in more details this weird thing, I'll use `strings` and `hexdump`. Who knows maybe there's a hint or something...

![image](https://user-images.githubusercontent.com/51073630/58405750-10e26900-8060-11e9-898c-93d6b0057c76.png)

Nope, `1337.MD{s e c r e t m e s s a g e}` is not the flag =)).

A ZIP file usually has this format: 

- Header bytes: `50 4b 03 04`;
- Central directory bytes: `50 4b 01 02`;
- Footer bytes: `50 4b 05 06`;

Let's use `hexdump` and see if we can find the following sequences of bytes.

**Footer** 

![image](https://user-images.githubusercontent.com/51073630/58406372-59e6ed00-8061-11e9-8cb3-a032110d210f.png)

**Central directory** 

![image](https://user-images.githubusercontent.com/51073630/58406354-505d8500-8061-11e9-876c-0952c1e96633.png)

**Header**

![image](https://user-images.githubusercontent.com/51073630/58406332-45a2f000-8061-11e9-8b72-2445e11e195b.png)

However, header bytes are not complete. Instead of `03 04` there are 2 NULL bytes `00 00`.

Using `Bless` hex editor, I modified 3rd and 4th bytes of the file from `00` to `03` and `04` respectively. New file was saved as `broken_matryoshka_modified`. By running `file` command we can see that it is able to detect the file type correctly now.

![image](https://user-images.githubusercontent.com/51073630/58406737-2b1d4680-8062-11e9-9e52-79d82b794a3e.png)

By unziping the modified file, we obtain `s e c r e t m e s s a g e`, which is a PDF.

![image](https://user-images.githubusercontent.com/51073630/58406812-5d2ea880-8062-11e9-9749-96895e9d7be2.png)

When you open this PDF, you can see the flag: `1337.MD{playing_spies_is_awesome}`

![image](https://user-images.githubusercontent.com/51073630/58406915-9535eb80-8062-11e9-85f2-c2a12610c358.png)
