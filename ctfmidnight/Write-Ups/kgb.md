# Recrutement au KGB
[kgb](https://github.com/mhoste51/writeups/tree/main/ctfmidnight/code-source/kgb) 
```
file kgb
kgb: PE32+ executable (console) x86-64 (stripped to external PDB), for MS Windows
```
Encore un éxécutable x86, parfait pour désassembler avec mon outil pref : **ghidra**  
On obtient alors le main suivant :  
```c
undefined8 FUN_00401550(void)
{
  size_t sVar1;
  undefined8 uVar2;
  undefined8 local_58;
  undefined8 local_50;
  undefined8 local_48;
  undefined8 local_40;
  undefined8 local_38;
  undefined8 local_30;
  undefined2 local_28;
  undefined *local_18;
  int local_c;
  
  FUN_00401710();
  local_58 = 0;
  local_50 = 0;
  local_48 = 0;
  local_40 = 0;
  local_38 = 0;
  local_30 = 0;
  local_28 = 0;
  local_18 = &DAT_00404000;
  local_c = 0;
  puts("Hello. Please find me input that is flag.");
  scanf("%48s",&local_58);
  sVar1 = strlen((char *)&local_58);
  if (sVar1 == 0x18) {
    for (local_c = 0; local_c < 0x18; local_c = local_c + 1) {
      if ((local_18[local_c] ^ 0x42) != *(byte *)((longlong)&local_58 + (longlong)local_c)) {
        puts("Wrong. You not have flag.");
        return 1;
      }
    }
    printf("Congratulations. Bravo. You have flag. Here:\n%s\n",&local_58);
    uVar2 = 0;
  }
  else {
    puts("No. This is not flag.");
    uVar2 = 1;
  }
  return uVar2;
}
```
Le if (sVar == 0x18) nous indique que le mdp (plutôt le flag) sera de 24 caractères.  
Dans la boucle nous avons un autre if :  
```c
if ((local_18[local_c] ^ 0x42) != *(byte *)((longlong)&local_58 + (longlong)local_c)
```
Ce qui nous intéresse ici, c'est le local_58 qui est notre input. Pour obtenir la chaîne de caractères c'est ce que nous allons devoir reverse.  
On sait que a^b=c et c^b=a, nous devons alors faire local_18[]^0X42. Cela va nous servir à créer notre script.  
Or il nous manque la valeur de local_18 vous allez me dire. Allons donc chercher les valeurs entrées dans la mémoire.
```
local_18 = &DAT_00404000;
```
A l'adresse de DAT_00404000 nous avons les valeurs suivantes : 
```
                             DAT_00404000                    
        00404000 0f              ??         0Fh
        00404001 01              ??         01h
        00404002 16              ??         16h
        00404003 04              ??         04h
        00404004 39              ??         39h    9
        00404005 0c              ??         0Ch
        00404006 76              ??         76h    v
        00404007 36              ??         36h    6
        00404008 0a              ??         0Ah
        00404009 76              ??         76h    v
        0040400a 0c              ??         0Ch
        0040400b 1d              ??         1Dh
        0040400c 73              ??         73h    s
        0040400d 11              ??         11h
        0040400e 1d              ??         1Dh
        0040400f 16              ??         16h
        00404010 2a              ??         2Ah    *
        00404011 71              ??         71h    q
        00404012 1d              ??         1Dh
        00404013 00              ??         00h
        00404014 71              ??         71h    q
        00404015 77              ??         77h    w
        00404016 36              ??         36h    6
        00404017 3f              ??         3Fh    ?
```
Nous obtenons alors :
```
local18 = [0x0f, 0x01, 0x16, 0x04, 0x39, 0x0c, 0x76, 0x36, 0x0a, 0x76, 0x0c, 0x1d, 0x73, 0x11, 0x1d, 0x16, 0x2a, 0x71, 0x1d, 0x00, 0x71, 0x77, 0x36, 0x3f]
```
Nous avons donc tout ce qu'il nous faut pour reverse cet éxécutable :
```python
local18 = [0x0f, 0x01, 0x16, 0x04, 0x39, 0x0c, 0x76, 0x36, 0x0a, 0x76, 0x0c, 0x1d, 0x73, 0x11, 0x1d, 0x16, 0x2a, 0x71, 0x1d, 0x00, 0x71, 0x77, 0x36, 0x3f]

temp = []

for i in range(len(local18)):
    temp.append(chr((local18[i] ^ 0x42)))

print(temp)
print("".join(temp))
```
Ce script va alors nous afficher le flag *MCTF{N4tH4N_1S_Th3_B35t}*