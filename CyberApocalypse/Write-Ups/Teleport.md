# Teleport 
[omega-One](https://github.com/mhoste51/writeups/tree/main/CyberApocalypse/Code-source/teleport)  
*Points : **300** / Solves : **307** / Difficulty : **Medium***   
```
mathieu@MacBook-Air-de-Mathieu Downloads % file teleport 
teleport: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=fe96e54a3cc59c165d21aa34f9b9355e88e2d7d0, stripped
```   
Ce fichier est un executable ELF x86 comme d'habitude. Ouvrons ghidra pour désassembler le programme.   
On obtiens des dizaines et des dizaines de fonctions qui ont la même fonction principal :   
```c
void FUN_00100cc2(void)

{
  int iVar1;
  
  iVar1 = _setjmp((__jmp_buf_tag *)&DAT_00304750);
  if (iVar1 == 0) {
    return;
  }
  if (DAT_0030329a == '_') {
                    /* WARNING: Subroutine does not return */
    longjmp((__jmp_buf_tag *)&DAT_003031a0,0x1b);
  }
                    /* WARNING: Subroutine does not return */
  longjmp((__jmp_buf_tag *)&DAT_003031a0,0x65);
}
```
On a donc un tableau *DAT_XXX* qui sera comparé à un caractère. On se retrouve alors avec des dizaines de caractères dans un désordre total.   
A partir de là mon hypothèse est la suivante : DAT_XXX est un tableau ou une chaine de caractère, la solution serai alors de reprendre les caractères qui sont check des les fonctions dans l'ordre.
Dans l'exemple, on a *&DAT_00304750* nous devons alors retrouver la première adresse du DAT_XXX. Soit on cherche tous les *DAT_XXX* soit on fouille dans ghidra pour retrouver l'ordre qui a été stocké dans la mémoire (qui sera donc l'ordre de tous les caractères).   
On tombe alors sur ça :   
 ```c
                             DAT_00303280                                    XREF[2]:     FUN_00100f6a:00100f7e(R), 
                                                                                          FUN_00101696:001016d4(*)  
        00303280                 undefined1 ??
                             DAT_00303281                                    XREF[1]:     FUN_00100dd2:00100de6(R)  
        00303281                 undefined1 ??
                             DAT_00303282                                    XREF[1]:     FUN_0010118a:0010119e(R)  
        00303282                 undefined1 ??
                             DAT_00303283                                    XREF[1]:     FUN_00100f26:00100f3a(R)  
        00303283                 undefined1 ??
                                    .
                                    .
                                    .
                             DAT_003032a6                                    XREF[1]:     FUN_00100b6e:00100b82(R)  
        003032a6                 undefined1 ??
                             DAT_003032a7                                    XREF[1]:     FUN_001013ee:00101402(R)  
        003032a7                 undefined1 ??
                             DAT_003032a8                                    XREF[1]:     FUN_00100bb2:00100bc6(R)  
        003032a8                 undefined1 ??
                             DAT_003032a9                                    XREF[1]:     FUN_00100d8e:00100da2(R)  
        003032a9                 undefined1 ??

 ```
Maintenant on sait que le premier c'est *DAT_00303280* qui, dans la fonction *FUN_00100f6a:00100f7e*, check si le caractère est == à H. Le deuxième caractère de la chaine est *DAT_00303281* et check dans la fonction *FUN_00100dd2:00100de6* si le caractère est == à T, ainsi de suite.  
On obtient alors le flag !   
```py
a = [0x48,0x54,0x42,0x7b,0x68,0x30,0x70,0x70,0x31,0x6e,0x67,0x5f,0x74,0x68,0x72,0x75,0x5f,0x74,0x68,0x33,0x5f,0x73,0x70,0x34,0x63,0x33,0x5f,0x74,0x31,0x6d,0x33,0x5f,0x63,0x30,0x6e,0x74,0x31,0x6e,0x75,0x75,0x6d,0x21,0x7d]
res = []

for i in range(len(a)):
	res.append(chr(a[i]))
	
print("".join(res))
```   
**HTB{h0pp1ng_thru_th3_sp4c3_t1m3_c0nt1nuum!}**