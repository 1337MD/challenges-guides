Insider Games #1
================

**Solution author:** https://twitter.com/s_persianov

**Description:** Someone is constantly leaking our internal data to competitors. 
Our IT Department captured network traffic from multiple employees. 
Find who’s doing it.

Extract the flag from given traffic.pcap file. Flag looks like: `1337.MD{`*something_random_or_not*`}`

**Walkthrough:** After a thorough analysis of the .pcap file, we can see that there are multiple DNS requests/replies
captured by our sniffer. Each DNS request looks like:

![image](https://user-images.githubusercontent.com/51073630/65370218-4b46c600-dc4e-11e9-82c3-8f86879a7e4b.png)

So the first DNS request is for `TkRrdE9Ea3ROemd0T1RrdE5UUXRNVEU1TFRFeE9TMHhNVGt0TVRBPQ.1337.md` domain.
If we try and base64 decode the level 3 domain we get `NDktODktNzgtOTktNTQtMTE5LTExOS0xMTktMTA=`, which looks like another base64
encoded string.
Second iteration: `49-89-78-99-54-119-119-119-10`.

Let's extract all DNS queries and decode the subdomains twice each. You can use `Scapy` to automate the process or apply
this filter `dns and ip.src==192.168.122.22 and not icmp` in Wireshark and export everything as .txt file.

Once .txt _(in my case it's called `dns`)_file created, let's do some `bash` magic to get the domains:

`grep "type A, class IN" dns --color=always | awk -F' ' '{print $1}' | awk -F'.' '{print $1}' > b64domains.txt && cat b64domains.txt`

I love oneliners...

```bash
TkRrdE9Ea3ROemd0T1RrdE5UUXRNVEU1TFRFeE9TMHhNVGt0TVRBPQ
TlRFdE9ETXRPRFl0TVRFekxURXhOaTB4TVRndE9EQXRNVEV4TFRFdw
TlRFdE56RXROelV0TVRBMUxURXdPQzB4TURRdE1URXdMVGcyTFRFdw
TlRVdE9UZ3ROell0TVRBeUxUYzBMVGc1TFRjM0xURXhPQzB4TUE9PQ
TkRZdE56UXROek10TVRBNUxUY3hMVFV6TFRjeExURXhPUzB4TUE9PQ
TnpjdE9UZ3ROVEl0TnpndE56UXRNVEF3TFRneExURXhNQzB4TUE9PQ
TmpndE9EZ3ROVE10TnpjdE5UUXRORGt0T0RBdE56TXRNVEE9
TVRJekxUVXpMVGMxTFRVMExUZ3lMVFV6TFRnNUxUY3lMVEV3
TVRJeExUWTRMVEV4TmkwM05TMDFNUzA0T1MwM01TMDFOeTB4TUE9PQ
TVRFeExUazNMVEV4TUMwM09DMHhNVFV0TmpjdE9EVXRORGd0TVRBPQ
TVRFM0xUY3hMVGMzTFRFd015MHhNRGd0T0RVdE1URXdMVEV3TlMweE1BPT0
T1RVdE1UQTFMVFE0TFRnM0xURXdPUzB4TWpFdE56Y3RPRGd0TVRBPQ
TVRBeUxUY3hMVGM1TFRnd0xUazVMVEV3TmkwMU55MHhNVFV0TVRBPQ
TVRFeExUVXlMVGczTFRjMExUazRMVEV3TUMwNE1pMHhNVE10TVRBPQ
TVRFM0xUZ3hMVEV3TmkwMU5DMDJOeTAyTnkweE1UUXROVFF0TVRBPQ
TVRFd0xUZ3hMVGcyTFRVd0xUYzRMVEV4T0MweE1UQXRNVEEzTFRFdw
TVRBd0xUVTBMVEV3TXkwNE15MHhNREl0TVRFMkxUVTBMVFkyTFRFdw
T1RVdE1URTRMVEV3TVMwMU5TMHhNVFV0T0RrdE1USXhMVGs0TFRFdw
TVRBMkxUVXpMVEV3TXkwNE5pMHhNVFl0T0RVdE9EUXRNVEF5TFRFdw
TVRFeExURXhPQzAzTVMweE1UTXRNVEV5TFRFd055MDROaTAzT1MweE1BPT0
TVRBeExUWTFMVEV3TUMweE1EWXRORGd0TVRFMExUY3hMVFUwTFRFdw
TVRJeExUYzJMVGcwTFRVMUxUZzBMVEV4TXkwMU5pMHhNVFV0TVRBPQ
T1RVdE1UQTRMVFk1TFRjNExUazVMVEV3TlMwNE9TMHhNVGN0TVRBPQ
T1RrdE5UUXRNVEUyTFRVMUxURXhNUzB4TURjdE9Ua3RNVEExTFRFdw
TVRFeExUVXhMVFE0TFRZM0xUZ3hMVGN6TFRrNUxURXdNaTB4TUE9PQ
TVRFd0xURXdNaTAxTXkwM05TMHhNRE10T1RrdE5qa3ROekl0TVRBPQ
TVRBekxUVTFMVEV4TWkwME9DMDJOeTAzTkMweE1EY3RNVEl5TFRFdw
TVRFMExURXdNQzAxTVMwM05TMDVPQzB4TVRrdE1UQTNMVGt3TFRFdw
T1RjdE1URTVMVGcyTFRrNUxUZ3hMVGM1TFRrNExUZzJMVEV3
TVRFMkxURXhOQzB4TVRrdE1URTJMVGcwTFRVMExURXdOUzB4TURBdE1UQT0
TVRFM0xURXlNQzB4TURFdE1UQTFMVFUwTFRVeUxURXhNUzAzTWkweE1BPT0
TVRBNExURXhNQzB4TURndE9UY3ROek10T0RRdE1URTBMVGc1TFRFdw
T1RjdE1URXlMVEV5TUMweE1qRXRPRFV0T0RZdE9UZ3ROVEF0TVRBPQ
TVRFMkxURXdNQzB4TVRBdE1URXhMVEV4T0MweE1UVXRNVEF6TFRFd055MHhNQT0
TVRBMUxUUTVMVEV4TlMweE1EY3ROVFV0TVRJeUxURXhOaTA0T0MweE1BPT0
TVRFeExUZ3hMVEV4TmkwNE9TMDVNQzAzTVMwM01DMDBPQzB4TUE9PQ
TVRFd0xURXdOQzA0TkMwM09DMDFNUzA0TWkweE1UY3RPVGN0TVRBPQ
TVRFMUxUUTVMVGM1TFRZNExUazRMVEV3T0MweE1USXRPRFF0TVRBPQ
TVRJMUxURXdNUzB4TWpFdE1URTJMVGMyTFRFd09TMDNPQzAzTXkweE1BPT0
```

Next we need to decode twice each line and see what we get:

```bash
for l in `cat b64domains.txt`; do echo ${l} | base64 -d | base64 -d; echo ""; done 
```

Looks like some base64 errors during decoding happened. Well, let's just ignore them for now and remove them with `grep -v "base64"` command:

```bash
49-89-78-99-54-119-119-119-10
51-83-86-113-116-118-80-111-10
51-71-75-105-108-104-110-86-10
55-98-76-102-74-89-77-118-10
46-74-73-109-71-53-71-119-10
77-98-52-78-74-100-81-110-10
68-88-53-77-54-49-80-73-10
123-53-75-54-82-53-89-72-10
121-68-116-75-51-89-71-57-10
111-97-110-78-115-67-85-48-10
117-71-77-103-108-85-110-105-10
95-105-48-87-109-121-77-88-10
102-71-79-80-99-106-57-115-10
111-52-87-74-98-100-82-113-10
117-81-106-54-67-67-114-54-10
110-81-86-50-78-118-110-107-10
100-54-103-83-102-116-54-66-10
95-118-101-55-115-89-121-98-10
106-53-103-86-116-85-84-102-10
111-118-71-113-112-107-86-79-10
101-65-100-106-48-114-71-54-10
121-76-84-55-84-113-56-115-10
95-108-69-78-99-105-89-117-10
99-54-116-55-111-107-99-105-10
111-51-48-67-81-73-99-102-10
110-102-53-75-103-99-69-72-10
103-55-112-48-67-74-107-122-10
114-100-51-75-98-119-107-90-10
97-119-86-99-81-79-98-86-10
116-114-119-116-84-54-105-100-10
117-120-101-105-54-52-111-72-10
108-110-108-97-73-84-114-89-10
97-112-120-121-85-86-98-50-10
116-100-110-111-118-115-103-107-10
105-49-115-107-55-122-116-88-10
111-81-116-89-90-71-70-48-10
110-104-84-78-51-82-117-97-10
115-49-79-68-98-108-112-84-10
125-101-121-116-76-109-78-73-10
```

Looks like there a no values bigger than 255, which probably mean that these are just decimal representations of ASCII charecters?
Let's do some Python =))

```python
>>> f = open("decimal_repr.txt", "r")
>>> l = f.readlines()
>>> l
['49-89-78-99-54-119-119-119-10\n', '51-83-86-113-116-118-80-111-10\n', '51-71-75-105-108-104-110-86-10\n', '55-98-76-102-74-89-77-118-10\n', '46-74-73-109-71-53-71-119-10\n', '77-98-52-78-74-100-81-110-10\n', '68-88-53-77-54-49-80-73-10\n', '123-53-75-54-82-53-89-72-10\n', '121-68-116-75-51-89-71-57-10\n', '111-97-110-78-115-67-85-48-10\n', '117-71-77-103-108-85-110-105-10\n', '95-105-48-87-109-121-77-88-10\n', '102-71-79-80-99-106-57-115-10\n', '111-52-87-74-98-100-82-113-10\n', '117-81-106-54-67-67-114-54-10\n', '110-81-86-50-78-118-110-107-10\n', '100-54-103-83-102-116-54-66-10\n', '95-118-101-55-115-89-121-98-10\n', '106-53-103-86-116-85-84-102-10\n', '111-118-71-113-112-107-86-79-10\n', '101-65-100-106-48-114-71-54-10\n', '121-76-84-55-84-113-56-115-10\n', '95-108-69-78-99-105-89-117-10\n', '99-54-116-55-111-107-99-105-10\n', '111-51-48-67-81-73-99-102-10\n', '110-102-53-75-103-99-69-72-10\n', '103-55-112-48-67-74-107-122-10\n', '114-100-51-75-98-119-107-90-10\n', '97-119-86-99-81-79-98-86-10\n', '116-114-119-116-84-54-105-100-10\n', '117-120-101-105-54-52-111-72-10\n', '108-110-108-97-73-84-114-89-10\n', '97-112-120-121-85-86-98-50-10\n', '116-100-110-111-118-115-103-107-10\n', '105-49-115-107-55-122-116-88-10\n', '111-81-116-89-90-71-70-48-10\n', '110-104-84-78-51-82-117-97-10\n', '115-49-79-68-98-108-112-84-10\n', '125-101-121-116-76-109-78-73-10\n']
>>> for a in l:
...     x = a.strip().split("-")
...     aux = []
...     for e in x:
...             aux.append(chr(int(e)))
...     print aux
... 
['1', 'Y', 'N', 'c', '6', 'w', 'w', 'w', '\n']
['3', 'S', 'V', 'q', 't', 'v', 'P', 'o', '\n']
['3', 'G', 'K', 'i', 'l', 'h', 'n', 'V', '\n']
['7', 'b', 'L', 'f', 'J', 'Y', 'M', 'v', '\n']
['.', 'J', 'I', 'm', 'G', '5', 'G', 'w', '\n']
['M', 'b', '4', 'N', 'J', 'd', 'Q', 'n', '\n']
['D', 'X', '5', 'M', '6', '1', 'P', 'I', '\n']
['{', '5', 'K', '6', 'R', '5', 'Y', 'H', '\n']
['y', 'D', 't', 'K', '3', 'Y', 'G', '9', '\n']
['o', 'a', 'n', 'N', 's', 'C', 'U', '0', '\n']
['u', 'G', 'M', 'g', 'l', 'U', 'n', 'i', '\n']
['_', 'i', '0', 'W', 'm', 'y', 'M', 'X', '\n']
['f', 'G', 'O', 'P', 'c', 'j', '9', 's', '\n']
['o', '4', 'W', 'J', 'b', 'd', 'R', 'q', '\n']
['u', 'Q', 'j', '6', 'C', 'C', 'r', '6', '\n']
['n', 'Q', 'V', '2', 'N', 'v', 'n', 'k', '\n']
['d', '6', 'g', 'S', 'f', 't', '6', 'B', '\n']
['_', 'v', 'e', '7', 's', 'Y', 'y', 'b', '\n']
['j', '5', 'g', 'V', 't', 'U', 'T', 'f', '\n']
['o', 'v', 'G', 'q', 'p', 'k', 'V', 'O', '\n']
['e', 'A', 'd', 'j', '0', 'r', 'G', '6', '\n']
['y', 'L', 'T', '7', 'T', 'q', '8', 's', '\n']
['_', 'l', 'E', 'N', 'c', 'i', 'Y', 'u', '\n']
['c', '6', 't', '7', 'o', 'k', 'c', 'i', '\n']
['o', '3', '0', 'C', 'Q', 'I', 'c', 'f', '\n']
['n', 'f', '5', 'K', 'g', 'c', 'E', 'H', '\n']
['g', '7', 'p', '0', 'C', 'J', 'k', 'z', '\n']
['r', 'd', '3', 'K', 'b', 'w', 'k', 'Z', '\n']
['a', 'w', 'V', 'c', 'Q', 'O', 'b', 'V', '\n']
['t', 'r', 'w', 't', 'T', '6', 'i', 'd', '\n']
['u', 'x', 'e', 'i', '6', '4', 'o', 'H', '\n']
['l', 'n', 'l', 'a', 'I', 'T', 'r', 'Y', '\n']
['a', 'p', 'x', 'y', 'U', 'V', 'b', '2', '\n']
['t', 'd', 'n', 'o', 'v', 's', 'g', 'k', '\n']
['i', '1', 's', 'k', '7', 'z', 't', 'X', '\n']
['o', 'Q', 't', 'Y', 'Z', 'G', 'F', '0', '\n']
['n', 'h', 'T', 'N', '3', 'R', 'u', 'a', '\n']
['s', '1', 'O', 'D', 'b', 'l', 'p', 'T', '\n']
['}', 'e', 'y', 't', 'L', 'm', 'N', 'I', '\n']
```

Bingo! First column is our flag: `1337.MD{you_found_joey_congratulations}`
