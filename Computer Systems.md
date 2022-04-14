# 深入理解计算机系统
## 第一章 计算机系统漫游
* 编译器的过程
  * 预处理（Pre-processor)：加载头文件。生成*.i文件
  * 编译：词法分析、语法分析、语义分析、中间代码生成、优化等，生成*.s文件
  * 汇编：翻译为机器码，*.o
  * 链接：将多个*.o的文件进行合并
* 文件是对IO设备的抽象，虚拟内存是对内存和磁盘IO的抽象，进程是对处理器、内存以及IO设备的抽象

***

## 第二章、信息的表示和处理
### 1、信息存储（Information Storage）
* 一个字节为8byte
* 十六进制以0x开始
* 32位和64位编译
  * gcc -m32 -o hello32 hello.c (可以同时运行在32位和64位的机器上)
  * gcc -m64 -o hello64 hello.c (只能运行再64位的机器上)

* C语言中各个类型所占用的位树
    <table>
        <tr>
            <th>类型</th>
            <th>32-bit (bytes)</th>
            <th>64-bit (bytes)</th>
        </tr>
        <tr>
            <td> char </td>
            <td> 1 </td>
            <td> 1 </td>
        </tr>
        <tr>
            <td> short </td>
            <td> 2 </td>
            <td> 2 </td>
        </tr>
        <tr>
            <td> int </td>
            <td> 4  </td>
            <td> 4  </td>
        </tr>
        <tr>
            <td> long </td>
            <td> 4  </td>
            <td> 8  </td>
        </tr>
        <tr>
            <td> int32_t </td>
            <td> 4  </td>
            <td> 4  </td>
        </tr>
        <tr>
            <td> int64_t </td>
            <td> 8  </td>
            <td> 8  </td>
        </tr>
        <tr>
            <td> char *</td>
            <td> 4  </td>
            <td> 8  </td>
        </tr>
        <tr>
            <td> float </td>
            <td> 4  </td>
            <td> 4  </td>
        </tr>
        <tr>
            <td> double </td>
            <td> 8  </td>
            <td> 8  </td>
        </tr>
    </table>
* 大端法和小端法
  * 大端法，存储方式符合人们的阅读，地址由低到高，依次存储数据的高位到低位
  * 小端法，最低有效字节存储在最前面
    ```c
        // 查看存储格式为大端还是小端的代码
        #include<stdio.h>
        typedef unsigned char *byte_pointer;
        void show_bytes(byte_pointer start, int len){
            int i;
            for (i = 0; i < len; i++>){
                printf(" %.2x", start[i]);
            }
            printf("\n");
        }

        void show_int(int x){
            show_bytes((byte_pointer) &x, sizeof(x));
        }

        int main(){
            show_int(12345);
            return 0;
        }
    ```



### 2、整数表示（Integer Representations）
### 3、整数运算（Integer Arithmetic）
### 4、浮点数（Floating Ponit）