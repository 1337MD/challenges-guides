Deep Dive
=========

**Solution author:** https://twitter.com/1337Moldova

**Description:** Find the correct PIN and/or extract the flag. Flag looks like: `1337.MD{`*something_random_or_not*`}`

This is a linux executable file (ELF) asking for a PIN in order to display the flag.

![image](https://user-images.githubusercontent.com/51073630/58430617-fb8e2e80-80a1-11e9-81f9-a977f013fc61.png)

![image](https://user-images.githubusercontent.com/51073630/58430652-224c6500-80a2-11e9-8217-b2f1309fd196.png)

After several attempts/failures let's analyze it in `radare2`. Knowing that the flag starts with `1337.MD{` sequence, we can do a quick search against this string and see if we get anything.

![image](https://user-images.githubusercontent.com/51073630/58431264-e535a200-80a4-11e9-9156-b4a239167542.png)

Looks like we got lucky and there is something like this in the binary. A thorough look around that part of the code reveals a chain of `printf` calls, which are meant to display something which starts with `1337.MD{`.

![image](https://user-images.githubusercontent.com/51073630/58431365-478ea280-80a5-11e9-9838-5080e60476ef.png)

Execution flow looks like this:

1. Prints "Ooooh snap...";
2. Prints `1337.MD{`;
3. Calls `Ox8fae` function;
4. Prints `_found_it}`;

So we have the beginning and the end of the flag here. Probably the missing part of it is generated/printed inside `Ox8fae`.

![image](https://user-images.githubusercontent.com/51073630/58431463-d8fe1480-80a5-11e9-9939-7d0e5a796a19.png)

The beauty of stack strings. Function `Ox8fae` prints out: `now_breath_you`, which gives us the whole flag: `1337.MD{now_breath_you_found_it}`
