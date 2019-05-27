The FLag Within
=================

**Solution author:** https://twitter.com/1337Moldova

**Description:** Analyze the obfuscated JavaScript file and extract the flag. Flag looks like: `1337.MD{`*something_random_or_not*`}`

The obfuscated JS code doesn't seem to contain anything related to the flag.

![image](https://user-images.githubusercontent.com/51073630/58409228-91f12e80-8067-11e9-826f-7cb3e0a68de5.png)

First things first, using a JavaScript beautifier may help, but we should be careful as sometimes important variables/function calls get stripped. Usually we should be using beautifiers to get just a glance at what **possibly** a script may/may not do.

![image](https://user-images.githubusercontent.com/51073630/58414198-9cfd8c00-8072-11e9-8763-5e7157261f9c.png)

We can see some interesting keywords, like: `console`, `String['fromCharCode']`. As mentioned above, this beautifier stripped an important detail:

```javascript
var _0x4a67=['WOZhE','log'];
```

This way, we can see that `_0x314c('0x1')` actually equals to `_0x4a67[1]` OR `log`. This means that the obfuscated script prints something out on the console in the browser. However, on executing the script inside VM's browser by copy-pasting it nothing was displayed. This is actually caused by the fact that the entry point into this program does not exist.

![image](https://user-images.githubusercontent.com/51073630/58414620-d97db780-8073-11e9-831e-dd191d67c6b4.png)

Let's try and add an entry point by calling `_0x311fb5()`, the function which contains that `console.log(...)` method call.

![image](https://user-images.githubusercontent.com/51073630/58414681-0762fc00-8074-11e9-9312-31d782b2d896.png)

Here we go, the flag is ours: `1337.MD{obfuscation_rocks}`
