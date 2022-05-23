# Omega One  
[omega-One](https://(https://github.com/mhoste51/writeups/tree/main/CyberApocalypse/Code-source/omega-one)  
*Points : **300** / Solves : **509** / Difficulty : **easy***   
Le fichier zip nous donne deux fichiers : omega-one et output.txt.  
```
mathieu@MacBook-Air-de-Mathieu rev_omega_one % file omega-one 
omega-one: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=002fdb38356a09bdeee2cf4b9ce0b41b4b8c53d9, stripped
``` 
Le fichier est donc un executable x86 que nous allons debugger avec radare2.    
``` 
[0x000009e0]> pdf @main
Do you want to print 515 lines? (y/N) y
            ; DATA XREF from entry0 @ 0x9fd
┌ 2766: int main (int argc, char **argv, char **envp);
│           0x00000b4c      55             push rbp
│           0x00000b4d      4889e5         mov rbp, rsp
│           0x00000b50      bf04000000     mov edi, 4                  ; size_t arg1
│           0x00000b55      e8190b0000     call fcn.00001673
│           0x00000b5a      488905b72420.  mov qword [0x00203018], rax ; [0x203018:8]=0
│           0x00000b61      488d3d82ffff.  lea rdi, [0x00000aea]
│           0x00000b68      e8b3150000     call fcn.00002120
│           0x00000b6d      488b05a42420.  mov rax, qword [0x00203018] ; [0x203018:8]=0
│           0x00000b74      488d15c51500.  lea rdx, str.Lendrens       ; 0x2140 ; "Lendrens" ; char *arg3
│           0x00000b7b      488d35c71500.  lea rsi, [0x00002149]       ; "k" ; char *arg2
│           0x00000b82      4889c7         mov rdi, rax                ; int64_t arg1
│           0x00000b85      e8e60c0000     call fcn.00001870
│           0x00000b8a      488b05872420.  mov rax, qword [0x00203018] ; [0x203018:8]=0
│           0x00000b91      488d15b31500.  lea rdx, str.Thauvi         ; 0x214b ; "Thauv'i" ; char *arg3
│           0x00000b98      488d35b41500.  lea rsi, [0x00002153]       ; "d" ; char *arg2
│           0x00000b9f      4889c7         mov rdi, rax                ; int64_t arg1
│           0x00000ba2      e8c90c0000     call fcn.00001870
│           0x00000ba7      488b056a2420.  mov rax, qword [0x00203018] ; [0x203018:8]=0
│           0x00000bae      488d15a01500.  lea rdx, str.Throrqiek      ; 0x2155 ; "Throrqiek" ; char *arg3
│           0x00000bb5      488d35a31500.  lea rsi, [0x0000215f]       ; "P" ; char *arg2
│           0x00000bbc      4889c7         mov rdi, rax                ; int64_t arg1
│           0x00000bbf      e8ac0c0000     call fcn.00001870
│           0x00000bc4      488b054d2420.  mov rax, qword [0x00203018] ; [0x203018:8]=0
│           0x00000bcb      488d158f1500.  lea rdx, str.Inqods         ; 0x2161 ; "Inqods" ; char *arg3
│           0x00000bd2      488d358f1500.  lea rsi, [0x00002168]       ; "e" ; char *arg2
│           0x00000bd9      4889c7         mov rdi, rax                ; int64_t arg1
│           0x00000bdc      e88f0c0000     call fcn.00001870
│           0x00000be1      488b05302420.  mov rax, qword [0x00203018] ; [0x203018:8]=0
│           0x00000be8      488d157b1500.  lea rdx, str.Tarquts        ; 0x216a ; "Tarquts" ; char *arg3
│           0x00000bef      488d357c1500.  lea rsi, [0x00002172]       ; "6" ; char *arg2
│           0x00000bf6      4889c7         mov rdi, rax                ; int64_t arg1
                                            .
                                            .
                                            .
│           0x000015d4      e897020000     call fcn.00001870
│           0x000015d9      488b05381a20.  mov rax, qword [0x00203018] ; [0x203018:8]=0
│           0x000015e0      488d15ab0e00.  lea rdx, str.Drercieks      ; 0x2492 ; "Drercieks" ; char *arg3
│           0x000015e7      488d35ae0e00.  lea rsi, [0x0000249c]       ; "9" ; char *arg2
│           0x000015ee      4889c7         mov rdi, rax                ; int64_t arg1
│           0x000015f1      e87a020000     call fcn.00001870
│           0x000015f6      488b051b1a20.  mov rax, qword [0x00203018] ; [0x203018:8]=0
│           0x000015fd      488d159a0e00.  lea rdx, str.Statars        ; 0x249e ; "Statars" ; char *arg3
│           0x00001604      488d359b0e00.  lea rsi, [0x000024a6]       ; "j" ; char *arg2
│           0x0000160b      4889c7         mov rdi, rax                ; int64_t arg1
│           0x0000160e      e85d020000     call fcn.00001870
│           0x00001613      b800000000     mov eax, 0
│           0x00001618      5d             pop rbp
└           0x00001619      c3             ret
``` 
Chaque nom se voit attribué un caractère par exemple Lendrens -> k.   
On comprend donc vite que la suite des caractères sera notre flag, cependant nous avons un ordre incohérent.   
Deux possibilité pour remettre tout dans l'ordre, faire une analyse dynamique (debogguer) ou tout simplement ouvrir le fichier output.txt qui contient une suite de noms dans un ordre précis :   
```
Crerceon    
Ezains  
Ummuh   
Zonnu   
Vinzo   
Cuzads  
Emoi    
Ohols   
Groz'ens    
Ukox    
Ehnu    
Pheilons    
Cuzads  
Khehlan 
Ohols   
Ehnu    
Munis   
Inphas  
Pheilons    
Ehnu    
Dut 
Ukox    
Ohols   
Pheilons    
Pheilons    
Zimil   
Ehnu    
Honzor  
Vinzo   
Ukteils 
Falnain 
Dhohmu  
Baadix  
```
Ce fichier nous montre le résultat final de l'éxecution du programme.  
Commençons à récuperer chaque caractère qui correspond à son nom.   
* Crerceon -> H
* Ezains -> T
* Ummuh -> B
* Zonnu -> {   
Cela correspond bien à notre flag !   
**HTB{l1n34r_t1m3_but_pr3tty_sl0w!}**