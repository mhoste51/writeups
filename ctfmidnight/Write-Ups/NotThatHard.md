# NotThatHard
[NotThatHard](https://github.com/mhoste51/writeups/tree/main/ctfmidnight/code-source/reverseNotThatHard) 
```
file reverseNotThatHard
reverseNotThatHard: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=aaa7245cda21b424d1bcf061547f0ab2b73edc04, for GNU/Linux 3.2.0, not stripped
```
Il s'agit d'un binaire ELF 64-bit executable complètement normal.  
```
chmod +x reverseNotThatHard
```

Cet executable nous demande un mot de passe, avec "aaa" il nous renvois wrong password.
Désassemblons ce fichier avec ghidra :

```c
undefined8 main(int param_1)

{
size_t sVar1;
long in_FS_OFFSET;
int local_100;
int local_fc;
int local_f8 [12];
undefined4 local_c8 [14];
byte abStack144 [12];
undefined8 local_84;
undefined4 local_7c;
char local_78 [104];
long local_10;

local_10 = *(long *)(in_FS_OFFSET + 0x28);
if (param_1 == 1) {
    local_f8[0] = 0x10;
    local_f8[1] = 0x2b;
    local_f8[2] = 0xffffffff;
    local_f8[3] = 0xffffffec;
    local_f8[4] = 0x17;
    local_f8[5] = 0x13;
    local_f8[6] = 0x3f;
    local_f8[7] = 0xfffffffd;
    local_f8[8] = 0x20;
    local_f8[9] = 0xfffffffb;
    local_f8[10] = 0x30;
    local_f8[11] = 0xffffffda;
    local_c8[0] = 0x1b;
    local_c8[1] = 0x30;
    local_c8[2] = 6;
    local_c8[3] = 0x3a;
    local_c8[4] = 0x10;
    local_c8[5] = 9;
    local_c8[6] = 0x13;
    local_c8[7] = 0x14;
    local_c8[8] = 0x3a;
    local_c8[9] = 0x57;
    local_c8[10] = 0x48;
    local_c8[11] = 0x17;
    local_84 = 0x67616c665f746f6e;
    local_7c = 0x282d3a5f;
    puts("Give me your password : ");
    __isoc99_scanf(&DAT_00102045,local_78);
    sVar1 = strlen(local_78);
    if (sVar1 == 0xc) {
    for (local_100 = 0; local_100 < 0xc; local_100 = local_100 + 1) {
        abStack144[local_100] =
            *(byte *)((long)&local_84 + (long)local_100) ^ (byte)local_c8[local_100];
    }
    for (local_fc = 0; local_fc < 0xc; local_fc = local_fc + 1) {
        if ((int)(char)abStack144[local_fc] - (int)local_78[local_fc] != local_f8[local_fc]) {
        puts("Wrong password.");
        goto LAB_00101431;
        }
    }
    printf("Good job ! Use this password as flag : %s\n",local_78);
    }
    else {
    puts("Wrong password.");
    }
}
else {
    puts("Please launch the app without args.");
}
LAB_00101431:
if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
}
return 0;
}
```

Dans la fonction main on remarque deux tableaux qui sont initialisés : local_f8 et local_c8 avec une taille de 12.  
La condition 
```c
if (sVarl == 0xc) 
```

nous confirme que la taille du mot de passe est de 12.  
On remarque ensuite un xor dans la première boucle for puis une vérification de ce xor dans la deuxième boucle for.  

Le tableau local_c8 nous sert donc à obtenir une chaine xoré qui sera comparé à local_f8.
local_84 et local_7c vont nous servir pour le xor.  
La chaine xoré s'obtient de la manière suivante : (&local_84+i)^(local_c8[i]).
```c
for (local_100 = 0; local_100 < 0xc; local_100 = local_100 + 1) {
    abStack144[local_100] =
        *(byte *)((long)&local_84 + (long)local_100) ^ (byte)local_c8[local_100];
}
```

On a donc ici c =a^b  (^ représente XOR)

Puis nous avons la vérification du password dans la deuxième boucle for :
```c
for (local_fc = 0; local_fc < 0xc; local_fc = local_fc + 1) {
    if ((int)(char)abStack144[local_fc] - (int)local_78[local_fc] != local_f8[local_fc]) {
    puts("Wrong password.");
    goto LAB_00101431;
    }
}
```

On a ici a - b = c.

Chaque caractère de local_f8 doit être égale à la différence entre notre chaîne xorée avec le mot de passe entré.
Nous pouvons donc reverse cet algo afin d'obtenir le mot de passe :

a-b=c --> a-c=b 

Pour comprendre comment &local_84+i fonctionne, ouvrons radare2 : 
```
    radare2 -d reverseNotThatHard 
    aaa
    afl
    pdf @main
```

On pose un breakpoint sur le xor afin d'afficher les valeurs stockés dans la mémoire à chaque itération de la boucle
```
    dp 0x0000138c
    dc
    dr
```

Dc nous permet de run le programme jusqu'au prochain breakpoint. Une fois le breakpoint touché, dr va nous permettre d'afficher les valeurs enregistrés en mémoire.  
Sur le première itération on retrouve donc rdx = 6e  
Sur la deuxième itération, rdx = 6f  
local_84 va donc se lire dans ce sens : 6e 6f 74 5f 66 6c 61 67  
A la 9ème itération, ce sera local_7c qui sera utilisé : 5f 3a 2d 28  
Je ne vais pas expliquer ici comment XOR fonctionne, nous allons plutôt faire un script :  

```python
import string

local84 = [0x28, 0x2d, 0x3a, 0x5f, 0x67, 0x61, 0x6c, 0x66, 0x5f, 0x74, 0x6f, 0x6e]

localc8 = [0x1b, 0x30, 6, 0x3a, 0x10, 9, 0x13, 0x14, 0x3a, 0x57, 0x48, 0x17]

# xor -- txt
temp = []
for i in range(len(local84)):
    temp.append(local84[-(i+1)] ^ localc8[i])
print(temp)

localf8 = [0x10, 0x2b, -1, -20, 0x17, 0x13, 0x3f, -3, 0x20, -5, 0x30, -38]

temp2 = []
for i in range(len(temp)):
    temp2.append(chr(temp[i] - localf8[i]))

print("".join(temp2))
```

Qui va donc retourner e4sy_R3vEr5e pour avoir le flag *MCTF{e4sy_R3vEr5e}*