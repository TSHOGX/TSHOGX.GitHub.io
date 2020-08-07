[toc]

# Lec 1

## 1. ca是什么

是为了更高的程序性能，It is about the hardware-software interface

c语言是最靠近硬件的语言，方便利用底层硬件、并行计算、节省功率

## 2. 计算机体系结构

![Old School Machine Structures](Lec%201/image-20200306101004016.png)

软件-指令集-硬件

![New-School Machine Structures](Lec%201/image-20200306101546544.png)

几个project的来源

## 3. 六个Great Ideas

1. Abstraction
2. Moore’s Law (Transistors / chip)按照未来的设计
3. Principle of Locality (Memory Hierarchy)
4. Parallelism (Amdahl’s Law)
5. Performance Measurement & Improvement
6. Dependability via Redundancy

![Why is Architecture Exciting Today?](Lec%201/image-20200306104605167.png)

CPU Clock Speed +15%/year --> CPU Speed Flat：受限于时钟速度和散热

之后增长的速度都来源于core的增加

## 4. 全都是数字

the value of $i$th digit $d$ is
$$
d \times Base^i
$$

* binary digit : bit
* least significant bit: RISC-V doubleword rightmost(第一位)
* most significant bit: leftmost(最后一位)
* RISC-V doubleword？: 64 bits long
* sign and magnitude: 表示符号的那一位bit，sign bit，0正1负
* Bit 31 is most significant, bit 0 is least significant

Hardware can be designed to add, subtract, multiply, and divide these binary bit patterns.

![Two’s complement representation](Lec%201/image-20200307094844654.png)

![看不懂](Lec%201/image-20200307095749763.png)

Binary (Base 2), Hexadecimal (Base 16), Decimal (Base 10) $1_{two}, 5_{ten}, 10_{hex}$

* Unsigned Integers: in 32 bit word represent 0 to 232-1 (4,294,967,295) (4 Gig)
* Signed Integers
	* Two’s complement treats 0 as positive, so 32-bit word represents 2^32 integers from -2^31 (–2,147,483,648) to 2^31-1 (2,147,483,647)

Ways to Make Two’s Complement：用N-bit的最大+1-正数=负数；bitwise complement后+1

![overflow](Lec%201/image-20200309131542032.png)

# Lec 2

![Components of a Computer](Lec%201/image-20200309132004360.png)

![Levels of
Representation/Interpretation](Lec%201/image-20200309132153480.png)

Key C concepts: Pointers, Arrays, Implications for Memory management

## 1. Compile vs. Interpret

C的编译器直接把c程序翻译成machine code给硬件（0、1）。assembling被自动隐秘的完成。Java依赖于bytecode；python翻译成code。

![两步：.c --> .o --> execute](Lec%201/image-20200309132648558.png)

优点：run-time快速；compilation快速（可用makefile使之只重编译修改过的、并行编译）

![缺点](Lec%201/image-20200309133344345.png)

## 2. 预处理器(CPP) 

![CPP](Lec%201/image-20200309133544420.png)

rules：全部大写，全部加括号；效果只是替换

![warning](Lec%201/image-20200309134018952.png)

所以避免使用macros，可以用function替代-e.g. inline function

NO or very tiny speedup.

## 3. 变量 (Consts/Enums)

integer: Depends on computer; 16 or 32 or 64 (target
processor works with most efficiently)

只需要满足: 

* sizeof(long long)
	≥ sizeof(long) ≥ sizeof(int) ≥ sizeof(short)
* short >= 16 bits, long >= 32 bits
* All could be 64 bits

---

constant：声明不可变的typed value

enums：声明一组相关的int constant

```c
const float golden_ratio = 1.618;
enum color {RED, GREEN, BLUE};
```

---

比较 “#define PI 3.14” and “const float pi=3.14”：速度一样占内存一样效果一样，define的无type。

---

所有variable declaration出现在使用之前（beginning of the block）

A variable may be initialized in its declaration; if not, it holds garbage!

![ex](Lec%201/image-20200309143241247.png)

---

C中没有Boolean type，用0（integer）和NULL（pointer）表示FALSE，其他都是TRUE。

---

运算符

