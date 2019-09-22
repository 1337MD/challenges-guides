Documents Heaven
================

**Solution author:** https://twitter.com/s_persianov

**Description:** We are a new startup aiming to make documents sharing a very easy and pleasant experience. 
All your documents are stored securely with us! [Link](http://docsheaven.1337.md/)

Flag looks like: `1337.MD{`*something_random_or_not*`}`

At the first glance this site looks very static, except one thing: The Manifesto. It is a PDF file, which you can 
download from 
[http://docsheaven.1337.md/4fdc5cd1b3130a18cff4ba052eb0ee62](http://docsheaven.1337.md/4fdc5cd1b3130a18cff4ba052eb0ee62)

Several failed trials to modify the `4fdc5cd1b3130a18cff4ba052eb0ee62` part and nothing =)).

Let's take a look at server's response when we download this file.

```bash
$ curl -I -L http://docsheaven.1337.md/4fdc5cd1b3130a18cff4ba052eb0ee62
HTTP/1.1 301 Moved Permanently
Server: nginx
Date: Sun, 22 Sep 2019 10:05:44 GMT
Content-Type: text/html
Content-Length: 178
Connection: keep-alive
Location: http://docsheaven.1337.md/1513123200/Document1.pdf
X-Frame-Options: SAMEORIGIN
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block

HTTP/1.1 200 OK
Server: nginx
Date: Sun, 22 Sep 2019 10:05:44 GMT
Content-Type: application/pdf
Content-Length: 37537
Last-Modified: Wed, 11 Sep 2019 07:12:20 GMT
Connection: keep-alive
ETag: "5d789e54-92a1"
X-Frame-Options: SAMEORIGIN
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Accept-Ranges: bytes
```

The `nginx` server in the backend actually redirects us to `http://docsheaven.1337.md/1513123200/Document1.pdf`.
What if we change `Document1.pdf` to let's say `Document2.pdf`? **Fail**

What about `1513123200`? This looks like a Unix Timestamp, which points to `December 13, 2017 12:00:00 AM`. On the
main page, we actually see `December 13, 2017` to be the date when the manifesto was actually uploaded.

The document we are interested in is probably the one uploaded from a `@1337.md` email address, on `13.09.2019`.
Converting this to Unix Timestamp gives us `1568332800`

Let's try and access `http://docsheaven.1337.md/1568332800/Document1.pdf`... JackPot!:

![image](https://user-images.githubusercontent.com/51073630/65385709-adc0c480-dd29-11e9-83d6-69893b26c914.png)

- Document2.pdf: **Boom**
- Document3.pdf: **Boom**
- Docuemnt4.pdf: **Boom**
- Document5.pdf: **Boom**
- `[...]` you know where this is going =))

```bash
for i in {0..255}; do wget http://docsheaven.1337.md/1568332800/Document${i}.pdf; done
```

Junk, junk, memes, more memes... and we get the Document133.pdf which contains our flag: 
`1337.MD{enumerate_and_download!!1!11!!}`
