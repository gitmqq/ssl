# openssl
将lib摆入到arm中的lib中

编译过程：
1、sudo -i
切换到root状态下
不然在config时会出现unable to create Makefile.new:权限不够
2、./config no-asm shared
3、配置完成后修改Makefile文件，CC=和--host=无效
#CC= gcc
CC= arm-none-linux-gnueabi-gcc

EX_LIBS=-ldl

#AR= ar $(ARFLAGS) r
AR= arm-none-linux-gnueabi-ar $(ARFLAGS) r

#RANLIB= /usr/bin/ranlib
RANLIB= arm-none-linux-gnueabi-ranlib

#NM= nm
NM= arm-none-linux-gnueabi-nm
4、make
5、make install
(默认生成的文件夹在/usr/local/ssl)

进入/usr/local/ssl/bin目录中，file openssl查看是否为arm上运行文件。

Text:
#include <stdio.h>
#include <openssl/sha.h>

int main ()
{
  SHA_CTX s;
  int i, size;
  char c[512];
  unsigned char hash[20];
  // 初始化 SHA Contex, 成功返回1,失败返回0
  SHA1_Init(&s);

  // 循环调用此函数,可以将不同的数据加在一起计算SHA1,成功返回1,失败返回0
  while ((size=read (0, c, 512)) > 0)
    SHA1_Update(&s, c, size);

  // 输出SHA1结果数据,成功返回1,失败返回0
  SHA1_Final(hash, &s);

  for (i=0; i < 20; i++)
    printf ("%.2x", (int)hash[i]);
  printf ("\n");
}


保存文件为text.c
交叉编译：arm-none-linux-gnueabi-gcc test.c -I/usr/local/ssl/include/ -L/usr/local/ssl/lib -lssl -lcrypto -ldl -o test.out

将编译后的文件放到arm上运行：./text.out < text.out
输出：fad20940b913687018241bbdd92902ccd5da187c
