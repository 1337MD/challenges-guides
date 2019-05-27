Chain of Secrets
================

**Solution author:** https://twitter.com/1337Moldova

**Description:** Extract the flag from given JPEG file. Flag looks like: `1337.MD{`*something_random_or_not*`}`

First let's check what type of file we have to deal with.

![image](https://user-images.githubusercontent.com/51073630/58404090-340b1980-805c-11e9-8254-caea6033b313.png)

We can see that it's a JPEG file (JFIF standard). In order to check if there are any hidden files inside (concatenated maybe), we can use `binwalk` tool. This tools parses binary files and shows all headers/footers found.

![image](https://user-images.githubusercontent.com/51073630/58404036-163db480-805c-11e9-80d6-fde9b7acf89b.png)

The are 3 JPEG files headers found in the `chain_of_secrets.jpg`. Based on the output we got fron `binwalk`, we can identify the position of where each file starts. Using `dd` and the number of bytes we have to skip, extraction shouldn't be difficult.

![image](https://user-images.githubusercontent.com/51073630/58404145-63ba2180-805c-11e9-988e-88844765cbdb.png)

![image](https://user-images.githubusercontent.com/51073630/58404194-7b91a580-805c-11e9-98d7-47f40a90cad1.png)

Now let's open obtained images in an image viewer to see what we got.

![image](https://user-images.githubusercontent.com/51073630/58404258-a2e87280-805c-11e9-9129-cde652df6074.png)

![image](https://user-images.githubusercontent.com/51073630/58404284-b09df800-805c-11e9-8b0c-7bd0fc74c556.png)


So the flag is: `1337.MD{c4t_l1k3_4_b055}`