![operators](Lec%201/image-20200309143620816.png)

## 4. 指针 pointer

一个address代表一块内存地址；pointer本身也存储在某个address表示的内存上，代表一个地址

```c
int *x; // x是一个pointer, 表示地址保存着一个int类型的数据
// *: 声明x是一个pointer类型value
x = &y; // 把y的地址赋给x，就是让x指向y
// &: address operator 获取一个数据的地址
z = *x; // 把x这个地址的数据赋给z
// *: 上述情况中是  dereference operator 获取访问一个地址中的数据
*x = 5;
```

---

C传递参数方式只有“by value”，所以别人只获得一个copy，修改copy无法改变原来的。所以为了在function内修改传入的参数，我们只能传入指向参数地址指针，从而通过指针访问外部的实际参数。

```c
void add_one (int *p) {
	*p = *p + 1;
}
int y = 3;
add_one(&y); // now y is 4
```

C++中可以“by reference”

```c++
void add_one (int &p) {
	p = p + 1; // or p += 1;
}
```

使用指针可以快速方便地传递巨大的struct和array

---

type: 一般来说pointer指向的内存地址存储固定的数据类型；`void *` 表示可以指向任何类型(generic pointer)；尽量避免使用

---

声明一个pointer时只分配了该指针所需的内存，没有分配内存给它所指向的数据

Local variables in C are not initialized, they may contain anything (aka “garbage”)

```c
void f(){
	int *ptr;
	*ptr = 5;
}
```

---

C structure不是“deep copy”，就是说所有的members都被拷贝了，但是指向它们的指针没有。

```c
typedef struct {
	int x;
	int y;
} Point;

Point p1;
Point p2;
Point *paddr;

/* dot notation */
int h = p1.x;
p2.y = p1.y;
/* arrow notation */
int h = paddr->x;
int h = (*paddr).x;
/* This works too */
p1 = p2;
```

---

指针的历史作用：一开始compiler非常愚蠢，人们需要告诉compiler使用哪一个register

## 5. 数组和指针

声明array时就是声明了一块内存，初始化时就给这块内存赋值

```c
int ar[2]; // 声明了两个integer大小的memory
int ar[] = {12,33};
```

---

string就是array of characters，最后一位是0 byte(aka “null terminator”)

```c
int strlen(char s[]){
	int n = 0;
	while (s[n] != 0) n++;
	return n;
}
```

---

Array variable is a “pointer” to the first (0th) element

所以`char *string` and `char string[]` 几乎是一样的声明，两者可以自由转换

* ar 是一个array类型变量
* ar[0]等效于*ar
* ar[2]等效于*(ar+2)

为了改变一个指针，我们可以创造一个指针的指针：Pass a pointer to a pointer, declared as **h

![wrong](Lec%201/image-20200309160321167.png)

![right](Lec%201/image-20200309160341404.png)

---

C的数组并不知道自己的长度，所以必须同时传入array和size，手动防止overflow

> Segmentation faults and bus errors:
> – These are VERY difficult to find; be careful!

![ex](Lec%201/image-20200309161406683.png)

---

Modern machines are “byte-addressable”: 硬件的memory由8-bit大小的cells组成，每一个cell有独特的地址，pointer就是这些内存地址的抽象

类型声明Type declaration：告诉编译器获取该pointer指向的地址上的数据时应该fetch多少bytes（e.g. 32-bit integer存储在4个consecutive 8-bit bytes中）

![Pointing to Different Size Objects](Lec%201/image-20200309174404564.png)

---

`sizeof(type)` returns number of bytes in object，而每个byte中的bits数量时不同的

By definition, `sizeof(char)==1`

Can take `sizeof(arr)`, or `sizeof(structtype)`

---

`pointer + 1`相当于在内存地址上增加`1*sizeof(char)` 

![interesting result](Lec%201/image-20200309175046099.png)
$$
a[i] \equiv *(a+i)
$$
array被传入function时就是作为pointer传入的（所以丢失了size）

![Passing arrays](Lec%201/image-20200309175505013.png)

上图sizeof(array)会输出4(32bit, 4byte)，就是一个指针的大小；但是sizeof(a)会输出40

