Hackers Vault
=================

**Solution author:** https://twitter.com/1337Moldova

**Description:** Analyze the .apk file and get the flag. Flag looks like: `1337.MD{`*something_random_or_not*`}`

First of all, let's check if we really got an `.apk` file and not something else.

![image](https://user-images.githubusercontent.com/51073630/58414821-76405500-8074-11e9-840d-70d23ddd9c4f.png)

We see that the file is a ZIP archive (which applies to APK files) and it contains `classes.dex`. Alright, it looks to be an Android application. Next step usually is `unzip`-ing the file and extracting that Dalvik Executable (.dex).

![image](https://user-images.githubusercontent.com/51073630/58415051-26ae5900-8075-11e9-824e-98755d6d3477.png)

Time to convert the .dex file to a proper Java Archive (.jar). We can use `dex2jar` tool for this purpose, make sure to use the latest version.

![image](https://user-images.githubusercontent.com/51073630/58416351-33cd4700-8079-11e9-902a-05a78fdb2c8d.png)

Now we can load the .jar file into any Java Decompiler (JD-GUI, jadx, etc.). Once loaded, besides multiple dependencies, two classes appear to be of the most interest: `MainActivity.class` and `FlagGuard.class`.
Usually `MainActivity.class` is the entry point of any Android application, and it applies in this case as well.

![image](https://user-images.githubusercontent.com/51073630/58416646-354b3f00-807a-11e9-9327-4ce452c6d2b1.png)

While analyzing `MainActivity.class` nothing of major interest pops up, except a call of `FlagGuard.getFlag()` method.

![image](https://user-images.githubusercontent.com/51073630/58416811-bb678580-807a-11e9-8452-2c6932781e11.png)

Let's switch to `FlagGuard.class` file and analyze it in more details. The `getFlag` method doesn't have any logic behind and just returns the `displayBuffer` attribute.

```java
public String getFlag() {
    return this.displayBuffer;
}
```

Looks like `displayBuffer` contains the flag in case if MD5-ed user's input is equal to a hardcoded checksum. This string being the last part of the flag. So, we have no choise and have to start brute forcing `066cc86fc481fd97bc091c9c4af2b9b6`.

```java
public FlagGuard(final String s) {
    this.password = "066cc86fc481fd97bc091c9c4af2b9b6";
    this.partial_flag = "1337.MD{the_secret_is_";
    this.displayBuffer = "You entered wrong password!\n\nReminder:\n\t- Password length: 7 characters;\n\t- First character: uppercase;\n\t- Last 2 characters: digits;";
    if (this.computeMD5(s).equals("066cc86fc481fd97bc091c9c4af2b9b6")) {
        final StringBuilder sb = new StringBuilder();
        sb.append("1337.MD{the_secret_is_");
        sb.append(s);
        sb.append("}");
        this.displayBuffer = sb.toString();
    }
}
```

I'll use `hashcat` to crack the MD5 hash. Also, in order to minimize the time needed for brute force, a mask can be created based on the password description inside the application:

- Password length: 7 characters;
- First character: uppercase;
- Last 2 characters: digits;

We end up using the following mask: `?u?a?a?a?a?d?d`, which takes about 1h 30m to run (VM: 1 vCPU, 1 GB, no video card).

After a while, we get the flag: `1337.MD{the_secret_is_BhkdL97}`
