Wires Ghost #2
==============

**Solution author:** https://twitter.com/s_persianov

**Description:** Session hijacking? We got the hashes of W1r3sGh0st, from one of the hacking forums on Dark Web. 
Passwords are up to 6 characters. Crack them!
 
- MD5: `36b4b2db48ab63e6ad3a03362a2a3fd8` 
- Broken. Unknown: `apr1$rV16SVx7$aAjWk1HZe280qOL7hDaTn/`  

Unzip the flag using combined passwords. Flag looks like: `1337.MD{`*something_random_or_not*`}`

This one doesn't need much of interaction, just the correct `hashcat` (or any other tool) masks for cracking.
The second hash appears to be Apache hash, based on the `apr1$` part. We should add `$` in the beginning, as this
is the correct format.

The only hint is that passwords are **up to** 6 characters long. We should probably gor for alphanumeric and special
characters here.

- For MD5 (6 characters): `hashcat -m 0 -a 3 hashes ?a?a?a?a?a?a` 
- For Apache hash (5 character): `hashcat -m 1600 -a 3 hashes ?a?a?a?a?a`

In the end we get `%ds@2s` and `Bh15f`. By combining them we obtain the password for provided .zip file `Bh15f%ds@2s`.

Flag is: `1337.MD{apache_apr_hashes_easy}`