```c
int *p;
int array[10];

for (p = array; p < &array[10]; p++){
	*p = …;
}
//相当于。。。
for (i = 0; i < 10; i++){
	array[i] = …;
}
```

---

strlen()

```c
int strlen(char *s)
{
	char *p = s;
	while (*p++)
		; /* Null body of while */
	return (p – s – 1);
}
```

![Point past end of array?](Lec%201/image-20200309181632525.png)

指针运算：

* legal：ptr+integer；在同一array中的两个ptr相减；比较(<, <=, ==, !=, >, >=)；与null比较
* illegal：其他所有，如两个ptr相加，相乘，integer-ptr

---

要获取主函数的参数：int main（int argc，char * argv []）

* argc：argument count 命令行上的字符串数（可执行文件计为1，每个参数加1）
* argv：argument vector 表示传入main函数的参数序列或指针
	* argv[0]：一定是程序的名称，并且包含了程序所在的完整路径
	* argv是指向指针的指针: char \*argv[]`等效于`char \**argv`

---

“Lowest High-level language”

# Lec 3

![image-20200309221045230](Lec%201/image-20200309221045230.png)

## C 内存管理

![C Memory Management](Lec%201/image-20200309221317752.png)

stack: 函数内声明的局部变量，return时释放 (temp variable) main()函数中的也算

> 如果不用malloc申请struct之类的内存，默认都会存放在stack里？

```c
struct stuff *TOM=(struct stuff *)malloc(sizeof(struct stuff));
```

heap: `malloc()`申请的动态内存

static data: 在程序运行开始时被声明，大小不会变化(指内存大小，实际表示的数据可以变化) (global variable)

code: 就是code

---

stack

![Stack](Lec%201/image-20200309225914150.png)

Last In, First Out (LIFO) data structure

每当一个function被调取，一个新的frame被存放在stack中，frame包含返回地址、程序、local variables。

frame连续的被存放在stack中，stack pointer指向最后那个

---

heap

* malloc() 申请一块未初始化内存
* calloc() 申请一块归零内存
* free() 释放内存块
* realloc() 修改之前分配的内存大小 (careful – it might move!)

---

`void *malloc(size_t n)` : 

* size_t是无符号整数类型，其大小足以“计数”内存字节
* n 代表申请的内存块大小(in bytes)
* sizeof 可以直接返回给定type的大小(in bytes) 比较方便
* 函数返回void *类型指针，NULL表示没有内存

```c
int *ip;
ip = (int *) malloc(sizeof(int)); //“Cast” operation, changes type of a variable. Here changes (void *) to (int *)

typedef struct { … } TreeNode;
TreeNode *tp = (TreeNode *) malloc(sizeof(TreeNode));
```

---

`void free(void *p)` :

p is pointer containing the address originally returned by malloc()

```c
int *ip;
ip = (int *) malloc(sizeof(int));
... .. ..
free((void*) ip); /* Can you free(ip) after ip++ ? NO WHY */

typedef struct {… } TreeNode;
TreeNode *tp = (TreeNode *) malloc(sizeof(TreeNode));
... .. ..
free((void *) tp);
```

注意检查NULL

```c
if ((ip = (int *) malloc(sizeof(int))) == NULL){
	printf(“\nMemory is FULL\n”);
	exit(1); /* Crash and burn! */
}
```

> When you free memory, you must be sure that you pass the **original address** returned from `malloc()` to `free()`; Otherwise, system exception (or worse)!

![Using Dynamic Memory](Lec%201/image-20200310091847391.png)

---

```c
void *realloc(void *ptr, size_t size)
```

- **ptr** -- 指针指向一个需要重新分配内存的内存块（之前是通过调用 malloc等得到的）如果本来就为NULL，则会分配一个新的内存块，且函数返回一个指向它的指针。
- **size** -- 内存块的新的大小，以**字节**byte为单位。如果大小为 0，且 ptr 指向一个已存在的内存块，则 ptr 所指向的内存块会被释放，并返回一个空指针。

`str = (char *) realloc(str, 25);`

---

**size_t**：无符号整数类型，它是 **sizeof** 关键字的结果。基本表示以byte为单位计数

---

