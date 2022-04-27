# corruption
```
file corruption
corruption: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=b83e898d5373971586776ef960eac000fbfd290a, not stripped
```
Encore un éxécutable x86 que nous allons désassembler avec ghidra.  
```c
undefined8 main(void)

{
  int iVar1;
  char *__ptr;
  void *__ptr_00;
  
  __ptr = (char *)calloc(0xffffff,1);
  __ptr_00 = calloc(0x20,1);
  puts("Enter number:");
  iVar1 = getint(__ptr_00,0x1f);
  if (iVar1 == 0) {
    exit_with_insult("This is not number");
  }
  iVar1 = parseint(__ptr_00);
  iVar1 = iVar1 + 0x133769;
  if ((0xffffff < iVar1) || (iVar1 < 0)) {
    exit_with_insult("You fail");
  }
  __ptr[iVar1] = 'B';
  if (*__ptr != 'B') {
    exit_with_insult("You fail");
  }
  puts("Ok. Cool. Bravo.");
  print_flag(__ptr_00);
  free(__ptr);
  free(__ptr_00);
  return 0;
}
```
La seul condition pour que l'appel à print_flag se fasse c'est d'avoir : *__ptr[0] = 'B'*  
On cherche alors à avoir iVar = 0 : 
```c
iVar1 = parseint(__ptr_00);
iVar1 = iVar1 + 0x133769;
```
On se retrouve avec :  
iVar = input + 0x133769  
On cherche donc : iVar + 0xffffffff = 0.  
0xffffffff = -1 en hexa sur 4 octets. En C un int est sur 4 octets.  
Nous obtenons alors -1, il nous reste plus qu'à ajouter +1 pour obtenir 0.    
Avec toutes ces infos nous pouvons écrire notre mini script pour reverse l'algo.  
```python
input = 0xffffffff-0x133769+1
print(f"{input=}")
```
Ca va nous retourner *input=4293707927*.  
Je peux désormais éxécuter le programme avec comme input la valeur trouvée précedemment.  
```
Ok. Cool. Bravo.
Here is flag: MCTF{R4b0t41_s_N@m1:)}
```
Bingo nous avons le flag !