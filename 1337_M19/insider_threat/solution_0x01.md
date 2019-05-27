Insider Threat
================

**Solution author:** https://twitter.com/1337Moldova

**Description:** Find the secret John had stolen. Flag looks like: `1337.MD{`*something_random_or_not*`}`

This was probably the easiest challenge during InfoSec Meetup CTF. We got `johnspc.img.gz`, which a GZIP arhive.

![image](https://user-images.githubusercontent.com/51073630/58407305-68360880-8063-11e9-86ad-fbdcc5aeb07e.png)

Let's `gunzip` the file and see what's inside. Do not forget to make a backup copy :))

![image](https://user-images.githubusercontent.com/51073630/58407460-b814cf80-8063-11e9-8a52-242c9b3062f8.png)

Looks like we got an 1GB filesystem image file. Well, there is nothing else we can do except:

1. Mount it right away;
2. Analyze with `binwalk` and then `mount` right away :D;

![image](https://user-images.githubusercontent.com/51073630/58407861-92d49100-8064-11e9-955f-2cab0adb2a7a.png)

Once image is mounted we can see a bunch of directories and files.

![image](https://user-images.githubusercontent.com/51073630/58408517-e0053280-8065-11e9-879c-f6da3e064f97.png)

In order to unzip `the_secret.zip` file, we need the password to it. After several trials, looks like the password is in the hidden `.password.wav` file. Password is: 13371337133713

![image](https://user-images.githubusercontent.com/51073630/58408676-2bb7dc00-8066-11e9-8b66-1be88234aaee.png)

And the extracted file is a PDF, containing the flag: `1337.MD{some_forensics_to_spice_up_things}`

![image](https://user-images.githubusercontent.com/51073630/58408841-9cf78f00-8066-11e9-8974-21bed171485c.png)
