# Le stagiaire 
[stagiaire.pyc](https://github.com/mhoste51/writeups/tree/main/ctfmidnight/code-source/stagiaire.pyc)  
Ce fichier est un fichier .pyc nous n'avons pas d'autres choix que d'utilises pycdc.
```c
┌──(kali㉿kali)-[~/Bureau/ctfmidnight]
└─$ ../../build/pycdc/pycdc stagiaire.pyc 
# Source Generated with Decompyle++
# File: stagiaire.pyc (Python 3.10)
```
Après décompilation, nous obtenons le main suivant :
```python
def main():
    user_input = input('Please enter the password : ')
    funcmap = {
        1: func1,
        2: func2,
        3: func3,
        4: func4 }
    if len(user_input) == 12:
        len(user_input)
        funcmap[1](user_input)
        return None
    if len(user_input) == 15:
        len(user_input)
        funcmap[2](user_input)
        return None
    if len(user_input) == 17:
        funcmap[3](user_input)
        return None
    if None == None:
        funcmap[4](user_input)
        return None
    looser()
```
Nous avons également 2 fonctions qui vont nous intéresser :  
La première va nous permettre d'obtenir le flag et la deuxième nous sert à déchiffrer ce flag.  
```python
def tryme(string):
    print(string)
    if string == 'NBUE|sStd^ShhnMN~':
        print('GG ! Use this password as flag')
        return None
    None()

def encrypt_string1(string):
    tmp_string = ''
    it = 1
    tmp_string += chr(ord(letter) - 1)
    it = 1
    continue
    return tmp_string
```
Nous allons donc devoir reverse la fonction encrypt_string. On remarque qu'un caractère sur deux est décalé de -1 et l'autre est décale de +1.  
Pour reverse cet algo, rien de plus simple que ce script :  
```python
def decrypt_string(string):
    flag = []
    for i in range(len(string)):
        if i %2 != 0:
            flag.append(chr(ord(string[i])+1))
        else:
            flag.append(chr(ord(string[i]) - 1))
    return flag


string = "NBUE|sStd^ShhnMN~"
flag = decrypt_string(string)
print("".join(flag))
```
Ce script nous affiche alors le flag : *MCTF{tRuc_RigoLO}*