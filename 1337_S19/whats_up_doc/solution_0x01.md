What's up, Doc?
===============

**Solution author:** https://twitter.com/s_persianov

**Description:** Ever heard about executable documents? Well know you’ve heard for sure. 
Would you get the flag for us please?

Extract the flag from given file. Flag looks like: `1337.MD{`*something_random_or_not*`}`

There are multiple ways to solve this challenge. One of them is opening the PDF file on a Windows VM.
From the beginning it shows an `alert` box with the following message: `Welcome to 1337.MD challenge`.
A first sign there is some code embedded in the document.

After few seconds it tries to connect to localhost `127.0.0.1`. We can run Wireshark and see what request (if any)
is performed.

This is a `GET` request to `http://127.0.0.1/index.html` with following parameters:

- `type`: `flag`;
- `enc`: `yes`;
- `times`: `4`;
- `value`: `VkZaU1RtVnJOVFZPVlRWVFUwaFNlRmw2UlRWaVIxSjBWbTVzYkZkSFVuWlhiR2hMWWtabmVXTkVSbXBOTVVwdFdXcE9RMkpIU25OUFZFSm9VakZhYlZkcll6VmhiVnBTVUZRd1N3bz0K`;

These arguments are meaningul =)), looks like the passed value is our flag, which is 4 times encoded/encrypted. Based
on the value, it looks more like base64 encoded.

```bash
echo "VkZaU1RtVnJOVFZPVlRWVFUwaFNlRmw2UlRWaVIxSjBWbTVzYkZkSFVuWlhiR2hMWWtabmVXTkVSbXBOTVVwdFdXcE9RMkpIU25OUFZFSm9VakZhYlZkcll6VmhiVnBTVUZRd1N3bz0K" | base64 -d | base64 -d | base64 -d | base64 -d
```
And we got our flag: `1337.MD{js_everywhere_just_open_the_doc}`

**Linux approach: ** If you don't have a Windows VM, then you can use `strings` command to extract the JS. As this JS
object wasn't encoded inside the document, we can easily see it.

```bash
$ strings doc.pdf
6 0 obj
/Type /Action
/S /JavaScript
/JS (var _0xeadc=["\\x57\\x65\\x6C\\x63\\x6F\\x6D\\x65\\x20\\x74\\x6F\\x20\\x31\\x33\\x33\\x37\\x2E\\x4D\\x44\\x20\\x63\\x68\\x61\\x6C\\x6C\\x65\\x6E\\x67\\x65","\\x61\\x6C\\x65\\x72\\x74","\\x68\\x74\\x74\\x70\\x3A\\x2F\\x2F\\x31\\x32\\x37\\x2E\\x30\\x2E\\x30\\x2E\\x31\\x2F\\x69\\x6E\\x64\\x65\\x78\\x2E\\x68\\x74\\x6D\\x6C\\x3F\\x74\\x79\\x70\\x65\\x3D\\x66\\x6C\\x61\\x67\\x26\\x65\\x6E\\x63\\x3D\\x79\\x65\\x73\\x26\\x74\\x69\\x6D\\x65\\x73\\x3D\\x34\\x26\\x76\\x61\\x6C\\x75\\x65\\x3D\\x56\\x6B\\x5A\\x61\\x55\\x31\\x52\\x74\\x56\\x6E\\x4A\\x4F\\x56\\x46\\x5A\\x50\\x56\\x6C\\x52\\x57\\x56\\x46\\x55\\x77\\x61\\x46\\x4E\\x6C\\x52\\x6D\\x77\\x32\\x55\\x6C\\x52\\x57\\x61\\x56\\x49\\x78\\x53\\x6A\\x42\\x57\\x62\\x54\\x56\\x7A\\x59\\x6B\\x5A\\x6B\\x53\\x46\\x56\\x75\\x57\\x6C\\x68\\x69\\x52\\x32\\x68\\x4D\\x57\\x57\\x74\\x61\\x62\\x6D\\x56\\x58\\x54\\x6B\\x56\\x53\\x62\\x58\\x42\\x4F\\x54\\x56\\x56\\x77\\x64\\x46\\x64\\x58\\x63\\x45\\x39\\x52\\x4D\\x6B\\x70\\x49\\x55\\x32\\x35\\x4F\\x55\\x46\\x5A\\x46\\x53\\x6D\\x39\\x56\\x61\\x6B\\x5A\\x68\\x59\\x6C\\x5A\\x6B\\x63\\x6C\\x6C\\x36\\x56\\x6D\\x68\\x69\\x56\\x6E\\x42\\x54\\x56\\x55\\x5A\\x52\\x64\\x31\\x4E\\x33\\x62\\x7A\\x30\\x4B","\\x48\\x54\\x4D\\x4C","\\x73\\x75\\x62\\x6D\\x69\\x74\\x46\\x6F\\x72\\x6D"];app[_0xeadc[1]]\(_0xeadc[0]\);this[_0xeadc[4]]\({cURL:_0xeadc[2],cSubmitAs:_0xeadc[3]}\)\n)
endobj
```

The script is a little obfuscated. Looks like HEX code. If we decode it to ASCII directly, we get:

- `Welcome to 1337.MD challenge`;
- `alert`;
- `http://127.0.0.1/index.html?type=flag&enc=yes&times=4&value=VkZaU1RtVnJOVFZPVlRWVFUwaFNlRmw2UlRWaVIxSjBWbTVzYkZkSFVuWlhiR2hMWWtabmVXTkVSbXBOTVVwdFdXcE9RMkpIU25OUFZFSm9VakZhYlZkcll6VmhiVnBTVUZRd1N3bz0K`;
- `HTML`;
- `submitForm`;

We have the request URL -> we have the flag.
