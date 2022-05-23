# Rebuilding  
[rebuilding](https://github.com/mhoste51/writeups/tree/main/CyberApocalypse/Code-source/rebuilding)  
*Points : **300** / Solves : **463** / Difficulty : **easy***   
```
mathieu@MacBook-Air-de-Mathieu Downloads % file rebuilding 
rebuilding: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=4097a7628977579d82b46c5cebc04ef3d6d045d2, not stripped
```   
Lors de l'execution du programme avec le password "test" il nous retourne une erreur sur la taille du password.   
En faisant un strace on remarque qu'il y a 2 boucles, une pour la taille du mot de passe (?) et une de 6 pour chaque caractère.   
Cela me semble inutile alors j'ignore cet affichage et j'ouvre ghidra.   
```c
undefined8 main(int param_1,long param_2)

{
  int __c;
  size_t sVar1;
  undefined8 uVar2;
  int local_14;
  int local_10;
  int local_c;
  
  if (param_1 != 2) {
    puts("Missing required argument");
                    /* WARNING: Subroutine does not return */
    exit(-1);
  }
  local_14 = 0;
  sVar1 = strlen(*(char **)(param_2 + 8));
  if (sVar1 == 0x20) {
    for (local_10 = 0; local_10 < 0x20; local_10 = local_10 + 1) {
      printf("\rCalculating");
      for (local_c = 0; local_c < 6; local_c = local_c + 1) {
        if (local_c == local_10 % 6) {
          __c = 0x2e;
        }
        else {
          __c = 0x20;
        }
        putchar(__c);
      }
      fflush(stdout);
      local_14 = local_14 +
                 (uint)((byte)(encrypted[local_10] ^ key[local_10 % 6]) ==
                       *(byte *)((long)local_10 + *(long *)(param_2 + 8)));
      usleep(200000);
    }
    puts("");
    if (local_14 == 0x20) {
      puts("The password is correct");
      uVar2 = 0;
    }
    else {
      puts("The password is incorrect");
      uVar2 = 0xffffffff;
    }
  }
  else {
    puts("Password length is incorrect");
    uVar2 = 0xffffffff;
  }
  return uVar2;
}
```
J'ai donc bien fait car ghidra nous montre une comparaison de notre input avec une chaine xoré, en effet nous avons encrypted^key qui doit être égale à notre password.   
En regardant à l'adresse mémoire des deux chaines, on retrouve leurs valeurs.   
A partir de là nous pouvons écrire notre script :    
```py
encrypted = [0x29, 0x38,0x2b,0x1e,0x06,0x42,0x05,0x5d,0x07,0x02,0x31,0x42,0x0f,0x33,0x0a,0x55,0x00,0x00,0x15,0x1e,0x1c,0x06,0x1a,0x43,0x13,0x59,0x36,0x54,0x00,0x42,0x15,0x11]
key = "humans"
key=list(key.strip())

res = []
for i in range(len(encrypted)):
	res.append(chr(encrypted[i]^(ord(key[i%6]))))

print("".join(res))
```   
Je vais vite me rendre compte que le script est faux car il retourne le résultat suivant :   
**AMFh1m(jc_1gFg4ns}kqgt0{,[5n1}d**   
On a donc un problème ici, ça ne veut strictement rien dire.   
Je décide alors de regarder ce qu'il se passe avec radare2 en posant un breakpoint sur le xor.   
A la première itération on comprend vite pourquoi ça ne marche pas, la clef à été modifié avec la valeur "aliens".   
Il suffit alors de changer la valeur de la clef dans notre script et nous avons le flag : **HTB{h1d1ng_1n_c0nstruct0r5_1n1t}**   
En lisant le flag j'me suis mit a chercher où était la modification et en effet elle est en clair dans le constructeur _INIT_1