Riddler's Notes #3
==================

**Solution author:** https://twitter.com/s_persianov

**Description:** Riddler got hold of a cryptographer. 
Now he encrypts his messages with customly made algorithm: CryptoRidd. 
Analyze the encrypter and decrypt the message if possible.

Flag format: 1337.MD{`something_random_or_not`}

The crypter is written in Python:

```python
from Crypto.Cipher import AES
import random
import base64

def CryptoRidd_encrypt(plain_text):
    key = ''.join(chr(random.randint(65, 90)) for i in range(6))
    key = key + "Ndu3Nfhh32"
    IV = "5NkH9cgLflAWl76O"
    aes = AES.new(key, AES.MODE_CBC, IV)

    # I'll make sure all my messages are multiples of 16
    enc_text = aes.encrypt(plain_text)
    print "Enc key: " + key
    print "IV: " + IV
    print "Encrypted: " + base64.b64encode(enc_text)
```

It used AES encryption, however the bottle neck here is the usage of same `IV`. Also, the encryption key, tends to be
16 characters (bytes) long with only 6 random.

Knowing all the above and the flag format, we can create our `CryptoRidd` cracker, which will try all possible
combinations of 6 characters and get the flag for us. You can make it multi-threaded and optimize of course.

Here's a PoC:

```python
from Crypto.Cipher import AES
import random
import base64
import string

def CryptoRidd_decrypt(enc_text):
    charset = string.ascii_letters + string.digits
    for a in charset:
        for b in charset:
            for c in charset:
                for d in charset:
                    for e in charset:
                        for f in charset:
                            key = a+b+c+d+e+f + "Ndu3Nfhh32"
                            IV = "5NkH9cgLflAWl76O"
                            aes = AES.new(key, AES.MODE_CBC, IV)
                            plain_text = aes.decrypt(enc_text)
                            if "1337.MD{" in plain_text:
                                print "Key: " + key
                                print "Message: " + plain_text
                                return 0

def main():
    CryptoRidd_decrypt(base64.b64decode("wTC08HH49DJj06DRU3nZnmCXd3W4M8Hxgip2Wrw9jbjOwWkBgml6KFNThZWT0rK6"))

if __name__ == "__main__":
    main()
```

And the result is: `1337.MD{new_coordinates_are_13.00North_37.0East}`
