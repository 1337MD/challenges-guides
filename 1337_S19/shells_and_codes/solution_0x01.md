The shells and codes
====================

**Solution author:** https://twitter.com/s_persianov

**Description:** This is a fragment of something, not sure what. Flag should be _pushed_ around =))

Flag looks like: `1337.MD{`*something_random_or_not*`}`

```python
\xB8\x01\x00\x00\x00\x83\xC0\x01\x90\x90\x90\x85\xC0\x31\xC0\x0F\x85\x40\x44\x00\x00\x68\x64\x65\x73\x7D\x68\x6C\x6C\x63\x6F\x68\x5F\x73\x68\x65\x68\x70\x69\x6E\x67\x68\x77\x72\x61\x70\x68\x2E\x4D\x44\x7B\x68\x31\x33\x33\x37\x39\xCB\xCC\xBB\x04\x00\x00\x00\x83\xEB\x00\x01\xD8\x83\xC0\x00
```

From the name of this challenge looks we'll have to deal with shellcodes, and the HEX sequence above looks to be some
compiled machine code.

There are several ways to analyze something like this:

- Convert from string to binary and write to a file. Analyze the file in a disassembler;
- Wrap the shellcode, compile and then analyze it with a disassembler;

Let's take the second approach.

```c
const char shellcode[] = "\xB8\x01\x00\x00\x00\x83\xC0\x01\x90\x90\x90\x85\xC0\x31\xC0\x0F\x85\x40\x44\x00\x00\x68\x64\x65\x73\x7D\x68\x6C\x6C\x63\x6F\x68\x5F\x73\x68\x65\x68\x70\x69\x6E\x67\x68\x77\x72\x61\x70\x68\x2E\x4D\x44\x7B\x68\x31\x33\x33\x37\x39\xCB\xCC\xBB\x04\x00\x00\x00\x83\xEB\x00\x01\xD8\x83\xC0\x00";

int main(){
    (*(void(*)()) shellcode)();
    return 0;
}
```

```bash
gcc shellcode.c -o shellcode.out
r2 -A shellcode.out
```

Let's jump to the address where our shellcode was loaded and check the assembly.

```assembly
mov eax, 1
add eax, 1
nop
nop
nop
test eax, eax
xor eax, eax
jne 0x4444
push 0x7d736564
push 0x6f636c6c
push 0x6568735f
push 0x676e6970
push 0x70617277
push 0x7b444d2e
push 0x37333331
cmp ebx, ecx
int3
mov ebx, 4
sub ebx, 0
add eax, ebx
add eax, 0
```

The code doesn't do anything, except pushing some values onto the stack. There is a hint in the description
of this challenge _flag should be pushed around_.

Let's decode each value from HEX to ASCII and see what we get:

- `0x37333331`: `7331`;
- `0x7b444d2e`: `{DM.`;
- `0x70617277`: `parw`;
- `0x676e6970`: `gnip`;
- `0x6568735f`: `ehs_`;
- `0x6f636c6c`: `ocll`;
- `0x7d736564`: `}sed`;

Looks like junk, except if you remember about the **Endianess**, specifically **little** one =))

More details about it [HERE](https://en.wikipedia.org/wiki/Endianness).

In the end, flag is: `1337.MD{wrapping_shellcodes}`
