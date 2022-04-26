# RocketLaunchValidator
```
file reverseRocket  
reverseRocket: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=f4286fa21a1e39d7015d83a38f75067679f81ea5, not stripped
```
Désassemblons le code binaire avec ghidra.  
Voici le main :  
```c
undefined8 main(int param_1,long param_2)

{
  int iVar1;
  undefined8 uVar2;
  long lVar3;
  char *__s;
  
  if (param_1 == 2) {
    lVar3 = ptrace(PTRACE_TRACEME,0);
    if (lVar3 < 0) {
      uVar2 = 1;
    }
    else {
      __s = (char *)calloc(0x20,1);
      iVar1 = decode_flag(__s,*(undefined8 *)(param_2 + 8));
      if (iVar1 == 0) {
        puts("You have flag! Here it is:");
        puts(__s);
        free(__s);
        uVar2 = 0;
      }
      else {
        uVar2 = 1;
      }
    }
  }
  else {
    uVar2 = 1;
  }
  return uVar2;
}
```
Rien de particulier ici, la fonction decode_flag attire l'attention car elle prend en paramètre la variable __s qui sera utilisé pour l'affichage du flag.  
Voici la fonction en question :  
```c
undefined8 decode_flag(undefined2 *param_1,char *param_2)

{
  size_t sVar1;
  undefined8 uVar2;
  
  sVar1 = strlen(param_2);
  if (sVar1 == 10) {
    if (*param_2 == 'C') {
      *param_1 = 0x434d;
      *(undefined *)(param_1 + 1) = 0;
      if (param_2[1] == '2') {
        *(undefined4 *)(param_1 + 1) = 0x7b4654;
        if (param_2[2] == 'I') {
          *(undefined2 *)((long)param_1 + 5) = 0x7254;
          *(undefined *)((long)param_1 + 7) = 0;
          if (param_2[3] == 'Y') {
            *(undefined2 *)((long)param_1 + 7) = 0x7634;
            *(undefined *)((long)param_1 + 9) = 0;
            if (param_2[4] == '3') {
              *(undefined2 *)((long)param_1 + 9) = 0x5f61;
              *(undefined *)((long)param_1 + 0xb) = 0;
              if (param_2[5] == 'X') {
                *(undefined2 *)((long)param_1 + 0xb) = 0x5f55;
                *(undefined *)((long)param_1 + 0xd) = 0;
                if (param_2[6] == 'P') {
                  *(undefined2 *)((long)param_1 + 0xd) = 0x3044;
                  *(undefined *)((long)param_1 + 0xf) = 0;
                  if (param_2[7] == 'Z') {
                    *(undefined2 *)((long)param_1 + 0xf) = 0x416d;
                    *(undefined *)((long)param_1 + 0x11) = 0;
                    if (param_2[8] == '9') {
                      *(undefined2 *)((long)param_1 + 0x11) = 0x2121;
                      *(undefined *)((long)param_1 + 0x13) = 0;
                      if (param_2[9] == 'A') {
                        *(undefined2 *)((long)param_1 + 0x13) = 0x7d;
                        uVar2 = 0;
                      }
                      else {
                        uVar2 = 1;
                      }
                   ...
  return uVar2;
}
```

Ici on obtient une suite de condition entre notre chaine de caractère entrée et des caractères simple.  
Rien de plus simple, pour reverse cette condition, il suffit simplement de reprendre le caractère comparé dans chaque if.  
On obtient alors C2IY3XPZ9  
La fonction va alors retourner uVar2 = 0.
```c
if (iVar1 == 0) {
    puts("You have flag! Here it is:");
    puts(__s);
    free(__s);
    uVar2 = 0;
}
```
Le flag est donc MCTF{C2IY3XPZ9}