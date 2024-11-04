# C
<details><summary>Nhấp vào đây để xem chi tiết</summary>
<p>

<details><summary><b>1. Compiler</b></summary>
<p>

<details><summary><b>1.1. Giới thiệu về compiler</b></summary>
<p>

Các ngôn ngữ lập trình thường dùng như C/C++, Java, Python, C#... được gọi là ngôn ngữ bậc cao. Khi code xong nhấn run thì chương trình chạy trên monitor, tưởng chừng như rất đơn giản nhưng thực chất phải trải qua rất nhiều bước xử lý phức tạp.

Ngoài ra, để viết code thì thường sử dụng các IDE (Arduino IDE, VSCode, PyCharm, Android Studio,...). Các IDE thường sẽ tích hợp 2 thứ:

- Editor: trình soạn thảo văn bản, cung cấp giao diện để viết và chỉnh sửa mã nguồn.
- Compiler: thực hiện chuyển đổi các ngôn ngữ bậc cao sang ngôn ngữ máy (0 và 1) để máy tính có thể hiểu được. Mỗi ngôn ngữ lập trình đều sẽ có một bộ biên dịch khác nhau, ví dụ: trong Arduino IDE thì sử dụng AVR-gcc, C/C++ sử dụng gcc/g++,...

Quá trình biên dịch của gcc sẽ trải qua 4 bước chính:

- Preprocessor
- Compiler
- Assembler
- Linker

![image](https://github.com/user-attachments/assets/a6150e07-e937-45c3-bf4f-0ee195113ff5)

</p>
</details>

<details><summary><b>1.2. Compiler process</b></summary>
<p>

Quá trình biên dịch một chương trình C sẽ trải qua 4 bước theo thứ tự như sau:

<details><summary><b>📚 Quá trình tiền xử lý (Preprocessor)</b></summary>
<p>

- Nhận mã nguồn.
- Xóa bỏ tất cả chú thích (comment) trong chương trình.
- Các đoạn mã khi được thêm vào (#include) sẽ được copy vào file .i
- Các định nghĩa (#define) cũng sẽ được thay thế trong file .i
- Câu lệnh: ``` gcc -E file.c -o file.i ```

💻
File **test.c**
```cpp
#include <stdio.h>

void display(){
    printf("This is file test.c\n");
}
```

File **main.c**
```cpp
#include <stdio.h>
#include "test.c"

// create size array
#define SIZE 20
int array[SIZE];

// create the function
void test(){
    printf("Hello world\n");
}

int SIZE2 = 30;

int main(int argc, char const *argv[]){
    int array2[SIZE2];
    display();
    test();
    return 0;
}
```

📝 Thực hiện câu lệnh để tạo file main.i: ``` gcc -E main.c -o main.i ```

File **main.i**
```cpp
# 1650 "D:/C++/msys64/ucrt64/include/stdio.h" 2 3
# 2 "main.c" 2
# 1 "test.c" 1


# 3 "test.c"
void display(){
    printf("This is file test.c\n");
}
# 3 "main.c" 2

int array[20];

void test(){
    printf("Hello world\n");
}

int SIZE2 = 30;

int main(int argc, char const *argv[])
{
    int array2[SIZE2];
    display();
    test();
    return 0;
}
```
📝 Toàn bộ source của thư viện stdio và file test.c đều được copy vào file main.i

📝 2 comment đều đã bị xóa.

📝 Định nghĩa ``` SIZE ``` cũng được thay thế bằng giá trị 20 -> ``` int array[20] ```

<br>

</p>
</details>

<details><summary><b>📚 Quá trình dịch ngôn ngữ bậc cao sang Assembly (Compiler)</b></summary>
<p>

- Phân tích cú pháp (syntax) sau đó chuyển sang Assembly code – hợp ngữ, là ngôn ngữ bậc thấp gần với tập lệnh của bộ vi xử lý.
- Câu lệnh: ``` gcc -S main.i -o main.s ```

File **main.s**
```cpp
	.file	"main.c"
	.text
	.section .rdata,"dr"
.LC0:
	.ascii "This is file test.c\0"
	.text
	.globl	display
	.def	display;	.scl	2;	.type	32;	.endef
	.seh_proc	display
display:
	pushq	%rbp
	.seh_pushreg	%rbp
	movq	%rsp, %rbp
	.seh_setframe	%rbp, 0
	subq	$32, %rsp
	.seh_stackalloc	32
	.seh_endprologue
	leaq	.LC0(%rip), %rax
	movq	%rax, %rcx
	call	puts
	nop
	addq	$32, %rsp
	popq	%rbp
	ret
	.seh_endproc
	.globl	array
	.bss
	.align 32
...
```

<br>

</p>
</details>

<details><summary><b>📚 Quá trình dịch Assembly sang ngôn ngữ máy (Assembler)</b></summary>
<p>

- Dịch chương trình sang mã máy 0 và 1.
- Một tệp mã máy (.obj) được sinh ra trong hệ thống sau đó.
- Câu lệnh: ``` gcc -c main.s -o main.o ```

File **main.o**
![image](https://github.com/user-attachments/assets/42711a58-af7e-4fd9-aece-795dda6cec99)

📝 Khi code trên VĐK thì đây chính là chương trình sẽ ghi vào bộ nhớ Flash của VĐK. Khi cấp nguồn cho VĐK thì nó sẽ tiến hành khởi tạo các PC, Stack Pointer và copy chương trình này vào bộ nhớ Flash và RAM rồi sau đó mới bắt đầu đi đến từng địa chỉ để thực thi.

<br>

</p>
</details>

<details><summary><b>📚 Quá trình liên kết (Linker)</b></summary>
<p>

- Trong giai đoạn này mã máy của một chương trình dịch từ nhiều nguồn (file .c hoặc file thư viện .lib) được liên kết lại với nhau để tạo thành chương trình đích duy nhất.
- Mã máy của các hàm thư viện gọi trong chương trình cũng được đưa vào chương trình cuối trong giai đoạn này.
- Các lỗi liên quan đến việc gọi hàm hay sử dụng biến tổng thể mà không tồn tại sẽ bị phát hiện. Kể cả lỗi viết chương trình chính không có hàm main() cũng được phát hiện trong liên kết.
- Câu lệnh: ``` gcc main.o -o main ```

<br>

</p>
</details>

</p>
</details>

</p>
</details>

<br>

<details><summary><b> 2. Macro</b></summary>
<p>

<details><summary><b>2.1. Giới thiệu về Macro</b></summary>
<p>

- Macro là từ dùng để chỉ những thông tin được xử lý ở quá trình tiền xử lý (Preprocessor), được sử dụng để thay thế một đoạn mã trước khi chương trình được biên dịch.
- Macro giúp tạo ra các định nghĩa chung, thường để tránh việc viết lại mã nhiều lần hoặc để làm cho mã dễ bảo trì hơn. 

</p>
</details>

<details><summary><b>2.2. Các nhóm chỉ thị Macro</b></summary>
<p>

<details><summary><b>📚 Chi thị bao hàm tệp (#include)</b></summary>
<p>

- Chỉ thị #include dùng để chèn nội dung của một file khác vào mã nguồn chương trình.
- Tái sử dụng mã nguồn.
- Phân chia chương trình thành các phần nhỏ, giúp quản lý mã nguồn hiệu quả.
- Khi sử dụng **<>** thì preprocessor sẽ thêm nội dung những file.h trong thư mục cài đặt. 

```cpp
#include <stdio.h>
#include <stdlib.h>
```
- Khi sử dụng dấu **""**, bộ tiền xử lý sẽ tìm file name trong thư mục chứa project. Nếu tìm không thấy thì nó sẽ tiếp tục tìm trong các file có sẵn trong thư mục cài đặt.

```cpp
#include "uart.h"
#include "spi.h"
```

<br>

</p>
</details>

<details><summary><b>📚 Chi thị định nghĩa Macro (#define)</b></summary>
<p>

Chỉ thị #define dùng để thay thế một chuỗi mã nguồn bằng một chuỗi khác trước khi chương trình biên dịch. Nó giúp giảm lặp lại mã, dễ bảo trì chương trình.

💻
File **main.c**
```cpp
#include <stdio.h>

#define CREATE_FUNC(name,cmd)   \
void name(){                    \
    printf(cmd);                \
}

CREATE_FUNC(test1, "this is function test1()\n");
CREATE_FUNC(test2, "this is function test2()\n");
CREATE_FUNC(test3, "this is function test3()\n");

int main(int argc, char const *argv[])
{
    test1();
    test2();
    test3();
    return 0;
}
```
File **main.i**
```cpp
# 8 "main.c"
void test1(){ printf("this is function test1()\n"); };
void test2(){ printf("this is function test2()\n"); };
void test3(){ printf("this is function test3()\n"); };

int main(int argc, char const *argv[])
{
    test1();
    test2();
    test3();
    return 0;
}
```

<br>

💻
```cpp
// Macro
#define sum(a,b) a+b
```
```cpp
// Function
int sum(int a, int b){ 
	return a+b;
}
```
📝 Cả 2 đều cho ra kết quả giống nhau. Vậy câu hỏi đặt ra là:

🤔 Sự khác nhau giữa Macro và Function là gì❓ 

🤔 Khi nào sử dụng Macro? Khi nào sử dụng Function❓ 

<br>

**Sử dụng Function**
```cpp
#include <stdio.h>

int sum(int a, int b){   // 0xc1 - 0xc9
    return a+b;
}

int main(int argc, char const *argv[]){
    int a;         // 0x01              
    int b;         // 0x05
    int c = a+b;   // 0x09
    sum(5,7);       
    return 0;
}
```
📝 Khi sử dụng hàm thì compiler sẽ cấp 1 vùng nhớ cho hàm (giả sử 0xc1 – 0xc9).

📝 Trong hàm main(), PC sẽ đi đến từng địa chỉ: 0x01 -> 0x02 -> 0x03 -> 0x04 ->... -> 0x09. Khi đến hàm sum(), địa chỉ 0x0A sẽ được lưu vào main stack pointer, sau đó PC nhảy đến 0xc1 để thực hiện sum.

📝 Khi thực hiện xong hàm sum() thì sẽ vào main stack pointer và lấy lại địa chỉ 0x0A để PC tiếp tục thực thi từ đó.

<br>

**Sử dụng Macro**
```cpp
#include <stdio.h>

#define sum(a,b) a+b        

int main(int argc, char const *argv[])
{
    int a;         // 0x01              
    int b;         // 0x05
    int c = a+b;   // 0x09
    5+7;           // 0x0A
    5+4;           // 0x0B
    return 0;
}
```
📝 Khi sử dụng Macro thì PC sẽ trỏ đến những địa chỉ liền kề nhau do đó sẽ tốn bộ nhớ để lưu giá trị sau mỗi lần gọi macro nhưng tốc độ xử lý sẽ nhanh hơn khi sử dụng hàm do không cần phải lưu vào main stack pointer.

<br>

<table>
  <tr>
    <th style="text-align: center;">Macro</th>
    <th style="text-align: center;">Function</th>
  </tr>
  <tr>
    <td style="text-align: center;">Tốn nhiều bộ nhớ để lưu chương trình</td>
    <td style="text-align: center;">Không tốn nhiều bộ nhớ trên RAM</td>
  </tr>
  <tr>
    <td style="text-align: center;">Tốc độ xử lý nhanh</td>
    <td style="text-align: center;">Tốc độ xử lý chậm</td>
  </tr>
</table>

<br>

</p>
</details>

<details><summary><b>📚 Chi thị hủy định nghĩa Macro (#undef)</b></summary>
<p>

Chỉ thị #undef dùng để hủy định nghĩa của một macro đã được định nghĩa trước đó bằng #define.

💻
```cpp
#include <stdio.h>

#define data 30

int main(int argc, char const *argv[])
{
    printf("Data = %d\n", data);

    #undef data

    #define data 50

    printf("Data = %d\n", data);

    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>📚 Chỉ thị biên dịch có điều kiện (#if, #elif, #else, #ifdef, #ifndef)</b></summary>
<p>

- Chỉ thị #ifdef dùng để kiểm tra một macro đã được định nghĩa hay chưa, nếu macro đã được định nghĩa thì mã nguồn sau #ifdef sẽ được biên dịch.
- Chỉ thị #ifndef dùng để kiểm tra một macro đã được định nghĩa hay chưa, nếu macro chưa được định nghĩa thì mã nguồn sau #ifndef sẽ được biên dịch.

💻
```cpp
#include <stdio.h>

//#define SIZE 30

#ifndef SIZE
#define SIZE 5

int arr[SIZE] = {1,2,3,4,5};

#endif

int main(int argc, char const *argv[])
{

    for (int i=0; i<SIZE; i++){
        printf("arr[%d] = %d\n", i, arr[i]);
    }

    return 0;
}
```

<br>

- Chỉ thị #if sử dụng để bắt đầu một điều kiện tiền xử lý.Nếu điều kiện trong #if là đúng, các dòng mã nguồn sau #if sẽ được biên dịch. Nếu sai, các dòng mã nguồn sẽ bị bỏ qua đến khi gặp #endif.
- Chỉ thị #elif dùng để thêm một điều kiện mới khi điều kiện trước đó trong #if hoặc #elif là sai.
- Chỉ thị #else dùng khi không có điều kiện nào ở trên đúng.

🤔 Muốn build một source để có thể nạp cho nhiều chip, thay vì mỗi con chip viết một source thì mình có thể sử dụng ``` #if, #elif, #else ```

💻
```cpp
#include <stdio.h>

typedef enum{
    LOW,
    HIGH
} Status;

typedef enum{
    PIN0,
    PIN1,
    PIN2,
    PIN3,
    PIN4,
    PIN5,
    PIN6,
    PIN7,
} Pin;

#define ESP32      1
#define STM32      2
#define ATmega324  3

#define MCU STM32

int main(int argc, char const *argv[])
{
    while(1){
        #if MCU == STM32
            void digitalWrite(Pin pin, Status state) {
                if (state == HIGH){
                    GPIOA->BSRR = (1 << pin);  // Đặt bit tương ứng để thiết lập chân
                } 
                else {
                    GPIOA->BSRR = (1 << (pin + 16));  // Đặt bit tương ứng để reset chân
                }
            }

        #elif MCU == ESP32
            void digitalWrite(Pin pin, Status state) {
                if (state == HIGH) {
                    GPIO.out_w1ts = (1 << pin);  // Đặt bit tương ứng để thiết lập chân HIGH
                } 
                else {
                    GPIO.out_w1tc = (1 << pin);  // Đặt bit tương ứng để reset chân LOW
                }
            }

        #else
            void digitalWrite(Pin pin, Status state) {
                if (state == HIGH) {
                    PORTA |= (1 << pin);  // Đặt bit tương ứng để thiết lập chân HIGH
                } 
                else {
                    PORTA &= ~(1 << pin);  // Xóa bit tương ứng để reset chân LOW
                }
            }
            
        #endif
    }
    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>📚 Một số toán tử Macro</b></summary>
<p>

- Sử dụng **##** để nối chuỗi.
- Sử dụng **#** để chuẩn hóa đoạn văn bản lên chuỗi.

💻
File **main.c**
```cpp
#include <stdio.h>

#define CREATE_VAR(name)    \
int    int_##name;          \
char   char_##name;         \
double double_##name     

// ## dùng để nối chuỗi

#define CREATE_STRING(cmd) printf(#cmd)

// # chuẩn hóa đoạn văn bản lên chuỗi

int main(int argc, char const *argv[])
{
    CREATE_VAR(test);  
    CREATE_STRING(abc);
    return 0;
}
```
File **main.i**
```cpp
# 20 "main.c"
int main(int argc, char const *argv[])
{
    int int_test; char char_test; double double_test;
    printf("abc");
    return 0;
}
```

<br>

- Variadic macro: là một dạng macro cho phép nhận một số lượng biến tham số có thể thay đổi.

💻 Tính tổng
```cpp
#define sum(a,b) a+b

#define sum(a,b,c) a+b+c
```
📝 Khi tính tổng 2 số thì dùng ``` sum(a,b) ```

📝 Khi tính tổng 3 số thì dùng ``` sum(a,b,c) ```

🤔 Vậy muốn tính tổng nhiều số thì hàm sum thay đổi thế nào❓ 

➡️Sử dụng variadic macro để giải quyết vấn đề trên.

```cpp
#include <stdio.h>

#define sum(...)                        \
do{                                     \
    int arr[] = {__VA_ARGS__, 0};       \
    int tong = 0;                       \
    int i = 0;                          \
    while (arr[i] != 0) {               \
        tong += arr[i];                 \
        i++;                            \
    }                                   \
    printf("Tong = %d\n", tong);        \
} while (0)

int main(int argc, char const *argv[]) {
    sum(1, 2);                          
    sum(1, 2, 3);                       
    sum(1, 3, 5, 7, 9, 10, 15);        
    return 0;
}
```

📝 Cách trên có thể tính tổng nhiều số nhưng với điều kiện các số đưa vào không được có số 0 chen ở giữa vì khi có số 0 thì việc thực hiện tính toán sẽ dừng lại ngay lập tức.

📝 Có thể khắc phục bằng cách kết hợp cả macro variadic và thư viện STDARG (sẽ nói đến ở bài sau).

<br>

💻 Tạo menu
```cpp
#include <stdio.h>

#define PRINT_MENU_ITEM(number, item) printf("%d. %s\n", number, item)

#define PRINT_MENU(...)                             \
        const char* items[] = {__VA_ARGS__};        \
        int n = sizeof(items) / sizeof(items[0]);   \
        for (int i = 0; i < n; i++) {               \
            PRINT_MENU_ITEM(i + 1, items[i]);       \
        }

#define CASE_OPTION(number, function) case number: function(); break;

#define HANDLE_OPTION(option, ...)              \
    switch (option) {                           \
        __VA_ARGS__                             \
        default: printf("Invalid option!\n");   \
    }

void feature1() { printf("Feature 1 selected\n"); }
void feature2() { printf("Feature 2 selected\n"); }
void feature3() { printf("Feature 3 selected\n"); }
void feature4() { printf("Feature 4 selected\n"); }

int main(int argc, char const *argv[])
{   
    PRINT_MENU("Option 1", "Option 2", "Option 3", "Option 4", "Exit");

    int option;
    scanf("%d", &option);

    HANDLE_OPTION(option, 
        CASE_OPTION(1,feature1)
        CASE_OPTION(2,feature2)
        CASE_OPTION(3,feature3)
        CASE_OPTION(4,feature4)
        )
    return 0;
}
```

<br>

</p>
</details>

</p>
</details>

</p>
</details>

<br>

<details><summary><b>3. Thư viện STDARG</b></summary>
<p>

<details><summary><b>3.1. Giới thiệu thư viện stdarg</b></summary>
<p>

- Tương tự với macro variadic.
- Cung cấp các hàm, macros để làm việc với các hàm có số lượng tham số đầu vào không xác định.
- Các hàm như printf, scanf là ví dụ điển hình.

</p>
</details>

<details><summary><b>3.2. Các Macro trong thư viện stdarg</b></summary>
<p>

<details><summary><b>📚 va_list</b></summary>
<p>

Là một kiểu dữ liệu để đại diện cho danh sách các đối số biến đổi.

``` va_list args ```

📝 Bản chất va_list là một kiểu dữ liệu đã được sử dụng ``` typedef ``` để định nghĩa lại: ``` typedef char* va_list = "int label, ..." ```

📝 Khi thay thế các số trực tiếp vào ``` ... ```, ví dụ ``` 1, 5, 9 ``` thì trở thành ``` typedef char* va_list = "int count, 1, 5, 9" ```

📝 Địa chỉ: 0x01(i) 0x02(n) 0x03(t) 0x04(c) 0x05(o) 0x06(u) 0x07(n) 0x08(t) 0x09(,) 0x0A(1) 0x0B(,) 0x0C(5) 0x0D(,) 0x0E(9)

📝 args: có thể hiểu là một con trỏ được cấp phát động bộ nhớ để truy cập vào từng ký tự trên.

</p>
</details>

<details><summary><b>📚 va_start</b></summary>
<p>

Bắt đầu một danh sách đối số biến đổi. Nó cần được gọi trước khi truy cập các đối số biến đổi đầu tiên.

``` va_start(args, label) ```

📝 label: chính là tên biến mà ta truyền vào, ví dụ ``` int count ``` thì label là ``` count ```, ``` int a ``` thì label là ``` a ```

📝 ``` va_start ``` thực hiện so sánh chuỗi phía trên với label (so sánh từng ký tự) để tìm kiếm đâu là nơi bắt đầu của những số cần thao tác chính. 

📝 Khi con trỏ ``` args ``` trỏ đến địa chỉ 0x09 (,) thì những số phía sau (1,5,9) sẽ được lưu vào mảng khác: arr[] = {'1', '5', '9'}

</p>
</details>

<details><summary><b>📚 va_arg</b></summary>
<p>

Truy cập một đối số trong danh sách. Hàm này nhận một đối số của kiểu được xác định bởi tham số thứ hai.

``` va_arg(args, <data_type>) ```

📝 ``` va_arg ``` sẽ truy cập đến từng phần tử trong mảng và thực hiện ép kiểu về kiểu dữ liệu chúng ta muốn (int, double, char*)

📝 Mỗi lần gọi ``` va_arg(args, <data_type>) ``` thì sẽ thực hiện truy cập và lấy ra 1 phần tử trong mảng.

</p>
</details>

<details><summary><b>📚 va_copy</b></summary>
<p>

Sao chép một đối tượng va_list.  Điều này rất hữu ích khi bạn muốn lưu trữ trạng thái hiện tại của va_list để sử dụng sau này hoặc khi cần đọc lại các đối số biến đổi mà không làm thay đổi va_list gốc.

```cpp
va_list args;
va_list check;
va_copy(check,args);
```

📝 ``` va_copy ``` giúp con trỏ ``` check ``` copy địa chỉ mà con trỏ ``` args ``` đang trỏ đến 

</p>
</details>

<details><summary><b>📚 va_end</b></summary>
<p>

Kết thúc việc sử dụng danh sách đối số biến đổi. Nó cần được gọi trước khi kết thúc hàm.

``` va_end(args) ```

📝 Thu hồi địa chỉ con trỏ ``` args ```

</p>
</details>

</p>
</details>

<details><summary><b>📚 Ví dụ</b></summary>
<p>

💻 **Tổng ``` n ``` số (Cách 1: chỉ sử dụng thư viện STDARG)**
```cpp
#include <stdio.h>
#include <stdarg.h>

int sum(int count, ...){                                
    va_list args; 
    
    va_start(args, count);

    int result = 0;

    for (int i=0; i<count; i++){
        result += va_arg(args, int);
    }

    va_end(args);   

    return result;
}

int main(int argc, char const *argv[])
{
    printf("Tong = %d\n",sum(7, 1, 2, 3, 4, 5, 10, 15));
    return 0;
}
```

<br>

💻 **Tổng ``` n ``` số (Cách 2: thư viện STDARG kết hợp variadic với ``` số 0 ``` ở cuối để nhận biết kết thúc việc tính tổng)**
```cpp
#include <stdio.h>
#include <stdarg.h>

#define tong(...)   sum(__VA_ARGS__, 0)

int sum(int count,...){
    va_list args;

    va_start(args, count);

    int result = count;
    int value;

    while ((value = va_arg(args, int)) != 0)
    {
        result += value;
    }

    va_end(args);

    return result;
}

int main()
{
    printf("Tong: %d\n", tong(3, 0, -1, 2, 33, 4, 5));
    return 0;
}
```

<br>

💻 **Tổng ``` n ``` số (Cách 3: thư viện STDARG kết hợp variadic với ``` ký tự bất kỳ ``` ở cuối để nhận biết kết thúc việc tính tổng)**
```cpp
#include <stdio.h>
#include <stdarg.h>

#define tong(...)   sum(__VA_ARGS__, '\n')

int sum(int count,...) {
    va_list args;
    va_list check;
    
    va_start(args, count);
    va_copy(check, args);

    int result = count;
    
    while (va_arg(check, char*) != (char*)'\n')
    {
        result += va_arg(args,int);
    }

    va_end(args);

    return result;
}

int main(int argc, char const *argv[])
{
    printf("Tong: %d\n", tong(3, 0, -1, 2, 0, 4, 5));
    return 0;
}
```

<br>

💻
```cpp
#include <stdio.h>
#include <stdarg.h>

typedef struct Data{
    int x;
    double y;
} Data;

void display(int count, ...) {
    va_list args;

    va_start(args, count);

    for (int i = 0; i < count; i++)
    {
        Data tmp = va_arg(args,Data);
        printf("Data.x at %d is: %d\n", i,tmp.x);
        printf("Data.y at %d is: %f\n", i,tmp.y);
    }
   
    va_end(args);
}

int main(int argc, char const *argv[])
{
    display(3, (Data){2,5.0} , (Data){10,57.0}, (Data){29,36.0});
    return 0;
}
```

<br>

💻
```cpp
#include <stdio.h>
#include <stdarg.h>

typedef enum {
    TEMPERATURE_SENSOR,
    PRESSURE_SENSOR
} SensorType;

void processSensorData(SensorType type, ...) {
    va_list args;
    va_start(args, type);

    switch (type){
        case TEMPERATURE_SENSOR:{
            int numArgs  = va_arg(args, int);
            int sensorId = va_arg(args, int);
            double temperature = va_arg(args, double); 

            printf("Temperature Sensor ID: %d, Reading: %.2f degrees\n", sensorId, temperature);
            if (numArgs > 2){
                // Xử lý thêm tham số nếu có
                char *additionalInfo = va_arg(args, char*);
                printf("Additional Info: %s\n", additionalInfo);
            }
            break;
        }
        case PRESSURE_SENSOR:{
            int numArgs = va_arg(args, int);
            int sensorId = va_arg(args, int);
            int pressure = va_arg(args, int);

            printf("Pressure Sensor ID: %d, Reading: %d Pa\n", sensorId, pressure);
            if (numArgs > 2) {
                // Xử lý thêm tham số nếu có
                char *unit = va_arg(args, char*);
                printf("Unit: %s\n", unit);
            }
            break;
        }
    }

    va_end(args);
}

int main(int argc, char const *argv[])
{
    processSensorData(TEMPERATURE_SENSOR, 3, 1, 36.5, "Room Temperature");
    processSensorData(PRESSURE_SENSOR, 4, 2, 101325, 123, "aads");
    return 0;
}
```

<br>

💻
```cpp
#include <stdio.h>
#include <stdarg.h>

typedef enum {
    TURN_ON,
    TURN_OFF,
    SET_LEVEL,
    SEND_MESSAGE
} CommandType;

void sendCommand(CommandType command, ...) {
    va_list args;
    va_start(args, command);

    switch (command) {
        case TURN_ON:
        case TURN_OFF: {
            int deviceID = va_arg(args, int);
            printf("Command: %s Device ID: %d\n", command == TURN_ON ? "Turn On" : "Turn Off", deviceID);
            break;
        }
        case SET_LEVEL: {
            int deviceID = va_arg(args, int);
            int level = va_arg(args, int);
            printf("Set Level of Device ID %d to %d\n", deviceID, level);
            break;
        }
        case SEND_MESSAGE: {
            char* message = va_arg(args, char*);
            printf("Send Message: %s\n", message);
            break;
        }
    }

    va_end(args);
}

int main() {
    sendCommand(TURN_ON, 1);
    sendCommand(TURN_OFF, 5);
    sendCommand(SET_LEVEL, 3, 75);
    sendCommand(SEND_MESSAGE, "Hello World");
    return 0;
}
```

<br>

</p>
</details>

</p>
</details>

<br>

<details><summary><b>4. Thư viện assert</b></summary>
<p>

- Cung cấp macro assert dùng để kiểm tra một điều kiện.
- Nếu điều kiện đúng thì chương trình tiếp tục thực thi.
- Nếu điều kiện sai thì chương trình dừng lại ngay lập tức và thông báo một thông điệp lỗi.
- Dùng trong debug, dùng ``` #define NDEBUG ``` để tắt debug

💻
```cpp
#include <stdio.h>
#include <assert.h>

#define LOG(condition, cmd) assert(condition && #cmd)

double divide(int a, int b){
    LOG(b != 0, Mau bang 0);
    return (double)a/b;
}

int main(int argc, char const *argv[])
{
    int x = 6;    
    LOG(x==5, X phai bang 5);
    printf("x = %d\n", x);    
    
    printf("kq: %f\n", divide(6,0));
    return 0;
}
```

📝 Có thể thấy rằng khi sử dụng assert để kiểm tra lỗi, nếu có lỗi thì chương trình dừng ngay lập tức và không thực thi tiếp bất kỳ tác vụ nào.

📝 Để hạn chế vấn đề trên thì nên sử dụng TRY, CATCH, THROW (sẽ nói ở bài **Thư viện setjmp**).

<br>

💻
```cpp
#include <stdio.h>
#include <assert.h>

#define ASSERT_IN_RANGE(val, min, max) assert((val) >= (min) && (val) <= (max))

void setLevel(int level) {
    ASSERT_IN_RANGE(level, 1, 10);
    // Thiết lập cấp độ
}
```

<br>

💻
```cpp
#include <assert.h>
#include <stdint.h>

#define ASSERT_SIZE(type, size) assert(sizeof(type) == (size))

void checkTypeSizes() {
    ASSERT_SIZE(int, 4);
    // Kiểm tra các kích thước kiểu dữ liệu khác
}
```

<br>

</p>
</details>

<br>

<details><summary><b>5. Pointer</b></summary>
<p>

<details><summary><b>5.1. Khái niệm về con trỏ</b></summary>
<p>

- Con trỏ (pointer) là một biến chứa địa chỉ của một đối tượng (biến, mảng hoặc hàm) khác.
- Việc sử dụng con trỏ giúp thực hiện các thao tác trên bộ nhớ một cách linh hoạt hơn.

</p>
</details>

<details><summary><b>5.2. Kích thước con trỏ</b></summary>
<p>

- Kích thước của con trỏ phụ thuộc vào **kiến trúc vi xử lý** hoặc **kiến trúc máy tính và trình biên dịch**.
- Hệ thống 32-bit, kích thước của con trỏ là 4 byte.
- Hệ thống 64-bit, kích thước của con trỏ là 8 byte.
- SMT32: kiến trúc 32-bit (ARM Cortex-M) nên kích thước con trỏ là 4 byte.
- STM8: kiến trúc 8-bit nên kích thước con trỏ là 1 byte.

🖥️
```cpp
#include <stdio.h>
#include <stdbool.h>
#include <stdint.h>

int main(int argc, char const *argv[]){
    printf("%d bytes\n", sizeof(int *));
    printf("%d bytes\n", sizeof(uint8_t *));
    printf("%d bytes\n", sizeof(int16_t *));
    printf("%d bytes\n", sizeof(char *));
    printf("%d bytes\n", sizeof(float *));
    printf("%d bytes\n", sizeof(double *));
    printf("%d bytes\n", sizeof(long *));
    printf("%d bytes\n", sizeof(short *));
    printf("%d bytes\n", sizeof(long long *));
    printf("%d bytes\n", sizeof(bool *));
    return 0;
}
```
<br>

🤔 Nếu kích thước con trỏ không thay đổi thì kiểu dữ liệu của nó sẽ ảnh hưởng như thế nào đến việc sử dụng nó❓ 

➡️ **Kích thước bước nhảy khi tăng/giảm giá trị của con trỏ**: giá trị con trỏ sẽ thay đổi theo kích thước của kiểu dữ liệu mà nó trỏ tới.

Ví dụ: 
```cpp
char:   0x00 -> 0x01 -> 0x02 -> ...
int:    0x00 -> 0x04 -> 0x08 -> ...
double: 0x00 -> 0x08 -> 0x10 -> ...
```

➡️ **Truy cập giá trị**: Khi truy cập giá trị thông qua con trỏ (bằng toán tử dereference ``` * ```), kiểu dữ liệu của con trỏ quyết định kích thước và cách thức đọc dữ liệu từ bộ nhớ.

Ví dụ: 
```cpp
char:   truy cập 1 byte tại địa chỉ pointer trỏ đến.
int:    truy cập 4 byte tại địa chỉ pointer trỏ đến.
double: truy cập 8 byte tại địa chỉ pointer trỏ đến.
```

<br>

**Ứng dụng con trỏ**:

🖥️ Ví dụ: Nhập số từ bàn phím
```cpp
#include <stdio.h>

void input(int *a, int *b){
    printf("Nhap so 1: "); scanf("%d", a);
    printf("Nhap so 2: "); scanf("%d", b);
}

int main(int argc, char const *argv[])
{
    int a,b;
    input(&a,&b);
    return 0;
}
```

<br>

🖥️ Ví dụ: Hoán đổi 2 số
```cpp
#include <stdio.h>

void swap1(int a, int b){
    int temp = a;
    a = b;
    b = temp;
}
// Khi gọi hàm sẽ khởi tạo 2 địa chỉ, gs là 0xc1 và 0xf2 để lưu giá trị a,b
// Thực hiện copy giá trị của biến a,b trong hàm main và đưa vào 2 địa chỉ trên
// a,b ở đây là 2 tham số truyền vào của hàm nên sẽ lưu ở Stack và bị thu hồi vùng nhớ khi kết thúc việc gọi hàm
// Vì vậy, gọi hàm này sẽ không hoán đổi 2 số

void swap2(int *a, int *b){
    int temp = *a;
    *a = *b;
    *b = temp;
}
// Khi gọi hàm sẽ truy cập đến 2 địa chỉ 0x01 và 0xb4 để lấy giá trị và thực hiện hoán đổi

int main(int argc, char const *argv[])
{
    int a = 10;	// địa chỉ 0x01
    int b = 20; // địa chỉ 0xb4

    // swap1(a,b);
    swap2(&a,&b);
    printf("value a is: %d\n", a);
    printf("value b is: %d\n", b);
    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>5.3. Các kiểu con trỏ</b></summary>
<p>

<details><summary><b>📚 Con trỏ Void (Void Pointer)</b></summary>
<p>
	
- Thường dùng để **trỏ tới bất kỳ địa chỉ** với bất kỳ kiểu dữ liệu của giá trị tại địa chỉ đó.
- Muốn in ra giá trị thì phải sử dụng ép kiểu để đưa con trỏ void về đến kiểu dữ liệu của giá trị đó.
- Cú pháp: ``` void *ptr_void; ```

🖥️
```cpp 
#include <stdio.h>

void sum(int a, int b){
    printf("%d + %d = %d\n", a, b, a+b);
}

int main(int argc, char const *argv[])
{
    void *ptr_void;
    ptr_void = (void*)sum;
    ((void (*)(int,int))ptr_void)(9,3);

    int var_int = 10;
    ptr_void = &var_int;
    printf("Dia chi: %p, int: %d\n", ptr_void, *(int*)ptr_void);

    double var_double = 3.14;
    ptr_void = &var_double;
    printf("Dia chi: %p, double: %.3f\n", ptr_void, *(double*)ptr_void);

    char var_char = 'A';
    ptr_void = &var_char;
    printf("Dia chi: %p, char: %c\n", ptr_void, *(char*)ptr_void);

    char arr[] = "hello";
    ptr_void = arr;

    // printf("chuoi: %c\n", *(char*)(ptr_void+1));
    
    printf("Chuoi: ");
    for (int i=0; i<(sizeof(arr)/sizeof(arr[0])); i++){
        printf("%c\n", *(char*)(ptr_void+i));
    }
    printf("\n");

    void *ptr[] = {&var_int, &var_double, &var_char, sum, arr};
    printf("ptr[0] = %d\n", *(int*)ptr[0]);
    printf("ptr[1] = %f\n", *(double*)ptr[1]);
    printf("ptr[2] = %c\n", *(char*)ptr[2]);

    ((void (*)(int,int))ptr[3])(9,3);

    for (int i=0; i<(sizeof(arr)/sizeof(arr[0])); i++){
        printf("%c", *(char*)(ptr[4]+i));
    }
    return 0;
}

```
```cpp
9 + 3 = 12
Dia chi: 00000075E7BFF70C, int: 10
Dia chi: 00000075E7BFF700, double: 3.140   
Dia chi: 00000075E7BFF6FF, char: A
Chuoi:
h
e
l
l
o

ptr[0] = 10
ptr[1] = 3.140000
ptr[2] = A
9 + 3 = 12
hello
```
</p>
</details>

<details><summary><b>📚 Con trỏ hàm (Function Pointer)</b></summary>
<p>
	
- Con trỏ hàm là một biến mà **giữ địa chỉ của hàm**.
- Cần chỉ định kiểu dữ liệu của hàm mà con trỏ đó sẽ tham chiếu đến khi khai báo, bao gồm kiểu trả về và các tham số của hàm. Sau đó, ta có thể gán con trỏ hàm này cho một hàm cụ thể.
- Khi gọi con trỏ hàm, chương trình sẽ thực thi hàm mà con trỏ đang tham chiếu đến.
- Cú pháp: ``` <return_type> (* func_pointer)(input_1_data type, input_2_data type,....); ```

🖥️
```cpp
#include <stdio.h>
// Hàm mẫu 1
void greetEnglish(){
    printf("Hello!\n");
}

// Hàm mẫu 2
void greetFrench(){
    printf("Bonjour!\n");
}

int main(){
    // Khai báo con trỏ hàm
    void (*ptrToGreet)();
    
    // Gán địa chỉ của hàm greetEnglish cho con trỏ hàm
    ptrToGreet = greetEnglish;
    
    // Gọi hàm thông qua con trỏ hàm
    ptrToGreet();  // In ra: Hello!

    // Gán địa chỉ của hàm greetFrench cho con trỏ hàm
    ptrToGreet = greetFrench;
    
    // Gọi hàm thông qua con trỏ hàm
    (*ptrToGreet)();  // In ra: Bonjour!    
    
    return 0;
}
```
```cpp
Hello!
Bonjour!
```

<br>

🖥️
```cpp
#include <stdio.h>

void tong(int a, int b){
    printf("%d + %d = %d\n", a, b, a+b);
}

void hieu(int a, int b){
    printf("%d - %d = %d\n", a, b, a-b);
}

void tich(int a, int b){
    printf("%d x %d = %d\n", a, b, a*b);
}

void thuong(int a, int b){
    printf("%d / %d = %0.3f\n", a, b, a/(double)b);
}

int main(int argc, char const *argv[])
{
    int a = 10, b = 20;

    // cách 1
    void (*ptr)(int,int);
    ptr = tong;
    ptr(a,b);

    ptr = hieu;
    ptr(a,b);

    ptr = tich;
    ptr(a,b);

    ptr = thuong;
    ptr(a,b);
    printf("\n");


    // cách 2
    tinhtoan(tong,a,b);
    tinhtoan(hieu,a,b);
    tinhtoan(tich,a,b);
    tinhtoan(thuong,a,b);
    printf("\n");


    // cách 3
    void (*calculate[])(int,int) = {tong, hieu, tich, thuong};
    calculate[0](a,b);
    calculate[1](a,b);
    calculate[2](a,b);
    calculate[3](a,b);
    return 0;
}

void tinhtoan(void (*pheptoan)(int,int), int a, int b){
    pheptoan(a,b);
}
```
```cpp
10 + 20 = 30
10 - 20 = -10
10 x 20 = 200
10 / 20 = 0.500

10 + 20 = 30
10 - 20 = -10
10 x 20 = 200
10 / 20 = 0.500

10 + 20 = 30
10 - 20 = -10
10 x 20 = 200
10 / 20 = 0.500
```
</p>
</details>

<details><summary><b>📚 Con trỏ hằng (Pointer to constant)</b></summary>
<p

- Con trỏ hằng là một cách định nghĩa một con trỏ **chỉ có thể đọc giá trị tại địa chỉ mà nó trỏ đến (Read Only)** nhưng không thể thay đổi được giá trị đó.
- Đối với biến là hằng số thì phải luôn dùng con trỏ hằng khi trỏ đến.
- Cú pháp: 
```cpp
<data_type> const *ptr_const;
const <data_type> *ptr_const;
```

🖥️
```cpp
#include <stdio.h>

int value1 = 10;
int value2 = 3;
const int *ptr_const = &value1;

int main(int argc, char const *argv[])
{
    printf("%p\n", ptr_const);
    printf("%d\n", *ptr_const);

    value1 = 5;
    printf("%p\n", ptr_const);
    printf("%d\n", *ptr_const);

    //*ptr_const = 5;    // wrong
    ptr_const = &value2; // right
    printf("%p\n", ptr_const);
    printf("%d\n", *ptr_const);
    return 0;
}
```
📝 Kết quả sau khi chạy sẽ gặp lỗi: ```assignment of read-only location '*ptr_const'```

<br>

🖥️
```cpp
#include <stdio.h>

void test(const char *str){
	str[0] = 'A';
}

int main(int argc, char const *argv[])
{
    char arr[] = "Hello World";
    test(arr);
    printf("%s\n", arr);
    return 0;
}
```

📝 Trong hàm test, nếu khai báo ``` char* ``` thì chuỗi truyền vào hoàn toàn có thể bị thay đổi. Để ngăn việc thay đổi xảy ra, nghĩa là nhu cầu chỉ đọc chuỗi truyền vào thì phải khai báo ``` const char* ```.

</p>
</details>

<details><summary><b>📚 Hằng con trỏ (Constant to Pointer)</b></summary>
<p
    
- Hằng con trỏ là một con trỏ mà **trỏ đến 1 địa chỉ cố định**, nghĩa là khi con trỏ này được khởi tạo thì nó sẽ không thể trỏ tới địa chỉ khác.
- Cú pháp: ``` int *const const_ptr = &value; ```

💻
```cpp
#include <stdio.h>

int value1 = 10;
int value2 = 20;
int *const const_ptr = &value1;

int main(int argc, char const *argv[]){
    printf("%p\n", const_ptr);
    printf("%d\n", *const_ptr);

    *const_ptr = 5;
    printf("%p\n", const_ptr);
    printf("%d\n", *const_ptr);
    
    // const_ptr = &value2; // wrong
    // printf("%p\n", const_ptr);
    return 0;
}

```
📝 Kết quả sau khi chạy sẽ gặp lỗi: ```assignment of read-only variable 'const_ptr'```

<br>

**Ứng dụng**: thiết kế thư viện, ví dụ một GPIO sẽ có nhiều thanh ghi bên trong như GPIO_CRL, GPIO_CRH, GPIO_ODR, v.v. Mỗi thanh ghi sẽ được cấp cho một địa chỉ cố định.

</p>
</details>

<details><summary><b>📚 Con trỏ NULL (Null Pointer)</b></summary>
<p
    
- Khi khai báo con trỏ mà chưa sử dụng ngay hoặc sử dụng xong thì phải gán NULL.

💻
```cpp
int *ptr_null = NULL;
//  ptr_null = 0x00: địa chỉ khởi tạo
// *ptr_null = 0   : giá trị tại địa chỉ khởi tạo
```
</p>
</details>

<details><summary><b>📚 Pointer to pointer</b></summary>
<p
    
- Là một kiểu dữ liệu trong ngôn ngữ lập trình cho phép bạn lưu trữ địa chỉ của một con trỏ.
- Con trỏ đến con trỏ cung cấp một cấp bậc trỏ mới, cho phép bạn thay đổi giá trị của con trỏ gốc.
- Cấp bậc này có thể hữu ích trong nhiều tình huống, đặc biệt là khi bạn làm việc với các hàm cần thay đổi giá trị của con trỏ.

💻
```cpp
int a = 10;		// a là 1 biến, giả sử có địa chỉ 0x01
int *ptr1  = &a;	// ptr1 là con trỏ cấp 1, trỏ đến địa chỉ biến a (0x01), dịa chỉ ptr1 là 0xf4
int **ptr2 = &ptr1;	// ptr2 là con trỏ cấp 2, trỏ đến địa chỉ ptr1 (0xf4)
```

```cpp
ptr1 = &a = 0x01;
*ptr1 = a = 10;

ptr2 = &ptr1 = 0xf4;
*ptr2 = *(&ptr1) = *(0xf4) = 0x01;
**ptr2 = *(0x01) = 10;
```

</p>
</details>

</p>
</details>

</p>
</details>

<br>

<details><summary><b>6. Các biến đặc biệt</b></summary>
<p>

<details><summary><b>📚 Extern</b></summary>
<p>

- **extern** có thể sử dụng cho một đối tượng (biến hoặc hàm), nếu là **biến** thì phải được **khai báo toàn cục** với mục đích là thông báo rằng biến hoặc hàm này đã được định nghĩa ở một nơi khác trong chương trình hoặc trong 1 file nguồn khác.
- **extern** cho phép các file nguồn khác nhau trong cùng một chương trình chia sẽ và sử dụng các biến và hàm mà không cần định nghĩa lại.
- **extern** chỉ cho phép khai báo chứ không định nghĩa.
- Cú pháp: ``` extern <data_type> <name_variable>; ```

💻 

File **main.c**
```cpp
#include <stdio.h>

extern int a;
extern int b;

extern void display1();
extern void display2();

int main(int argc, char const *argv[])
{
    a = 20;
    printf("a = %d\n",a);

    b = 50;
    printf("b = %d\n",b);

    display1();
    display2();
    return 0;
}
```

**Ứng dụng**:

- Thiết kế thư viện.
- Chia sẻ biến và hàm giữa các file nguồn hoặc giữa các module và thư viện.
- Sử dụng một hàm trước khi nó được định nghĩa trong mã nguồn.
- Chia sẻ hằng số giữa các file nguồn.

💻

File **File1.h**
```cpp
#ifndef _FILE1_H
#define _FILE1_H

extern int a;

void display1();

#endif
```

File **File1.c**
```cpp
#include <stdio.h>
#include "File1.h"

int a = 10;

void display1(){
    printf("This is file1.c\n");
}
```

File **File2.h**
```cpp
#ifndef _FILE2_H
#define _FILE2_H

extern int b;

void display2();

#endif
File **File2.c**
#include <stdio.h>
#include "File2.h"

int b = 10;

void display2(){
    printf("This is file2.c\n");
}
```

File **main.c**
```cpp
#include <stdio.h>
#include "File1.h"
#include "File2.h"

extern void display1();
extern void display2();

int main(int argc, char const *argv[])
{
    a = 20;
    printf("a = %d\n",a);

    b = 50;
    printf("b = %d\n",b);

    display1();
    display2();
    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>📚 Static</b></summary>
<p>

**Cú pháp**:

```cpp
static <data_type> <name_variable>;
static <data_type> <name_function>;
```

<details><summary><b>📚📚 Static local</b></summary>
<p>
	
Khi 1 biến cục bộ được khai báo với từ khóa static:

- Biến chỉ được khởi tạo một lần, nghĩa là địa chỉ của nó sẽ tồn tại xuyên suốt chương trình.
- Giữ phạm vi của biến chỉ trong hàm đó.
- Giữ giá trị của biến qua các lần gọi hàm.

Biến cục bộ static chỉ có thể được gọi trong nội bộ hàm khởi tạo ra nó. Mỗi lần hàm được gọi, giá trị của biến chính bằng giá trị tại lần gần nhất hàm được gọi.

💻
```cpp
#include <stdio.h>

void count(){
    static int a = 5;
    a++;
    printf("a = %d\n",a);
}

int main(int argc, char const *argv[])
{
    count();
    count();
    count();
    return 0;
}
```

Kết quả in ra:
```cpp
a = 6
a = 7
a = 8
```

📝 Nếu không có từ khóa static, kết quả 3 lần gọi hàm đều giống nhau vì biến ``` a ``` biến cục bộ, sẽ được lưu trong Stack và địa chỉ sẽ bị thu hồi sau khi hàm thực thi xong. 

📝 Khi thêm ``` static ```, ở lần gọi hàm đầu tiên, biến ``` a ``` sẽ được cấp phát địa chỉ, giả sử 0x01 và địa chỉ tồn tại cho đến hết chương trình.

📝 Khi gọi hàm từ lần 2 trở đi, nó sẽ không thực thi câu lệnh ``` static int a = 5 ``` vì địa chỉ biến a đã được cấp phát trước đó mà sẽ bắt đầu thực thi từ câu lệnh tiếp theo (``` a++ ```).

**Có thể thay đổi giá trị của biến cục bộ từ bên ngoài thông qua một con trỏ.** 

💻
```cpp
#include <stdio.h>

int *ptr = NULL;

void count(){
    static int a = 5;
    ptr = &a;
    a++;
    printf("a = %d\n",a);
}

int main(int argc, char const *argv[]){
    count();     // in ra "a = 6"
    count();     // in ra "a = 7"
    count();     // in ra "a = 8"

    *ptr = 99;  // truy cập địa chỉ 0x01 và thay đổi giá trị biến a thành 99
    count();     // in ra "a = 100"
    ptr = NULL;
    return 0;
}
```

</p>
</details>

<details><summary><b>📚📚 Static global</b></summary>
<p>

Khi **'static'** được sử dụng với các biến toàn cục, nó sẽ hạn chế phạm vi của biến chỉ có thể gọi trong file nguồn hiện tại.

💻

File **file1.h**
```cpp
#ifndef _FILE1_H
#define _FILE1_H

static int a;

static void display();

void test();

#endif
```

File **file1.c**
```cpp
#include <stdio.h>
#include "file1.h"

static int a = 10;

static void display(){
    printf("This is file1.c\n");
}

void test(){
    printf("Hello world\n");
}
```

File **main.c**
```cpp
#include <stdio.h>

extern int a;
extern void display();
extern void test();

int main(int argc, char const *argv[])
{
    printf("a = %d\n",a);
    display();
    test();
    return 0;
}

```

📝 Kết quả sau khi chạy: 
```cpp
undefined reference to `display'
undefined reference to `a'
```

📝 Dễ thấy file main.c khi chạy sẽ gặp lỗi do cố gắng sử dụng extern để gọi 1 biến toàn cục hoặc hàm đã được khai báo với static trong 1 file nguồn khác.

<br>

</p>
</details>

<details><summary><b>📚📚 Ứng dụng</b></summary>
<p>

- Thiết kế thư viện.
- Quản lý tài nguyên bộ nhớ tốt hơn và tránh xung đột tên biến giữa các module khác nhau.
- Khi khai báo biến toàn cục với static trong một file C, biến chỉ có thể truy cập trong file đó, ngăn ngừa các vấn đề chia sẻ biến không mong muốn giữa các file.
- Dùng cho biến cục bộ trong một hàm để giữ lại giá trị của biến giữa các lần gọi hàm (persistence).

<br>

</p>
</details>

</p>
</details>

<details><summary><b>📚 Volatile</b></summary>
<p>

- **volatile** báo cho compiler biết rằng biến này **có thể sẽ được thay đổi ở bởi yếu tố bên ngoài chương trình** như hardward (ngắt, nhấn button,…) hoặc một luồng khác.
- **volatile** ngăn chặn compiler tối ưu hóa hoặc xóa bỏ các thao tác trên biến đó, giữ cho các thao tác trên biến được thực hiện như đã được định nghĩa.
- Cú pháp: ``` volatile <data_type> <name_variable>; ```
- Biến Volatile rất cần thiết trong lập trình nhúng, vì khi đó có các tác vụ như ngắt ảnh hưởng tới giá trị của biến. Trong lập trình C cơ bản thì rất ít gặp.

💻
```cpp
#include "stm32f4xx.h"                  // Device header

uint8_t *addr = (uint8_t*)0x20000000;
volatile uint8_t var = 0;

int main(){
	while (1){
		var = *addr;
		if (var != 0){
			break;
		}
	}
}
```
📝 Khi khai báo biến ``` var ``` mà không có từ khóa ``` volatile ```, nếu giá trị của biến không thay đổi hoặc thay đổi ngay lần đầu chạy debug (thông qua thay đổi giá trị tại địa chỉ 0x20000000) thì compiler sẽ tối ưu hóa biến này khi nhận thấy biến này không có sự thay đổi giá trị ở những lần chạy kế tiếp.

📝 Khi khai báo biến ``` var ``` có từ khóa ``` volatile ```, trong quá trình chạy, nếu giá trị biến thay đổi đột ngột thì chương trình vẫn cập nhật vì compiler chưa tối ưu hóa biến này.

<br>

</p>
</details>

<details><summary><b>📚 Register</b></summary>
<p>

📝 Trong kiến trúc của vi xử lý thì ALU (Arithmetic Logic Unit) đóng vai trò xử lý các tính toán số học và nó chỉ làm việc với các dự liệu được lưu trữ trong thanh ghi (Register).

📝 Khi khai báo các biến trong chương trình thì những biến này được lưu ở RAM. Nếu có thêm phép tính (``` ++ ``` hoặc ``` -- ```) thì nó chỉ lưu thông tin của phép tính này chứ chưa thực hiện.

![image](https://github.com/user-attachments/assets/a57d1e72-86f1-405d-853c-660e459a37f0)

📝 Giai đoạn 1: Nạp giá trị từ RAM vào Register

📝 Giai đoạn 2: Đưa dữ liệu từ Register sang ALU để bắt đầu xử lý.

📝 Giai đoạn 3: Khi ALU xử lý xong thì trả ngược dữ liệu về Register.

📝 Giai đoạn 4: Trả giá trị vừa xử lý từ Register về lại vùng nhớ RAM.

- **register** được sử dụng để định nghĩa các biến cục bộ mà nên được lưu giữ trong một thanh ghi thay vì RAM.
- **register** làm tăng hiệu năng (performance) của chương trình.
- Cú pháp: ``` register <data_type> <name_variable>; ```

💻
```cpp
#include <stdio.h>
#include <time.h>

int main() {
    // Lưu thời điểm bắt đầu
    clock_t start_time = clock();
    int i;
    //register int i;

    // Đoạn mã của chương trình
    for (i = 0; i < 2000000; ++i) {
        // Thực hiện một số công việc bất kỳ
    }

    // Lưu thời điểm kết thúc
    clock_t end_time = clock();

    // Tính thời gian chạy bằng miligiây
    double time_taken = ((double)(end_time - start_time)) / CLOCKS_PER_SEC;

    printf("Thoi gian chay cua chuong trinh: %f giay\n", time_taken);
    return 0;
}
```

📝 Khi chưa register ```Thoi gian chay cua chuong trinh: 0.005 giay```

📝 Khi có register ```Thoi gian chay cua chuong trinh: 0.001 giay```

</p>
</details>

</p>
</details>

<br>

<details><summary><b>7. goto - thư viện setjmp</b></summary>
<p>

<details><summary><b>7.1. goto trong C</b></summary>
<p>

<details><summary><b>📚 Khái niệm</b></summary>
<p>

- Từ khóa **"goto"** cho phép chương trình nhảy đến một label đã được đặt trước đó **cùng một hàm**.
- "goto" cung cấp khả năng kiểm soát luồng hoạt động của mã nguồn, nhưng việc sử dụng goto thường được xem là không tốt vì nó có thể làm cho mã nguồn trở nên khó đọc và khó bảo trì.

💻
```cpp
int main(){
    int i=0;
    
    // đặt label start
    start:
        if (i >= 5){
            goto end;       // chuyển control đến lable "end"
        }
        printf("%d\n",i);
        i++;
        goto start;

    // đặt label end
    end:
        printf("The end\n");// chuyển control đến label "start"
    return 0;
}
```
📝 Trong ví dụ này, goto được sử dụng để tạo một vòng lặp đơn giản. Khi i đạt đến giá trị 5, control sẽ chuyển đến nhãn "end" và kết thúc chương trình.

</p>
</details>

<details><summary><b>📚 Ứng dụng</b></summary>
<p>

**Thoát khỏi vòng lặp nhiều cấp độ**

Trong một số trường hợp, việc thoát khỏi nhiều cấp độ vòng lặp có thể trở nên phức tạp nếu sử dụng cấu trúc kiểm soát vòng lặp thông thường. Trong tình huống như vậy, goto có thể được sử dụng để dễ dàng thoát khỏi nhiều cấp độ vòng lặp.

💻
```cpp
int main(int argc, char const *argv[]){
    int count=0;

    for (int i=0; i<10; i++){
        for (int j=0; j<10; j++){
            if (i==5 && j==5) goto exit_loops;
            else{
                printf("i=%d  j=%d\n", i, j);
            }
        }
    }
    
    exit_loops:
    return 0;
}
```

**Xử lý lỗi và giải phóng bộ nhớ**

Trong trường hợp xử lý lỗi, có thể sử dụng goto để dễ dàng giải phóng bộ nhớ đã được cấp phát trước khi thoát khỏi hàm.

💻
```cpp
void process_data() {
    int *data = malloc(sizeof(int) * 100);
    if (data == NULL) {
        goto cleanup;
    }

    // Xử lý dữ liệu ở đây

    cleanup:
    free(data);
}
```

**Thực hiện Finite State Machine**
Trong một số trường hợp, đặc biệt là khi triển khai Finite State Machines, goto có thể được sử dụng để chuyển đến các trạng thái khác nhau một cách dễ dàng.

💻
```cpp
switch (current_state) {
    case STATE_A:
        // Xử lý State A
        if (condition) {
            goto STATE_B;
        }
        break;

    case STATE_B:
        // Xử lý State B
        break;
}
```

<br>

</p>
</details>

</p>
</details>

<details><summary><b>7.2. Thư viện setjmp</b></summary>
<p>

<details><summary><b>📚 Hàm setjmp</b></summary>
<p>

- setjmp lưu trạng thái hiện tại của môi trường thực thi vào một biến kiểu jmp_buf.
- setjmp thường được sử dụng để thiết lập một điểm quay lại (checkpoint) trong chương trình.
- setjmp trả về giá trị 0 khi được gọi lần đầu tiên và giá trị khác 0 khi quay lại từ longjmp.

```setjmp(jmp_buf buf);```

</p>
</details>

<details><summary><b>📚 Hàm longjmp</b></summary>
<p>

- longjmp là hàm dùng để nhảy trở lại vị trí đã lưu bởi setjmp và tiếp tục thực thi chương trình từ đó.

```cpp
void longjmp(jmp_buf buf, int value);
// buf  : biến 'jmp_buf' đã được lưu bởi 'setjmp'
// value: giá trị trả về từ 'setjmp'. Nếu value=0 thì 'setjmp' trả về 1
```

💻
```cpp
#include <stdio.h>
#include <setjmp.h>

jmp_buf buf;
int exception_code;

double thuong(int a, int b){
	if (!b){
		longjmp(buf,1);
	}
	return a/(double)b;
}

int checkArray(int *arr, int size){
    	if (size <= 0){
        	longjmp(buf,2);
	}
    	return 1;
}

int main(int argc, char const *argv[]){
	// khi bắt đầu thì setjmp(buf) luôn bằng 0

	if ((exception_code = setjmp(buf)) == 0){
		int array[0];
		double ketqua = thuong(8,0);
		printf("Ket qua: %0.3f\n", ketqua);
		checkArray(array,0);
	}
	else if (exception_code == 1){
		printf("ERROR! Mau bang 0\n");
	}
	else if (exception_code == 2){
		printf("ERROR! Array bang 0\n");
	}
	return 0;
}
```

</p>
</details>

<details><summary><b>📚 Xử lý ngoại lệ</b></summary>
<p>

Cả hai hàm setjmp và longjmp thường được sử dụng để thực hiện xử lý ngoại lệ trong C thông qua 3 keywords chính là: **try, catch, throw**.

```cpp
#include <stdio.h>
#include <setjmp.h>

jmp_buf buf;
int exception_code;

#define TRY if ((exception_code = setjmp(buf)) == 0)
#define CATCH(x) else if (exception_code == x)
#define THROW(x) longjmp(buf,x)
```

💻**Ví dụ**
```cpp
#include <stdio.h>
#include <setjmp.h>

jmp_buf buf;
int exception_code;

#define TRY if ((exception_code = setjmp(buf)) == 0)
#define CATCH(x) else if (exception_code == x)
#define THROW(x) longjmp(buf,x)

double thuong(int a, int b){
    if (b == 0){
        THROW(1);
    }
    return a/(double)b;
}

int checkArray(int *arr, int size){
    if (size <= 0){
        THROW(2);
    }
    return 1;
}

int main(int argc, char const *argv[])
{
    
    TRY{
        int array[0];
        double ketqua = thuong(8,1);
        printf("Ket qua = %0.3f\n",ketqua);
        checkArray(array,0);
    }
    CATCH(1){
        printf("Error\n");
    }
    CATCH(2){
        printf("Error! Array = 0\n");
    }
    return 0;
}
```

<br>

</p>
</details>

</p>
</details>

</p>
</details>

<br>

<details><summary><b>8. Bitmask</b></summary>
<p>

<details><summary><b>8.1. Khái niệm</b></summary>
<p>

- Bitmask, hay còn gọi là mặt nạ bit, là một kỹ thuật phổ biến trong lập trình được sử dụng để thao tác trên các bit riêng lẻ trong một giá trị nhị phân.
- Bitmask cho phép bạn kiểm tra, đặt, hoặc xóa các bit cụ thể trong một số mà không ảnh hưởng đến các bit khác. Bitmask được dùng nhiều trong các ứng dụng yêu cầu xử lý dữ liệu cấp thấp hoặc tối ưu hóa bộ nhớ..
- Bitmask thường được sử dụng để tối ưu hóa bộ nhớ, thực hiện các phép toán logic trên một cụm bit, và quản lý các trạng thái, quyền truy cập, hoặc các thuộc tính khác của một đối tượng.

<br>

</p>
</details>

<details><summary><b>8.2. Các toán tử bitwise</b></summary>
<p>

Bitmask là một số nhị phân được sử dụng kết hợp với các phép toán bit như **AND (&), OR (|), XOR (^), NOT (~), dịch phải (>>) và dịch trái (<<)** để thực hiện các thao tác trên bit.

![image](https://github.com/user-attachments/assets/e9b5fc2e-0d94-4708-bbe1-740f7a21d417)

![image](https://github.com/user-attachments/assets/c8549e26-9a0b-4835-a886-d11a6bed9fb1)

<br>

</p>
</details>

<details><summary><b>8.3. Các phép toán phổ biến với bitwise</b></summary>
<p>

<details><summary><b>📚 Kiểm tra bit (bit checking)</b></summary>
<p>

- Sử dụng phép AND để kiểm tra xem một bit cụ thể có đang được bật hay không.

💻 Ví dụ 1:
```cpp
int num = 0b1010; // Số nhị phân: 1010
int mask = 0b0010; // Mặt nạ để kiểm tra bit thứ 2

if (num & mask) {
    printf("Bit thứ 2 đang bật\n");
} else {
    printf("Bit thứ 2 đang tắt\n");
}
```

💻 Ví dụ 2:
```cpp
uint8_t num = 0b11011010;
uint8_t mask = 0b00001100; // Mặt nạ để kiểm tra bit thứ 2 và 3

if (num & mask) {
    printf("Ít nhất một trong hai bit thứ 2 và 3 được bật\n");
} else {
    printf("Cả hai bit thứ 2 và 3 đều tắt\n");
}
```

<br>

</p>
</details>

<details><summary><b>📚 Bật một bit (bit setting)</b></summary>
<p>

- Sử dụng phép OR để bật một bit cụ thể.

💻 Ví dụ:
```cpp
int num = 0b1001; // Số nhị phân: 1001
int mask = 0b0100; // Mặt nạ để bật bit thứ 3

num = num | mask; // num bây giờ là 1101
```

<br>

</p>
</details>

<details><summary><b>📚 Tắt một bit (bit clearing)</b></summary>
<p>

- Sử dụng phép AND với phép NOT để tắt một bit cụ thể.

💻 Ví dụ:
```cpp
int num = 0b1011; // Số nhị phân: 1011
int mask = 0b1101; // Mặt nạ để tắt bit thứ 2 (NOT(0010) = 1101)

num = num & mask; // num bây giờ là 1001
```

<br>

</p>
</details>

<details><summary><b>📚 Đảo một bit (bit toggling)</b></summary>
<p>

- Sử dụng phép XOR để đảo một bit cụ thể.

💻 Ví dụ:
```cpp
int num = 0b1010; // Số nhị phân: 1010
int mask = 0b0010; // Mặt nạ để đảo bit thứ 2

num = num ^ mask; // num bây giờ là 1000
```

<br>

💻 Ví dụ 1:
```cpp
#include <stdio.h>
#include <stdint.h>
                                          //    0    1
#define GENDER    1 << 0  // bit 0: giới tính  nữ   nam     0b00000001
#define SHIRT     1 << 1  // bit 1: áo thun   không  có     0b00000010
#define HAT       1 << 2  // bit 2: nón       không  có     0b00000100
#define SHOES     1 << 3  // bit 3: giày      không  có     0b00001000
#define FEATURE1  1 << 4  // bit 4: tính năng 1             0b00010000
#define FEATURE2  1 << 5  // bit 5: tính năng 2             0b00100000
#define FEATURE3  1 << 6  // bit 6: tính năng 3             0b01000000
#define FEATURE4  1 << 7  // bit 7: tính năng 4             0b10000000


// enable bit
void enableFeature(uint8_t *options, uint8_t feature){
    *options = *options | feature;
}

// disable bit
void disableFeature(uint8_t *options, uint8_t feature){
    *options = *options & ~feature;
}

// check bit
int8_t isFeatureEnabled(uint8_t options, uint8_t feature){
    return ((options & feature) != 0);
}

// liệt kê các tính năng đang bật
void listSelectedFeatures(uint8_t options){
    printf("Selected Features: \n");

    const char* featureName[] = {
        "Gender",
        "Shirt",
        "Hat",
        "Shoes",
        "Feature 1",
        "Feature 2",
        "Feature 3",
        "Feature 4"
    };

    for (int i=0; i<8; i++){
        if ((options >> i) & 1){
            printf("%s\n", featureName[i]);
        }
    }
}

int main(int argc, char const *argv[])
{
    uint8_t options = 0; // 0xb6: 0b00000000

    // Thêm tính năng
    enableFeature(&options, GENDER | HAT | SHOES);

    // Loại bỏ tính năng
    disableFeature(&options, HAT);

    // Liệt kệ các tính năng đã chọn
    listSelectedFeatures(options);
    
    return 0;
}
```

<br>

💻 Ví dụ 2:
```cpp
#include <stdio.h>
#include <stdint.h>

#define COLOR_RED     0 // 0b0000 0000
#define COLOR_BLUE    1 // 0b0000 0001
#define COLOR_BLACK   2 // 0b0000 0010
#define COLOR_WHITE   3 // 0b0000 0011

#define POWER_100HP   0
#define POWER_150HP   1
#define POWER_50HP    2

#define ENGINE_1_5L   0
#define ENGINE_2_0L   1

typedef uint8_t CarColor;
typedef uint8_t CarPower;
typedef uint8_t CarEngine;

#define SUNROOF_MASK         1 << 0
#define PREMIUM_AUDIO_MASK   1 << 1
#define SPORTS_PACKAGE_MASK  1 << 2
// Thêm các bit masks khác tùy thuộc vào tùy chọn

typedef struct{
    uint8_t additionalOptions : 3; // 3-bit cho tùy chọn bổ sung
    CarColor  color  : 2; // 2-bit cho màu sắc
    CarPower  power  : 2; // 2-bit cho năng lượng
    CarEngine engine : 1; // 1-bit cho động cơ
} CarOptions;

void configureCar(CarOptions *car, CarColor color, CarPower power, CarEngine engine, uint8_t options){
    car->color  = color;
    car->power  = power;
    car->engine = engine;
    car->additionalOptions = options;
}

void setOption(CarOptions *car, uint8_t optionMask){
    car->additionalOptions |= optionMask;
}

void resetOption(CarOptions *car, uint8_t optionMask){
    car->additionalOptions &= ~optionMask;
}

void displayCarOptions(const CarOptions car) {
    const char *colors[]  = {"Red", "Blue", "Black", "White"};
    const char *powers[]  = {"100HP", "150HP", "200HP"};
    const char *engines[] = {"1.5L", "2.0L"};

    printf("Car Configuration: \n");
    printf("Color: %s\n", colors[car.color]);
    printf("Power: %s\n", powers[car.power]);
    printf("Engine: %s\n", engines[car.engine]);
    printf("Sunroof: %s\n", (car.additionalOptions & SUNROOF_MASK) ? "Yes" : "No");
    printf("Premium Audio: %s\n", (car.additionalOptions & PREMIUM_AUDIO_MASK) ? "Yes" : "No");
    printf("Sports Package: %s\n", (car.additionalOptions & SPORTS_PACKAGE_MASK) ? "Yes" : "No");
    printf("\n");
}

int main()
{
    CarOptions myCar;

    // cấu hình các thông số của xe
    configureCar(&myCar, COLOR_BLACK, POWER_150HP, ENGINE_2_0L, 0); 

    // thêm tính năng bổ sung
    setOption(&myCar, SUNROOF_MASK);
    setOption(&myCar, PREMIUM_AUDIO_MASK);

    // hiển thị các thông số và tính năng của xe
    displayCarOptions(myCar);

    // xóa tính năng  bổ sung
    resetOption(&myCar, PREMIUM_AUDIO_MASK); 

    // hiển thị các thông số và tính năng của xe
    displayCarOptions(myCar);

    return 0;
}
```

</p>
</details>

</p>
</details>

<details><summary><b>8.4. Ứng dụng</b></summary>
<p>

📚 **Quản lý cờ trạng thái (Status Flags)**: 

- Trong các hệ thống nhúng, việc theo dõi trạng thái của các thiết bị hoặc các module khác nhau là rất phổ biến. Bitmask cho phép lưu trữ nhiều cờ trạng thái trong một biến duy nhất, giúp tiết kiệm bộ nhớ và dễ dàng kiểm tra hoặc cập nhật trạng thái đó.

💻 Ví dụ:
```cpp
uint8_t status_flags = 0x00; // Biến lưu trữ cờ trạng thái

// Đặt bit thứ 2 để báo hiệu thiết bị đang hoạt động
status_flags |= (1 << 2);

// Kiểm tra xem bit thứ 2 có được đặt không
if (status_flags & (1 << 2)) {
    // Thiết bị đang hoạt động
}

// Xóa bit thứ 2 để báo hiệu thiết bị ngừng hoạt động
status_flags &= ~(1 << 2);
```

<br>

📚 **Quản lý I/O (Input/Output) Ports**: 

- Trong các hệ thống nhúng, bitmask được sử dụng để thiết lập hoặc đọc các chân I/O của vi điều khiển một cách hiệu quả.

💻 Ví dụ: Đặt một chân I/O thành mức cao mà không ảnh hưởng đến các chân khác:
``` PORTA |= (1 << 3); // Đặt chân PA3 lên mức cao ```

💻 Ví dụ: Xóa (đưa về mức thấp) một chân I/O:
``` PORTA &= ~(1 << 3); // Đưa chân PA3 về mức thấp ```

<br>

📚 **Đọc và Ghi các Thanh ghi (Registers)**: 

- Bitmask giúp thao tác trực tiếp với các thanh ghi của vi điều khiển một cách dễ dàng, cho phép cấu hình và điều khiển các phần cứng ngoại vi như bộ định thời (timers), UART, ADC, v.v.

💻 Ví dụ: Cấu hình bit điều khiển trong một thanh ghi:
``` TCCR0 |= (1 << CS01); // Đặt bit CS01 để chọn bộ chia tần số cho bộ định thời ```

</p>
</details>

</p>
</details>

<br>

<details><summary><b>9. Struct - Union</b></summary>
<p>

<details><summary><b>9.1. Struct</b></summary>
<p>

<details><summary><b>📚 Cơ bản về Struct</b></summary>
<p>
	
- struct là một cấu trúc dữ liệu cho phép tự định nghĩa một kiểu dữ liệu mới bằng cách nhóm các biến có các kiểu dữ liệu khác nhau lại với nhau.
- struct cho phép tạo ra một thực thể dữ liệu lớn hơn và có tổ chức hơn từ các thành viên (members) của nó.
- Cú pháp:

**Cách 1: Khai báo tường minh**
```cpp
struct structureName{       struct student_t{       struct student_t sv1;
    dataType1 member1;          char ten[30];       struct student_t sv2;
    dataType2 member2;          int tuoi;           ...
    ...                         int MSSV;           hoặc
};                          };                      struct student_t sv[50];
```

**Cách 2: Khai báo không tường minh**
```cpp
typedef struct{             typedef struct{         student_t sv1;
    dataType1 member1;          char ten[30];       student_t sv2;
    dataType2 member2;          int tuoi;           ...
    ...                         int MSSV;           student_t sv[20];
}structureName;             }student_t;
```

- Địa chỉ của struct chính là địa chỉ của member đầu tiên.

- Truy xuất dữ liệu:

	+ Sử dụng "." để truy xuất tới thành viên khi khai báo biến bình thường (int, char,…).

	+ Sử dụng "->" để truy xuất tới thành viên khi khai báo biến là con trỏ.

<br>

</p>
</details>

<details><summary><b>📚 Data Structure Alignment</b></summary>
<p>

Data alignment là quá trình sắp xếp các member của một struct sao cho mỗi member nằm ở địa chỉ phù hợp với yêu cầu căn chỉnh của nó. Điều này giúp CPU truy xuất dữ liệu nhanh hơn và tránh các lỗi tiềm ẩn khi truy cập bộ nhớ.

Bộ xử lý thường yêu cầu các thành phần dữ liệu nằm trên địa chỉ được căn chỉnh theo bội số của kích thước phần tử đó. Ví dụ:

- double (8 byte): thường yêu cầu được căn chỉnh trên địa chỉ chia hết cho 8 (0x00, 0x08, 0x10,...)
- int, int32_t, uint32_t (4 byte): thường yêu cầu được căn chỉnh trên địa chỉ chia hết cho 4 (0x00, 0x04, 0x08,...).
- float, uint16_t (2 byte): thường yêu cầu được căn chỉnh trên địa chỉ chia hết cho 2 (0x00, 0x02, 0x04,...).
- char, uint8_t (1 byte): không yêu cầu căn chỉnh đặc biệt

<br>

</p>
</details>

<details><summary><b>📚 Data Padding</b></summary>
<p>

Padding là các byte trống, không chứa dữ liệu và được thêm vào giữa các thành viên của cấu trúc để đảm bảo sự căn chỉnh bộ nhớ (alignment) phù hợp với kiến trúc phần cứng. Việc này giúp CPU truy cập dữ liệu nhanh hơn, nhưng cũng làm tăng kích thước tổng thể của cấu trúc.

<br>

</p>
</details>

<details><summary><b>📚 Kích thước của struct</b></summary>
<p>

**Kích thước Struct bằng tổng các kích thước của các member và padding (nếu có).**

💻 Ví dụ 1:
```cpp
#include <stdio.h>
#include <stdint.h>

typedef struct{
    uint32_t var1;  // 4 byte
    uint8_t  var2;  // 1 byte
    uint16_t var3;  // 2 byte
} frame;

int main(int argc, char const *argv[])
{
    printf("Size of frame: %d\n", sizeof(frame));

    frame frame;
    printf("Address of var1: %p\n", &frame.var1);
    printf("Address of var2: %p\n", &frame.var2);
    printf("Address of var3: %p\n", &frame.var3);
    return 0;
}
```

**Kết quả:**
```cpp
Size of frame: 8
Address of var1: 00000048F3DFF6D8
Address of var2: 00000048F3DFF6DC
Address of var3: 00000048F3DFF6DE
```

**Giải thích:**

![image](https://github.com/user-attachments/assets/0f6e0cdf-c716-41f1-a3db-fa700344513b)

📝 Compiler sẽ cấp phát địa chỉ , mỗi lần cấp phát bao nhiêu byte sẽ tùy thuộc vào kiểu dữ liệu nào có kích thước lớn nhất.

📝 Lần 1: compiler cấp phát 4 bytes địa chỉ và ``` var1 ``` sử dụng.

📝 Lần 2: compiler cấp phát 4 bytes, ``` var2 ``` chỉ sử dụng 1 byte, thừa 3 bytes.	

📝 ``` var3 ``` sử dụng 2 bytes (dựa vào căn chỉnh) trong 3 bytes trên và còn thừa 1 byte và byte này sẽ trở thành padding.

📝 Tổng kích thước struct frame trên là 8 bytes nhưng thực tế chỉ sử dụng 7 bytes.

<br>

💻 Ví dụ 2:
```cpp
#include <stdio.h>
#include <stdint.h>

typedef struct{
    uint8_t  var1;  // 1 byte
    uint32_t var2;  // 4 byte
    uint16_t var3;  // 2 byte
} frame;

int main(int argc, char const *argv[])
{
    printf("Size of frame: %d\n", sizeof(frame));

    frame frame;
    printf("Address of var1: %p\n", &frame.var1);
    printf("Address of var2: %p\n", &frame.var2);
    printf("Address of var3: %p\n", &frame.var3);
    return 0;
}
```

**Kết quả:**
```cpp
Size of frame: 8
Address of var1: 00000048F3DFF6D8
Address of var2: 00000048F3DFF6DC
Address of var3: 00000048F3DFF6DE
```

**Giải thích:**

![image](https://github.com/user-attachments/assets/0b61bf95-9f0a-406c-8ad5-0263c7f55ad8)

📝 Lần 1: compiler cấp phát 4 bytes và var1 sử dụng 1 byte trong 4 byte, thừa 3 byte và 3 bytes này không đủ để lưu trữ ``` var2 ``` nên compiler sẽ tiếp tục cấp phát 4 bytes và 3 bytes còn thừa sẽ trở thành 3 byte padding.

📝 Lần 2: compiler cấp phát 4 bytes, var2 sử dụng hết.

📝 Lần 3: compiler cấp phát 4 bytes, var3 sử dụng 2 trong 4 bytes và thừa 2 byte.

📝 Tổng kích thước struct frame trên là 12 bytes nhưng thực tế chỉ sử dụng 7 bytes.

<br>

💻 Ví dụ 3:
```cpp
#include <stdio.h>
#include <stdint.h>

typedef struct{
    uint8_t  var1[9];   // 1 byte
    uint32_t var2[2];   // 4 byte
    uint16_t var3[10];  // 2 byte
} frame;

int main(int argc, char const *argv[])
{
    printf("Size of frame: %d\n", sizeof(frame)); 
    return 0;
}
```

**Kết quả:**
```cpp
Size of frame: 40
```

**Giải thích:**

![image](https://github.com/user-attachments/assets/af1f44b2-c870-4fd1-a125-7b76f0ffcbb7)

<br>

💻 Ví dụ 4:
```cpp
#include <stdio.h>
#include <stdint.h>

typedef struct{
    uint8_t  var1[9];   // 1 byte   8 + 1 + 7 padding	  				 = 16 byte
    uint64_t var2[3];   // 8 byte   8*3			  				 = 24 byte
    uint16_t var3[10];  // 2 byte   0123 + 4567 + 89 + 4 padding = 8 + 8 + 4 + 4 padding = 24 byte
    uint32_t var4[2];   // 4 byte   8							 = 8  byte
} frame;

int main(int argc, char const *argv[])
{
    printf("Size of frame: %d\n", sizeof(frame));   
    return 0;
}
```

**Kết quả:** ```72 byte```

<br>

</p>
</details>

</p>
</details>

<details><summary><b>9.2. Union</b></summary>
<p>

<details><summary><b>📚 Cơ bản về Union</b></summary>
<p>

- Union là một cấu trúc dữ liệu giúp kết hợp nhiều kiểu dữ liệu khác nhau vào một cùng một vùng nhớ.
- Mục đích chính của union là tiết kiệm bộ nhớ bằng cách chia sẻ cùng một vùng nhớ cho các thành viên của nó. Điều này có nghĩa là, trong một thời điểm, chỉ một thành viên của union có thể được sử dụng.
- Cú pháp:

**Cách 1**
```cpp
union unionName{       	    union student_t{       union student_t sv1;
    dataType1 member1;          char ten[30];       union student_t sv2;
    dataType2 member2;          int tuoi;           ...
    ...                         int MSSV;           hoặc
};                           };                     union student_t sv[50];
```

**Cách 2**
```cpp
typedef union{             typedef union{           student_t sv1;
    dataType1 member1;          char ten[30];       student_t sv2;
    dataType2 member2;          int tuoi;           ...
    ...                         int MSSV;           student_t sv[20];
}unionName;                }student_t;
```

<br>

</p>
</details>

<details><summary><b>📚 Kích thước Union</b></summary>
<p>

Kích thước của Union chính là tổng của kích thước của member có kích thước lớn nhất và padding (nếu có).

💻
```cpp
#include <stdio.h>
#include <stdint.h>

typedef union{     //  Scope
    uint8_t  var1; //  0 - 255
    uint32_t var2; //  0 - 4294967295
    uint16_t var3; //  0 - 65535
} frame;

int main(int argc, char const *argv[]){
    frame data;
    printf("Size = %d\n",sizeof(frame));
    return 0;
}
```
Kết quả: ```4 byte```

<br>

💻
```cpp
#include <stdio.h>
#include <stdint.h>

typedef union{        
    uint8_t  var1[3]; //  3 byte + 1 padding
    uint32_t var2[6]; //  4 byte * 6
    uint16_t var3[9]; //  4 byte (01) + 4 byte (23) + 4 byte (45) + 4 byte (67) + 2 byte (8) + 2 padding
} frame;

int main(int argc, char const *argv[]){
    frame data;
    printf("Size = %d\n",sizeof(frame));
    return 0;
}
```
**Kết quả:** ```24 byte```

<br>

</p>
</details>

<details><summary><b>📚 Sử dụng vùng nhớ trong Union</b></summary>
<p>

💻
```cpp
#include <stdio.h>
#include <stdint.h>

typedef union{     //  Scope
    uint8_t  var1; //  0 - 255
    uint32_t var2; //  0 - 4294967295
    uint16_t var3; //  0 - 65535
} frame;

int main(int argc, char const *argv[]){
    frame data;
    data.var1 = 5;
    data.var2 = 6;
    data.var3 = 7;

// 0x005FFE9C  0x005FFE9D  0x005FFE9E  0x005FFE9F
//      5
//      6
//      7

    printf("Data.var1 = %d\n", data.var1);
    printf("Data.var2 = %u\n", data.var2);
    printf("Data.var3 = %d\n", data.var3);
    return 0;
}
```
**Kết quả**
```cpp
Data.var1 = 7
Data.var2 = 7
Data.var3 = 7
```

**Giải thích**

📝 Khi gán 5 cho member var1 thì tại địa chỉ 0x5FFE9C sẽ có giá trị 5.

📝 Khi gán 6 cho member var2, do các member sử dụng chung vùng nhớ nên tại địa chỉ 0x5FFE9C thì giá trị 6 sẽ ghi đè lên 5.

📝 Tương tự với member var3, giá trị cuối cùng tại địa chỉ 0x5FFE9C là 7 nên giá trị của các member đều sẽ là 7.

<br>

💻
```cpp
#include <stdio.h>
#include <stdint.h>

typedef union{     //  Scope
    uint8_t  var1; //  0 - 255
    uint32_t var2; //  0 - 4294967295
    uint16_t var3; //  0 - 65535
} frame;

int main(int argc, char const *argv[])
{
    frame data;
    data.var2 = 4294967290;

    printf("Data.var1 = %d\n", data.var1);
    printf("Data.var2 = %u\n", data.var2);
    printf("Data.var3 = %d\n", data.var3);
    return 0;
}
```
**Kết quả:**
```cpp
Data.var1 = 250
Data.var2 = 4294967290
Data.var3 = 65530
```

**Giải thích**

📝 4294967290 = 0b11111111 111111111 11111111 1111010

📝 Do các member dùng chung vùng nhớ nên giá trị các member bằng nhau và kích thước của union frame là 4 byte nên dữ liệu sẽ được như sau:

```cpp
// 0x005FFE9C  0x005FFE9D  0x005FFE9E  0x005FFE9F
//  11111010    11111111    11111111    11111111
```

📝 member var1 có kiểu dữ liệu là uint8_t nên chỉ lấy 8 bit ở địa chỉ 0x005FFE9C -> var1 = 0b11111010 = 250

📝 member var2 có kiểu dữ liệu là uint32_t nên giữ nguyên giá trị

📝 member var3 có kiểu dữ liệu là uint16_t nên chỉ lấy 16 bit ở địa chỉ 0x005FFE9C và 0x005FFE9D -> var3 = 0b11111111 11111010 = 65530

<br>

</p>
</details>

</p>
</details>

<details><summary><b>9.3. So sánh Struct và Union</b></summary>
<p>

<details><summary><b>📚 Giống nhau</b></summary>
<p>
	
- Đều do lập trình viên tự định nghĩa (user defined type).
- Đều sử dụng dấu "." hoặc "->" để truy cập các phần tử (hoặc có thể gọi là thuộc tính).

</p>
</details>

<details><summary><b>📚 Khác nhau</b></summary>
<p>

|           |   Struct  |   Union   |
|-----------|-----------|-----------|
| keyword   | Dùng keyword 'struct' để định nghĩa   | Dùng keyword 'union' để định nghĩa    |
| size      | Tổng các kích thước của các member và padding (nếu có)    | Tổng của kích thước của member có kích thước lớn nhất và padding (nếu có)  |
| Memory    | Mỗi member có vùng nhớ riêng biệt    | Các member dùng chung 1 vùng nhớ    |
| Modify    | Thay đổi giá trị 1 member không ảnh hưởng các member khác   | Thay đổi giá trị bất kỳ member nào cũng ảnh hưởng các member khác    |
| Truy cập  | Truy cập bất kỳ member ở thời điểm nào cũng được    | Trong một lúc chỉ truy cập được một member     |


</p>
</details>

</p>
</details>

<details><summary><b>9.4. Ứng dụng</b></summary>
<p>

<details><summary><b>📚 Ứng dụng Struct</b></summary>
<p>

Các giao thức trong nhúng như UART, I2C, SPI khi sử dụng đều phải cấu hình các thông số; mỗi giao thức sẽ có các thông số cấu hình khác nhau.

💻 UART
```cpp
typedef struct{
  uint32_t USART_BaudRate;            /* USART communication baud rate */

  uint16_t USART_WordLength;          /* the number of data bits transmitted or received in a frame */

  uint16_t USART_StopBits;            /* Specifies the number of stop bits transmitted */

  uint16_t USART_Parity;              /* Specifies the parity mode */
 
  uint16_t USART_Mode;                /* pecifies wether the Receive or Transmit mode is enabled or disabled */

  uint16_t USART_HardwareFlowControl; /* Specifies wether the hardware flow control mode is enabled or disabled */
} USART_InitTypeDef;
```
	
<br>

</p>
</details>

<details><summary><b>📚 Ứng dụng Union</b></summary>
<p>

- Một chân GPIO_pin tại một thời điểm có thể là input hoặc output.
- Input có thể là đọc giá trị cảm biến từ bên ngoài.
- Output có thể là xuất điện áp ra để điều khiển LED hoặc relay.
- Để cấu hình thì dùng Union

💻 
```cpp
#include <stdio.h>
#include <stdint.h>

typedef union{
    struct{
        uint32_t mode  : 2;  // 00: input, 01: output
        uint32_t pull  : 2;  // 00: no pull, 01: pull-up, 10: pull-down
        uint32_t speed : 2;  // 00: 2MHz, 01: 10MHz, 10: 50MHz
        uint32_t reserved : 26; // các bit khác
    } config;

    uint32_t reg;
} GPIO_Config;

void configure_gpio(GPIO_Config *gpio, uint32_t mode, uint32_t pull, uint32_t speed) {
    gpio->config.mode  = mode;  // Cấu hình chế độ
    gpio->config.pull  = pull;  // Cấu hình pull-up/pull-down
    gpio->config.speed = speed; // Cấu hình tốc độ
}

int main(int argc, char const *argv[])
{
    GPIO_Config GPIOA;

    // Cấu hình PA0 là output
    configure_gpio(&GPIOA, 1, 0, 2);

    // Sử dụng GPIOA.reg để viết vào thanh ghi GPIO thực tế
    write_gpio_register(GPIOA.reg);
    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>📚 Ứng dụng kết hợp Struct và Union</b></summary>
<p>

- Truyền dữ liệu từ MCUA sang MCUB

```cpp
MCUA							   MCUB

Data:
{
   id;	 	// 2 byte	   Data_Frame
   data; 	// 4 byte	--------------->	   Data
   check_sum; 	// 2 byte				Data_Frame
}

Data_Frame
```

📝 Cách làm thủ công: đưa từng byte vào mảng rồi truyền đi

📝 Ví dụ: id=10, data=1234, checksum=70 thì chuyển thành chuỗi "10", "1234", "70" rồi đưa vào mảng.

```cpp
    id            data      checksum
0x01 0x02 0x03 0x04 0x05 0x06 0x07 0x08
 1    0    1    2    3    4    7    0
              mảng frame[8]
```

🤔 Có cách nào tự động convert vào mảng khi có dữ liệu không❓

➡️ Sử dụng Struct + Union

💻
```cpp
#include <stdio.h>
#include <stdint.h>
#include <string.h>

typedef union{
    struct {
        uint8_t id[2];
        uint8_t data[4];
        uint8_t check_sum[2];
    } data;
    uint8_t frame[8];
} Data_Frame;


int main(int argc, char const *argv[])
{
    Data_Frame transmit_data, receive_data;
    strcpy((char*)transmit_data.data.id, "10");
    strcpy((char*)transmit_data.data.data, "1234");
    strcpy((char*)transmit_data.data.check_sum, "70");
    strcpy((char*)receive_data.frame, (char*)transmit_data.frame);
    return 0;
}
```

📝 Sủ dụng kiểu ``` uint8_t ``` cho các member để không có padding, tránh việc tốn bộ nhớ.

📝 Kích thước của mỗi member trong union đều là 8 byte nên kích thước của union cũng là 8 byte.

📝 Khi có dữ liệu, ví dụ copy các chuỗi "10", "1234", "70" vào ``` id, data, check_sum ``` thì mảng ``` frame ``` cũng tự động cập nhật dư liệu theo.

📝 Khi cần truyền dữ liệu đi, ta chỉ cần sử dụng mảng ``` frame ``` để truyền đi.

<br>

</p>
</details>

</p>
</details>

</p>
</details>

<br>

<details><summary><b>10. Memory layout (Phân vùng nhớ)</b></summary>
<p>

<details><summary><b>10.1. Text (Code Segment)</b></summary>
<p>

- **Vùng nhớ của địa chỉ thấp nhất**.
- **Chứa mã máy (mã lệnh thực thi chương trình).**
- **Quyền truy cập là chỉ đọc (Read-only).**
- Đối với compiler ``` Clang ``` (compiler trên MacOS) thì phân vùng Text còn lưu trữ hằng số, chuỗi hằng (string literal) và con trỏ kiểu char (khai báo toàn cục).
- Đối với compiler ``` MinGW ``` (gcc/g++ trên Windows) thì hằng số, chuỗi hằng và con trỏ kiểu char sẽ lưu trữ ở phân vùng Data nhưng với quyền truy cập là Read-only.

💻
File **main.c**
```cpp
#include <stdio.h>

char *ptr = "Hello World";

const int a = 10;

int main(int argc, char const *argv[])
{
    printf("a = %d\n", a);

    // a = 20;  / wrong

    ptr[1] = 'a';
    printf("chuoi: %s\n", *ptr);

    *ptr = "Hello";
    printf("chuoi: %s\n", *ptr);

    return 0;
}
```

📝 Compiler gcc

📝 Biến ``` a ``` khi khai báo toàn cục thì được lưu ở phân vùng Read-only Data (rdata) nên khi cố tình thay đổi giá trị của nó sẽ gặp lỗi ``` assignment of read-only variable 'a' ``` (gán biến chỉ đọc 'a'). Ngược lại, biến ``` a ``` khi khai báo cục bộ thì nó sẽ được lưu ở phân vùng Stack và có thể bị thay đổi giá trị thông qua một con trỏ.

📝 con trỏ ptr và chuỗi ``` Hello World ``` được lưu ở phân vùng Data nhưng quyền truy cập của chuỗi là read-only nên khi cố tính thay đổi (``` ptr[1] = 'a' ``` hoặc ``` *ptr = "hello" ```), mặc dù không hiện lỗi cụ thể (do compiler) nhưng khi cố thay đổi giá trị của hằng số thì chương trình tự set một cơ chế bảo vệ và khiến cho chương trình dừng lại ngay sau khi chạy dòng lệnh ``` printf("a = %d\n", a); ```.

<br>

</p>
</details>

<details><summary><b>10.2. Data (Initialized Data)</b></summary>
<p>

- Lưu trữ biến toàn cục và biến static (bao gồm toàn cục và cục bộ) với giá trị khởi tạo khác 0.
- Quyền truy cập: đọc - ghi, nghĩa là có thể đọc giá trị và có thể thay đổi giá trị của biến.
- Tất cả các biến sẽ được thu hồi sau khi kết thúc chương trình.

💻
File **main.c**:
```cpp
#include <stdio.h>

int a = 5;

static int b = 6;

void *ptr = &b;

void test(){
    static int c = 7;
}

int main(int argc, char const *argv[])
{
    /* code */
    return 0;
}
```

📝 Biến a là toàn cục nên lưu ở phân vùng Data (.data)

📝 Biến static bao gồm toàn cục (b) và cục bộ (c) cũng sẽ được lưu trữ ở Data

📝 Con trỏ ``` ptr ``` cũng được lưu trữ ở Data và địa chỉ của biến mà nó trỏ tới (biến b) cũng nằm ở Data.

📝 Tất cả các biến a, b, c đều sẽ được cấp phát địa chỉ (giả sử 0x01, 0xb2, 0xf5) và các địa chỉ trên sẽ bị thu hồi khi chương trình kết thúc

📝 Biến a, b, c ptr đều có thể thay đổi trong quá trình thực thi (đọc – ghi).

<br>

</p>
</details>

<details><summary><b>10.3. BSS (Uninitialized Data)</b></summary>
<p>

- Lưu trữ biến toàn cục và biến static (bao gồm toàn cục và cục bộ) mà không khởi tạo giá trị ban đầu hoặc khởi tạo với giá trị khác 0.
- Quyền truy cập: đọc-ghi, nghĩa là có thể đọc giá trị và có thể thay đổi giá trị của biến.
- Tất cả các biến sẽ được thu hồi sau khi kết thúc chương trình.

💻
File **main.c**:
```cpp
#include <stdio.h>

typedef struct{
    int x;
    int y;
} Point_Data;

Point_Data p1 = {5,7};

Point_Data p2;

int a = 0;
int b = 5;

static int m = 0;
static int n;

void test(){
    static int c = 0;
    static int d;
}

int main() {
    printf("a: %d\n", a);
    printf("m: %d\n", m);
    return 0;
}
```

📝 Khi dùng Struct (Union) để định nghĩa một kiểu dữ liệu mới thì những member bên trong sẽ chưa được lưu trên RAM.

📝 Khi khai báo biến thuộc kiểu struct thì khi đó những member thuộc phân vùng nhớ nào sẽ tùy thuộc vào cách khai báo biến.

📝 Ví dụ: biến ``` p1 ``` được khởi tạo với giá trị khác 0 (``` {5,7} ```) nên các member ```x,y ``` và ``` p1 ``` sẽ được lưu trữ ở Data.

📝 Ví dụ: biến ``` p2 ``` không được khởi tạo nên các member ```x,y ``` và ``` p2 ``` sẽ được lưu trữ ở BSS.

📝 Các biến static như m, n, c, d được lưu ở BSS.

📝 Biến toàn cục b lưu ở Data

<br>

</p>
</details>

<details><summary><b>10.4. Stack</b></summary>
<p>

- Lưu trữ biến cục bộ (bao gồm cả biến ``` const ``` nhưng ngoại trừ static cục bộ) và tham số truyền vào của hàm.
- Quyền truy cập: đọc - ghi
- Stack là vùng nhớ được cấp phát tự động và có cấu trúc LIFO (Last In Fisrt Out).
- Vùng nhớ sẽ bị thu hồi khi ra hỏi block code/hàm.

📝 Biến static cục bộ chỉ được khởi tạo một lần duy nhất và giữ nguyên giá trị giữa các lần gọi hàm. Vì vậy, chúng không thể lưu trữ trên ngăn xếp (stack), vì bộ nhớ stack chỉ tồn tại trong thời gian hàm đang thực thi và sẽ được giải phóng khi hàm kết thúc.

<br>

💻
```cpp
#include <stdio.h>

int total(int a, int b){
    int c;
    c = a + b;
    return c;
}

int main(int argc, char const *argv[])
{
    printf("a+b=%d\n",total(5,6));
    /*
        0x01: 5
        0x12: 6
        0x06: 11
    */
    printf("a+b=%d\n",total(9,7));
    /*
        0x01: 9
        0x02: 7
        0x08: 16
    */
    return 0;
}
```
📝 Biến a, b là input parameter của hàm nên lưu trữ trong Stack.

📝 Biến c là biến cục bộ nên cũng lưu trữ trong Stack.

📝 Khi chạy lệnh printf() đầu tiên, các biến a, b, c đều được cấp phát địa chỉ (giả sử 0x01 (a=5), 0x12 (b=6) và 0x06 (c)). Những địa chỉ này sẽ được lưu ở phần vùng Stack. Sau khi return kết quả thì địa chỉ của các biến a, b, c sẽ bị thu hồi.

📝 Khi chạy lệnh printf() thứ hai, a=9 và tiếp tục được cấp phát địa chỉ (có thể là 0x01 hoặc khác), tương tự b, c và sau khi return kết quả thì các địa chỉ cũng bi thu hồi.

<br>

</p>
</details>

<details><summary><b>10.5. Heap</b></summary>
<p>

<details><summary><b>📚 Cấp phát động với malloc - calloc - realloc</b></summary>
<p>

💻 Ví dụ 1: malloc
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <stdint.h>

int main(int argc, char const *argv[])
{
    uint16_t *ptr = NULL;
    int num_element = 5;
    ptr = (uint16_t*)malloc(sizeof(uint16_t)*num_element);

    ptr[0] = 256; // 0b00000001 00000000

    for (int i=1; i<num_element; i++){
        ptr[i] = 2*i;
    }

    for (int i=0; i<num_element*2; i++){
        printf("dia chi: %p, gia tri: %d\n", (uint8_t*)ptr+i, *((uint8_t*)ptr+i));
    }
    return 0;
}
```

**Kết quả**:
```cpp
dia chi: 0000023164232C90, gia tri: 0
dia chi: 0000023164232C91, gia tri: 1
dia chi: 0000023164232C92, gia tri: 2
dia chi: 0000023164232C93, gia tri: 0
dia chi: 0000023164232C94, gia tri: 4
dia chi: 0000023164232C95, gia tri: 0
dia chi: 0000023164232C96, gia tri: 6
dia chi: 0000023164232C97, gia tri: 0
dia chi: 0000023164232C98, gia tri: 8
dia chi: 0000023164232C99, gia tri: 0
```

📝 ``` malloc(sizeof(uint16_t)*num_element) ```: cấp phát vùng nhớ 2*5=10 byte gồm 10 địa chỉ liền kề nhau (0x01, 0x02, 0x03, 0x04, ..., 0x0A) và mỗi phần tử sẽ chiếm 2 ô địa chỉ (2 byte).

📝 Do giá trị trả về là con trỏ void nên để xác định được giá trị chính xác ở từng địa chỉ thì phải ép kiểu.

📝 ``` (uint8_t*)ptr+i ```: ép kiểu để xem giá trị từng byte địa chỉ

<br>

💻 Ví dụ 2: malloc và realloc
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <stdint.h>

int main(int argc, char const *argv[])
{
    uint16_t *ptr = NULL;
    ptr = (uint16_t*)malloc(sizeof(uint16_t)*4);

    for (int i=0; i<4; i++){
        ptr[i] = 2*i;
    }

    ptr = (uint16_t*)realloc(ptr, sizeof(uint16_t)*8);

    for (int i=0; i<8; i++){
        ptr[i] = 2*i;
    }

    for (int i=0; i<16; i++){
        printf("dia chi: %p, gia tri: %d\n", (uint8_t*)ptr+i, *((uint8_t*)ptr+i));
    }
    return 0;
}
```

📝 ``` realloc() ```: thay đổi kích thước vùng nhớ đã được cấp phát bởi malloc() từ 8 byte lên 16 byte

<br>

💻 Ví dụ 3: calloc
```cpp
#include <stdio.h>
#include <stdlib.h>

int main()
{   
    int size = 5;
    int *ptr = (int*)calloc(size, sizeof(int));

    // Kiểm tra nếu việc cấp phát thất bại
    if (ptr == NULL) {
        printf("Cấp phát bộ nhớ thất bại!\n");
        return 1; // Thoát chương trình
    }

    for (int i=0; i<size; i++){
        printf("arr[%d]: %d\n",i, *(ptr+i));
    }
}
```

**Kết quả:**
```cpp
arr[0]: 0
arr[1]: 0
arr[2]: 0
arr[3]: 0
arr[4]: 0
```

<br>

💻 Ví dụ 4:
```cpp
#include <stdio.h>
#include <stdlib.h>

void test1(){
    int array[3];
    for (int i = 0; i < 3; i++){
        printf("address of array[%d]: %p\n", i, (array+i));
    }
    printf("----------------------\n");
}

void test2(){
    int *array = (int*)malloc(3*sizeof(int)); 
    for (int i = 0; i < 3; i++){
        printf("address of array[%d]: %p\n", i, (array+i));
    }
    printf("----------------------\n");
    free(array);
}

int main(int argc, char const *argv[]){  
    test1();
    test1();
    test2();
    test2();
    return 0;
}
```
**Kết quả:**
```cpp
address of array[0]: 00000000005FFE60
address of array[1]: 00000000005FFE64
address of array[2]: 00000000005FFE68
----------------------
address of array[0]: 00000000005FFE60
address of array[1]: 00000000005FFE64
address of array[2]: 00000000005FFE68
----------------------
address of array[0]: 0000000000656EC0
address of array[1]: 0000000000656EC4
address of array[2]: 0000000000656EC8
----------------------
address of array[0]: 0000000000656EC0
address of array[1]: 0000000000656EC4
address of array[2]: 0000000000656EC8
```

📝 Ở hàm ``` test1() ```, các phần tử trong mảng đều được lưu ở Stack nên khi kết thúc gọi hàm thì địa chỉ tự động thu hồi nên ở lần gọi hàm thứ 2 thì địa chỉ các phần tử vẫn giống ở lần 1.

📝 Ở hàm ``` test2() ```, con trỏ ``` array ``` lưu ở Stack nhưng các địa chỉ mà nó trỏ đến lại lưu ở phân vùng Heap và khi gọi hàm ``` free() ``` thì các địa chỉ đó bị thu hồi nên ở lần gọi hàm thứ 2 thì địa chỉ các phần tử vẫn giống ở lần 1. Tuy nhiên, nếu không thu hồi địa chỉ thì ở lần gọi hàm thứ 2, các địa chỉ chắc chắn sẽ khác so với lần 1.

<table>
  <tr>
    <th></th>
    <th><b>malloc</b></th>
    <th><b>calloc</b></th>
    <th><b>realloc</b></th>
  </tr>
  <tr>
    <td><b>Chức năng</b></td>
    <td>Cấp phát bộ nhớ liên tục với kích thước được chỉ định.</td>
    <td>Cấp phát một khối bộ nhớ liên tục có kích thước bằng số lượng phần tử.</td>
    <td>Thay đổi kích thước vùng nhớ đã được cấp phát bởi malloc () hoặc calloc ().</td>
  </tr>
  <tr>
    <td><b>Khởi tạo giá trị</b></td>
    <td>Không khởi tạo (giá trị rác).</td>
    <td>Khởi tạo toàn bộ vùng nhớ về 0.</td>
    <td>Giữ lại dữ liệu cũ, phần mở rộng không khởi tạo.</td>
  </tr>
  <tr>
    <td><b>Cú pháp</b></td>
    <td>void *malloc(size_t size)</td>
    <td>void *calloc(int n, size_t size)</td>
    <td>void *realloc(void *ptr, size_t new_size)</td>
  </tr>
  <tr>
    <td><b>Tham số truyền vào</b></td>
    <td>Kích thước mong muốn (byte).</td>
    <td>Số lượng phần tử và Kích thước mỗi phần tử (byte).</td>
    <td>Số lượng phần tử và Kích thước mỗi phần tử (byte).</td>
  </tr>
  <tr>
    <td><b>Kết quả trả về</b></td>
    <td>Con trỏ tới bộ nhớ đã cấp phát, hoặc trả về NULL nếu yêu cầu thất bại, khi dùng phải ép kiểu sang kiểu dữ liệu cần dùng.</td>
    <td>Con trỏ tới bộ nhớ đã cấp phát, hoặc trả về NULL nếu yêu cầu thất bại.</td>
    <td>
	    - Nếu con trỏ ptr là NULL, thì một khối mới được cấp phát và một con trỏ tới nó được trả về bởi hàm này.
	    - Nếu kích thước là 0 và con trỏ ptr trỏ tới một khối nhớ đang tồn tại, khối nhớ được trỏ tới bởi ptr được giải phóng và một con trỏ NULL được trả về.
    </td>
  </tr>
  <tr>
    <td><b>Giải phóng bộ nhớ</b></td>
    <td>Dùng hàm free()</td>
    <td>Dùng hàm free()</td>
    <td>Dùng hàm free()</td>
  </tr>
  <tr>
    <td><b>Hiệu suất</b></td>
    <td>Nhanh hơn so với calloc vì không thực hiện khởi tạo</td>
    <td>Chậm hơn malloc do có bước khởi tạo giá trị 0.</td>
    <td>Tùy thuộc vào việc cấp phát mới hoặc mở rộng trực tiếp.</td>
  </tr>
  <tr>
    <td><b>Sử dụng khi nào?</b></td>
    <td>Khi cần hiệu suất cao hơn và có thể tự khởi tạo dữ liệu theo cách riêng.</td>
    <td>Khi cần khởi tạo bộ nhớ với giá trị mặc định là 0 để tránh các lỗi liên quan đến giá trị rác.</td>
    <td>Khi cần thay đổi kích thước vùng nhớ đã cấp phát.</td>
  </tr>
</table>

<br>

</p>
</details>

<details><summary><b>📚 Phân vùng Heap</b></summary>
<p>

- Quyền truy cập là đọc và ghi, nghĩa là có thể thay đổi được giá trị của biến.
- Heap được sử dụng để cấp phát bộ nhớ động (thông qua các lệnh: malloc, calloc, relloc, free, new, delete, …). Điều này cho phép chương trình tạo ra và giải phóng bộ nhớ theo nhu cầu, thích ứng với sự biến đổi của dữ liệu trong quá trình chạy.
- Sẽ được giải phóng khi gọi hàm free.

💻
```cpp
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char const *argv[])
{
    int *ptr = (int*)malloc(sizeof(int) * 6);
    return 0;
}
```

📝 Con trỏ ptr được lưu trữ ở phân vùng Stack.

📝 Những địa chỉ mà con trỏ ptr trỏ tới (địa chỉ được cấp phát thông qua malloc) được lưu trữ ở phân vùng Heap.

📝 Để thu hồi vùng nhớ thì ngoài việc sử dụng ``` free() ``` thì ta còn có thể sử dụng ``` realloc() ``` và cấp phát giá trị cho các địa chỉ đó bằng 0.

<br>

**Khi nào sử dụng Heap? Khi nào sử dụng Stack?**

**Sử dụng Stack**: Khi bạn biết trước kích thước của dữ liệu và dữ liệu này có vòng đời ngắn, được sử dụng trong phạm vi hàm.

```cpp
void function() {
    int a = 10; // Biến cục bộ "a" được lưu trên stack
    int b[20];  // Mảng tĩnh "b" có kích thước cố định được lưu trên stack
}
```

**Sử dụng Heap**: Khi bạn cần quản lý bộ nhớ với kích thước động và vòng đời của dữ liệu dài, tồn tại ngoài phạm vi của một hàm.

```cpp
void function() {
    int* ptr = (int*)malloc(sizeof(int) * 10); // Cấp phát mảng động trên heap
    // Sử dụng mảng "ptr"
    free(ptr); // Giải phóng bộ nhớ
}
```

<br>

</p>
</details>

</p>
</details>

</p>
</details>

<br>

<details><summary><b>11. JSON</b></summary>
<p>

<details><summary><b>11.1. Khái niệm</b></summary>
<p>

- JSON là viết tắt của "JavaScript Object Notation" (Ghi chú về Đối tượng JavaScript) là một định dạng dữ liệu phổ biến trong lập trình và giao tiếp giữa các máy chủ và trình duyệt web, cũng như giữa các hệ thống khác nhau.
- JSON được thiết kế để dễ đọc và dễ viết cho con người, cũng như dễ dàng để phân tích và tạo ra cho máy tính. Nó sử dụng một cú pháp nhẹ dựa trên cặp **key - value**, tương tự như các đối tượng và mảng trong JavaScript. Mỗi đối tượng JSON bao gồm một tập hợp các cặp "key" và "value", trong khi mỗi mảng JSON là một tập hợp các giá trị.

</p>
</details>

<details><summary><b>11.2.  Các kiểu dữ liệu JSON</b></summary>
<p>

**Object**:

- Một tập hợp các cặp key – value, được đặt trong dấu ngoặc nhọn {}.
- Mỗi **key** phải là một **chuỗi** và **value** có thể là **bất kỳ kiểu dữ liệu** nào như chuỗi, số, boolean, mảng, hoặc một đối tượng khác.
- Mỗi cặp key - value được phân tách bằng dấu hai chấm (:).
- Các cặp key - value trong object được phân tách bằng dấu phẩy (,).

```cpp
{ 
  "name": "John Doe",
  "age": 30,
  "city": "New York",
  "isStudent": false,
  "grades": [85, 90, 78]
}
```

<br>

**Array**:

- Là một danh sách các giá trị, được bao quanh bởi dấu ngoặc vuông []. Các giá trị trong mảng có thể thuộc bất kỳ kiểu dữ liệu JSON nào, kể cả object hoặc mảng khác.

```cpp
[
  {
    "name": "John Doe",
    "age": 30,
    "city": "New York",
    "occupation": "Software Engineer",
    "isStudent": false
  },
  {
    "name": "Jane Smith",
    "age": null,
    "city": "Los Angeles",
    "contact": {
      "email": "jane.smith@example.com",
      "phone": "555-1234"
    }
  },
  {
    "name": "Bob Johnson",
    "age": 35,
    "city": "Chicago"
  }
]
```

<br>

**Number**:

- Có thể là số nguyên hoặc số thực.

```cpp
{
  "integer": 42,
  "float": 3.14
}
```

<br>

**String**:

```cpp
{
  "greeting1": "Hello, world 1!",
  "greeting2": "Hello, world 2!"
}
```

<br>

**Boolean**:

- Chỉ có hai giá trị là true hoặc false

```cpp
{
  "isAdmin": true,
  "isLoggedIn": false
}
```

<br>

**null**:

- Đại diện cho một giá trị rỗng hoặc không có giá trị.

```cpp
{
  "middleName": null
}
```

</p>
</details>

</p>
</detail>

</p>
</details>

<br>

<details><summary><b>12. Linked List</b></summary>
<p>

<details><summary><b>12.1. Khái niệm</b></summary>
<p>

Danh sách liên kết (Linked List) là một cấu trúc dữ liệu trong lập trình máy tính, được sử dụng để tổ chức và lưu trữ dữ liệu. Một Linked list bao gồm một chuỗi các nút (nodes) được phân bổ động, được sắp xếp theo cách mà mỗi node sẽ chứa một giá trị và một con trỏ (pointer) trỏ đến node tiếp theo nó. Nếu con trỏ là NULL thì nó là node cuối cùng trong danh sách.

Có hai loại linked list chính:

- Singly Linked List (Danh sách liên kết đơn): Mỗi nút chỉ chứa một con trỏ đến nút tiếp theo trong chuỗi.
- Doubly Linked List (Danh sách liên kết đôi): Mỗi nút chứa hai con trỏ, một trỏ đến nút tiếp theo và một trỏ đến nút trước đó.

Một linked list cung cấp một cách linh hoạt để thêm, xóa và chèn các phần tử mà không cần phải di chuyển toàn bộ dãy số như mảng. Tuy nhiên, nó cũng có một số nhược điểm, như việc cần thêm một con trỏ cho mỗi nút, tăng độ phức tạp của bộ nhớ và có thể dẫn đến hiệu suất kém hơn trong một số trường hợp so với mảng.

![image](https://github.com/user-attachments/assets/c2669fde-dacb-4f2b-9cd8-cb861c7e9e3e)

</p>
</details>

<details><summary><b>12.2. Các thao tác chính trong danh sách liên kết đơn</b></summary>
<p>

<details><summary><b>📚 Khởi tạo 1 node mới</b></summary>
<p>

```cpp
typedef struct Node{
    int data;
    struct Node *next;
} Node_t;

// khởi tạo giá trị ban đầu và trả địa chỉ về cho node được cấp phát.
Node_t *createNode(int data){
    Node_t *temp = (Node_t*)malloc(sizeof(Node_t));
    temp->data = data;
    temp->next = NULL;
    // node vừa tạo chưa liên kết với phần tử nào nên phần liên kết gán bằng NULL
    return temp;
}
```

</p>
</details>

<details><summary><b>📚 Thêm node mới vào đầu danh sách</b></summary>
<p>

```cpp
void push_front(Node_t **head, int data){
    Node_t *new_node = createNode(data);
    if (*head == NULL){
        *head = new_node;
    }
    else{
        new_node->next = *head;
        *head = new_node;
    }
}
```

</p>
</details>

<details><summary><b>📚 Thêm node mới vào cuối danh sách</b></summary>
<p>

```cpp
void push_back(Node_t **head, int data){
    Node_t *new_node = createNode(data);
    if (*head == NULL){
        *head = new_node;
    }
    else{
        Node_t *p = *head;
        while (p->next != NULL){
            p = p->next;
        }
        p->next = createNode(data);
    }
}
```

</p>
</details>

<details><summary><b>📚 Thêm node mới vào vị trí bất kỳ trong danh sách</b></summary>
<p>

```cpp
void insert(Node_t **head, int data, int position){
    Node_t *new_node = createNode(data);
    Node_t *p = *head;
    int k = 0;

    while (p->next != NULL && k != position-1){
        p = p->next;
        k++;
    }
    
    if (k == position-1){
        new_node->next = p->next;
        p->next = new_node;
    }
    else return;
}
```

</p>
</details>

<details><summary><b>📚 Xóa node ở đầu danh sách</b></summary>
<p>

```cpp
void pop_front(Node_t **head){
    Node_t *new_head = (*head)->next;
    free(*head);
    *head = new_head;
}
```

</p>
</details>

<details><summary><b>📚 Xóa node ở cuối danh sách</b></summary>
<p>

```cpp
void pop_back(Node_t **head){
    if (*head == NULL){
        free(*head);
    }
    else{
        Node_t *p = *head;
        while (p->next->next != NULL){
            p = p->next;
        }
        free(p->next);
        p->next = NULL;
    }
}
```

</p>
</details>

<details><summary><b>📚 Xóa node ở vị trí bất kỳ trong danh sách</b></summary>
<p>

```cpp
void delete_node(Node_t **head, int position){
    Node_t *p = *head;
    int k = 0;

    while (p != NULL && k != position-1){
        p = p->next;
        k++;
    }

    if (k == position-1){
        Node_t *node_delete = p->next;
        p->next = p->next->next;
        free(node_delete);
    }
    else return;
}
```

</p>
</details>

<details><summary><b>📚 Xóa toàn bộ node trong danh sách</b></summary>
<p>

```cpp
void clear_all(Node_t **head){
    Node_t *p = *head;
    while (p != NULL){
        Node_t *temp = p->next;
        free(p);
        p = temp;
    }
    *head = NULL;
}
```

</p>
</details>

<details><summary><b>📚 Tính kích thước danh sách</b></summary>
<p>

```cpp
int size(Node_t *head){
    Node_t *p = head;
    int count = 0;
    while (p != NULL){
        count++;
        p = p->next;
    }
    return count;
}
```

</p>
</details>

</p>
</details>

</p>
</details>

<br>

<details><summary><b>13. Stack (Ngăn xếp)</b></summary>
<p>

<details><summary><b>13.1. Khái niệm</b></summary>
<p>

- Lưu trữ biến cục bộ và tham số truyền vào của hàm.
- Quyền truy cập đọc - ghi.
- Địa chỉ các biến bị thu hồi khi kết thúc hàm.
- Hoạt động theo nguyên tắc **"Last In, First Out"** (LIFO), nghĩa là phần tử cuối cùng được thêm vào stack sẽ là phần tử đầu tiên được lấy ra.

<br>

</p>
</details>

<details><summary><b>13.2. Các thao tác trên Stack</b></summary>
<p>

<details><summary><b>📚 Khởi tạo một Stack</b></summary>
<p>

```cpp
typedef struct{
    int *items; // mảng lưu trữ giá trị các phần tử
    int size;   // kích thước stack (số lượng phần tử có trong Stack)
    int top;	// chỉ số của phần tử ở đỉnh stack
} Stack;

void initialize(Stack *stack, int size){
    stack->items = (int*)malloc(sizeof(int)*size);
    stack->size  = size;
    stack->top   = -1;
}
```

<br>

</p>
</details>

<details><summary><b>📚 push</b></summary>
<p>

Thêm một phần tử vào đỉnh Stack.

![push](https://drive.google.com/uc?id=1ef0gBl5fAJLY5tnV7o5KZ3yvEbZtqO3X)

💻
```cpp
void push(Stack *stack, int value){
    if (!isFull(*stack)){
        stack->items[++stack->top] = value;
    } 
    else{
        printf("Stack overflow\n");
    }
}
```

<br>

</p>
</details>

<details><summary><b>📚 pop</b></summary>
<p>

Xóa một phần tử ở đỉnh Stack.

![pop](https://drive.google.com/uc?id=1_5GZao45wgCi6cauwsMyWBbsY6UMsGGO)

💻
```cpp
int pop(Stack *stack){
    if (!isEmpty(*stack)){
        return stack->items[stack->top--];
    } 
    else{
        printf("Stack underflow\n");
        return -1;
    }
}
```

<br>

</p>
</details>

<details><summary><b>📚 top</b></summary>
<p>

Lấy giá trị của phần tử ở đỉnh Stack.

💻
```cpp
int top(Stack stack){
    if (!isEmpty(stack)){
        return stack.items[stack.top];
    } 
    else{
        printf("Stack is empty\n");
        return -1;
    }
}
```

<br>

</p>
</details>

<details><summary><b>📚 Kiểm tra Stack rỗng</b></summary>
<p>

💻
```cpp
int isEmpty(Stack stack){
    return stack.top == -1;
}
```

<br>

</p>
</details>

<details><summary><b>📚 Kiểm tra Stack đầy</b></summary>
<p>

💻
```cpp
int isFull(Stack stack){
    return stack.top == (stack.size - 1);
}
```

<br>

</p>
</details>
 
</p>
</details>

</p>
</details>

<br>

<details><summary><b>14. Queue (Hàng đợi)</b></summary>
<p>

<details><summary><b>14.1. Khái niệm</b></summary>
<p>

- Queue là một cấu trúc dữ liệu tuân theo nguyên tắc "First In, First Out" (FIFO), nghĩa là phần tử đầu tiên được thêm vào hàng đợi sẽ là phần tử đầu tiên được lấy ra. 

![queue](https://drive.google.com/uc?id=1h6uwf3TiU4wA8eOgTSxVohd13ibq8szi)

</p>
</details>

<details><summary><b>14.2. Các thao tác trên Queue</b></summary>
<p>

<details><summary><b>📚 Khởi tạo một Queue</b></summary>
<p>

```cpp
typedef struct{
    int *item;    // mảng chứa các giá trị của phần tử trong queue
    int size;     // kích thước của queue
    int front;    // vị trí phần tử đầu queue
    int rear;     // vị trí phần tử cuối queue
} Queue;

Queue *initialize(int size){
    Queue *queue = (Queue*)malloc(sizeof(Queue));
    queue->item  = (int*)malloc(size * sizeof(int));
    queue->size  = size;
    queue->front = queue->rear = -1;
    return queue;
}
```

<br>

</p>
</details>

<details><summary><b>📚 enqueue</b></summary>
<p>

Thêm phần tử vào cuối hàng đợi.

💻
```cpp
void enqueue(Queue *queue, int data){
    if (isFull(*queue)){
        // nếu queue đầy thì không cho thêm phần tử vào
        printf("Queue overflow\n");
    } 
    else{
        if (isEmpty(*queue)){
            queue->front = queue->rear = 0;
        }
        else{
            queue->rear = (queue->rear + 1) % queue->size;
        }
        queue->item[queue->rear] = data;
    }
}
```

<br>

</p>
</details>

<details><summary><b>📚 dequeue</b></summary>
<p>

Xóa phần tử từ đầu hàng đợi.

💻
```cpp
int dequeue(Queue *queue){
    if (isEmpty(*queue)){
        // nếu queue rỗng thì không cho xóa
        printf("Queue underflow\n");
        return -1;
    }
    else{
        int dequeue_value = queue->item[queue->front];
        if (queue->front == queue->rear){
            queue->front = queue->rear = -1;
        }
        else{
            queue->front = (queue->front + 1) % queue->size;
        }
        return dequeue_value;
    }
}
```

<br>

</p>
</details>

<details><summary><b>📚 front</b></summary>
<p>

Lấy giá trị của phần tử đứng đầu hàng đợi.

💻
```cpp
int front(Queue queue){
    if (isEmpty(queue)){
        printf("Queue is empty\n");
        return -1;
    }
    else{
        return queue.item[queue.front];
    }
}
```

<br>

</p>
</details>

<details><summary><b>📚 Kiểm tra hàng đợi rỗng</b></summary>
<p>

💻
```cpp
int isEmpty(Queue queue){
    return (queue.front == -1);
}
```

<br>

</p>
</details>

<details><summary><b>📚 Kiểm tra hàng đợi đầy</b></summary>
<p>

💻
```cpp
int isFull(Queue queue){
    return (queue.rear + 1) % queue.size == queue.front;
}
```

<br>

</p>
</details>
 
</p>
</details>

</p>
</details>

<br>

<details><summary><b>15. AUTOSAR Classic</b></summary>
<p>

</p>
</details>

<br>

</p>
</details>

# C++
<details><summary>Nhấp vào đây để xem chi tiết</summary>
<p>

<details><summary><b>1. Tham chiếu - Tham trị</b></summary>
<p>

<details><summary><b>1.1. Tham trị (Pass by value)</b></summary>
<p>

- Trong C++, "tham trị" (pass by value) là một cách truyền tham số vào hàm, trong đó một bản sao của biến được truyền cho hàm. Điều này có nghĩa là bất kỳ thay đổi nào được thực hiện trên tham số bên trong hàm sẽ không ảnh hưởng đến giá trị của biến gốc bên ngoài hàm.
- Khi sử dụng tham trị, nó sẽ tốn bộ nhớ trên RAM để cấp phát, sau đó thực hiện copy giá trị.

💻 Ví dụ:
```cpp
#include <iostream>

void test(int a){   	// int a = 10 (0x20: 10)
    printf("dia chi a: %p\n", &a);
}

int main()
{
    int x = 10; 	// 0x34: 10
    printf("dia chi x: %p\n", &x);
    test(x);
}
```

</p>
</details>

<details><summary><b>1.2. Tham chiếu (Pass by reference)</b></summary>
<p>

- Trong C++, tham chiếu (pass by reference) là một cách truyền tham số vào hàm, trong đó thay vì truyền một bản sao của biến, địa chỉ của biến sẽ được truyền vào trực tiếp. Điều này có nghĩa là bất kỳ thay đổi nào thực hiện trên tham số trong hàm sẽ ảnh hưởng trực tiếp đến biến gốc bên ngoài hàm.
- Tham chiếu (&): bỏ qua bước khởi tạo biến, không tốn bộ nhớ RAM, Stack không tăng lên

💻 Ví dụ 1:
```cpp
#include <iostream>

void test(int &a){   
    cout << "Địa chỉ biến a: " << &a << endl;
    a = 50;
    cout << "Giá trị: " << a << endl;
}

int main()
{
    int x = 10; 	// 0x34: 10
    cout << "Địa chỉ biến x: " << &x << endl;
    test(x);
}
```

📝 Lưu ý: tham chiếu có thể thay đổi giá trị.

📝 Nếu nhu cầu của bạn là chỉ đọc thì thêm từ khóa const, vì nó chỉ cho phép đọc giá trị, không cho phép thay đổi.

💻 Ví dụ 2:
```cpp
#include <iostream>

void test(const int &a){   
    cout << "Địa chỉ biến a: " << &a << endl;
    cout << "Giá trị: " << a << endl;
}

int main()
{
    int x = 10; 	// 0x34: 10
    cout << "Địa chỉ biến x: " << &x << endl;
    test(x);
}
```

</p>
</details>

</p>
</details>

<br>

<details><summary><b>2. Cấp phát động với trong C++</b></summary>
<p>

<details><summary><b>2.1. new operator</b></summary>
<p>

- Toán tử new được sử dụng để cấp phát bộ nhớ động cho một biến, một mảng hoặc một đối tượng.
- Khi sử dụng new, bộ nhớ sẽ được cấp phát trên phân vùng Heap (khu vực nhớ dành cho cấp phát động) thay vì trên stack (khu vực nhớ dành cho cấp phát tĩnh).

</p>
</details>

<details><summary><b>2.2. delete operator</b></summary>
<p>

- Toán tử delete dùng để giải phóng bộ nhớ đã được cấp phát bằng new. Điều này giúp tránh lãng phí bộ nhớ và hiện tượng rò rỉ bộ nhớ (memory leak).

💻 **Ví dụ 1: Cấp phát động một biến đơn**
```cpp
    int *ptr = new int(10);
    /****************************************************
     * new int(10)
     *      + Khởi tạo một biến int
     *      + Cấp phát động cho biến int (0x01 - Heap)
     *      + Gán giá trị tại địa chỉ vừa cấp phát là 10
     *      + Trả về địa chỉ của vùng nhớ mới được cấp phát
     * 
     * con trỏ ptr sẽ trỏ đến địa chỉ trên
     ***************************************************/

    cout << "Địa chỉ đối tượng: " << ptr << endl;
    cout << "Giá trị đối tượng: " << *ptr << endl;

    delete ptr;
```

<br>

💻 **Ví dụ 2: Cấp phát động một mảng**
```cpp
#include <iostream>

using namespace std;

int main(int argc, char const *argv[])
{   
    int size;
    cout << "Nhập kích thước của mảng: ";
    cin >> size;

    int *arr = new int[size]; 

    for (int i = 0; i < size; i++){
        arr[i] = i * 2;
    }

    for (int i = 0; i < size; i++){
        cout << arr[i] << " ";
    }
    cout << endl;

    delete[] arr;  // Giải phóng bộ nhớ   

    return 0;
}
```

📝 Mặc dù việc cấp phát động bằng new trong C++ không thể thay đổi kích thước giống như realloc trong C, nhưng cấp phát động vẫn có nhiều điểm khác biệt so với khai báo mảng tĩnh.
📝 Mảng tĩnh: 

- Kích thước phải được xác định khi biên dịch (compile-time), nghĩa là bạn cần biết trước số lượng phần tử và không thể thay đổi trong khi chạy chương trình.
- Bộ nhớ của mảng tĩnh được cấp phát trên stack, và nó sẽ tự động giải phóng khi ra khỏi phạm vi (scope) của nó. Bộ nhớ mảng tĩnh không tồn tại lâu dài trong suốt chương trình mà phụ thuộc vào phạm vi khai báo.
- Kích thước mảng trên stack bị giới hạn bởi kích thước của stack (thường là vài MB), nên nếu bạn cần một mảng lớn, mảng tĩnh có thể không khả thi và dễ gây lỗi stack overflow.

📝 Cấp phát động với new: 

- Bạn có thể xác định kích thước mảng tại thời điểm chạy, giúp linh hoạt hơn nếu kích thước mảng phụ thuộc vào các giá trị mà bạn chỉ biết trong quá trình chạy chương trình.
- Bộ nhớ được cấp phát trên heap, có thể tồn tại cho đến khi bạn giải phóng thủ công bằng delete[]. Điều này cho phép bộ nhớ tồn tại lâu dài hơn, ngay cả khi phạm vi khai báo của con trỏ đã kết thúc.
- Vì bộ nhớ cấp phát động nằm trên heap, bạn có thể tạo ra các mảng rất lớn nếu bộ nhớ hệ thống còn đủ, mà không bị giới hạn bởi stack.

<br>

💻 **Ví dụ 3: Cấp phát động cho một biến của struct**
```cpp
#include <iostream>

using namespace std;

typedef struct{
    int x;
    int y;
} Point;

int main(int argc, char const *argv[])
{   
    Point *p1 = new Point;
    p1->x = 10;
    p1->y = 20;
    cout << "Point 1: (" << p1->x << ", " << p1->y << ")" << endl << endl;

    Point *p2 = new Point{5, 15};
    cout << "Point 2: (" << p2->x << ", " << p2->y << ")" << endl << endl;

    Point *points = new Point[3];    // Cấp phát động cho một mảng gồm 3 biến Point
    points[0] = {1,2};
    points[1] = {3,4};
    points[2] = {5,6};
    for (int i = 0; i < 3; i++) {
        cout << "Point " << i + 1 << ": (" << points[i].x << ", " << points[i].y << ")" << endl;
    }

    delete p1;
    delete p2;
    delete[] points;

    return 0;
}
```

<br>

💻 **Ví dụ 4: Cấp phát động một mảng**
```cpp
#include <iostream>

using namespace std;

class SinhVien{
    private:
        string name;
        int id;
        int age;
    
    public:
        SinhVien(string _name, int _age): name(_name), age(_age){
            static int _id = 1;
            id = _id;
            _id++;
        }

        void display(){
            cout << "Name: " << name << ", ID: " << id << ", Age: " << age << endl;
        }
};

int main(int argc, char const *argv[])
{   
    SinhVien *p1 = new SinhVien("Trung", 25);
    p1->display();

    SinhVien *p2 = new SinhVien("Tuan", 22);
    p2->display();

    return 0;
}
```

<br>

</p>
</details>

</p>
</details>

<br>

<details><summary><b>3. Class</b></summary>
<p>

<details><summary><b>3.1. Khái niệm</b></summary>
<p>

Class là một mô tả trừu tượng của nhóm các đối tượng (object) có cùng bản chất.

Một class bao gồm:

- Các thành phần dữ liệu (biến hay **property**).
- Các phương thức (hàm thành phần hay **method**).

Khai báo và sử dụng Class:
```cpp
class <class_name>{
   <access_modifier>:
      <data_type> property1;
      <data_type> property2;
      ...
      <return_type> <method_name_1>(argument1, argument2, ....);
      <return_type> <method_name_2>(argument1, argument2, ....);
};

<return_type> <class_name>::<method_name1>(argument1, argument2, ....){
    ...
}

<return_type> <class_name>::<method_name1>(argument1, argument2, ....){
    ...
}
```

<br>

💻 **Ví dụ 1:**
```cpp
class Person{
    public:
        string firstName; // property
        string lastName;  // property
        int age;	  // property

        void fullName(){  // method
            cout << firstName << " " << lastName;
        }
}
```

<br>

💻 **Ví dụ 2:**
```cpp
class SinhVien{
    public:
        int ID;         // property
        string name;    // property
        string lop;     // property
        void display(); // method
};

void SinhVien::display(){
    cout << "MSSV: " << ID << endl;
    cout << "TEN: " << name << endl;
    cout << "LOP: " << lop << endl;
}
```

<br>

</p>
</details>

<details><summary><b>3.2. Phạm vi truy cập</b></summary>
<p>

<details><summary><b>📚 public</b></summary>
<p>

- Truy cập từ bên trong class thông qua các method.
- Truy cập từ bên ngoài class thông qua các object.
- Các lớp kế thừa có thể truy cập trực tiếp đến các thuộc tính của lớp cha.

💻**Ví dụ 1:**
```cpp
#include <iostream>
#include <string>

using namespace std;

class SinhVien{
    public:
        int ID;         // property
        string name;    // property
        string lop;     // property
        void display(){ // method
            cout << "MSSV: " << ID << endl;
            cout << "TEN: " << name << endl;
            cout << "LOP: " << lop << endl;
        }
};

int main(int argc, char const *argv[]){
    SinhVien sv; // sv được gọi là object
    sv.ID = 2010117;
    sv.name = "Anh";
    sv.lop = "DD20TD1";
    sv.display();  
    return 0;
}
```

<br>

💻 **Ví dụ 2:**
```cpp
#include <iostream>

using namespace std;

class HinhChuNhat {
    public:
        double chieuDai;    // property
        double chieuRong;   // property

    // Hàm tính diện tích
    double tinhDienTich(){  // method
        return chieuDai * chieuRong;
    }

    void display(); // method
};

void HinhChuNhat::display(){
   std::cout << " Hello " << "\n";
}

int main(){
    HinhChuNhat hinh1;
    hinh1.chieuDai = 10.0;
    hinh1.chieuRong = 5.0;
    hinh1.display();
    std::cout << "Dien tich: " << hinh1.tinhDienTich() << '\n';
    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>📚 private</b></summary>
<p>

- Các thuộc tính mà bạn không muốn người khác có thể truy cập đến sẽ được khai báo ở **private**.
- Những property hay method nằm ở **private** thì chỉ có giá trị tại class và các **object** không thể truy cập đến.
- Muốn truy cập các thuộc tính **private** thì chỉ có thể truy cập gián tiếp thông qua các **method** hay **constructor** nằm ở phạm vi **public**.

💻 **Ví dụ:**
```cpp
#include <iostream>
#include <string>

using namespace std;

class SinhVien{
    private:
        string ten;
        string chuyenNganh;
        int id;
        double GPA;

    public:
        SinhVien(string ten, string chuyenNganh);

        string getName(){           // getter method
            return ten;
        }

        string getChuyenNganh(){
            return chuyenNganh;
        }

        void setGPA(double gpa){    // setter method
            GPA = gpa;
        }
        double getGPA(){            
            return GPA;
        }

        int getID(){
            return id;
        }
};

SinhVien::SinhVien(string ten, string chuyenNganh){
    static int id = 1;
    SinhVien::id = id;
    id++;

    SinhVien::ten = ten;
    SinhVien::chuyenNganh = chuyenNganh;
}

int main(int argc, char const *argv[])
{
    SinhVien sv1("Trung", "DTVT");

    // sv1.ten = "Tuan";        // wrong
    // sv1.id = 10;             // wrong
    // sv1.chuyenNganh = "TDH"; // wrong

    cout << "ID: " << sv1.getID() << endl;
    cout << "Name: " << sv1.getName() << endl;
    cout << "Chuyen nganh: " << sv1.getChuyenNganh() << endl;
    return 0;
}
```

📝 Khi đã setup một số thông tin và không muốn users có thể truy cập vào và thay đổi được những thông tin thì ta nên khai báo nó ở phạm vi ``` private ```.

📝 Những property nào đã được khai báo ở phạm vi ``` private ``` thì nó sẽ chỉ có giá trị nội bộ trong class và chỉ có những metod nằm trong class mới có thể truy cập để thay đổi được.

📝 Việc ẩn đi những property bằng cách khai báo trong ``` private ``` chính là **tính đóng gói** trong hướng đối tượng.

<br>

</p>
</details>

<details><summary><b>📚 protected</b></summary>
<p>

- Các method và property nằm ở protected chỉ có thể truy cập thông qua các class **kế thừa**.
- Các method và property nằm ở protected không thể truy cập từ các object bên ngoài.
- Các method và property nằm ở protected có thể truy cập trực tiếp từ các method bên trong class.

💻**Ví dụ 1:**
```cpp
#include <iostream>

using namespace std;

class Base{
    protected:
        int protectedVar;

    public:
        Base() : protectedVar(10){}
};

class Derived : public Base{
    public: 
        void showProtectedVar(){
            cout << "Protected variable: " << protectedVar << endl;
        }
};

int main(int argc, char const *argv[])
{
    Derived obj;
    // obj.protectedVar = 10; // wrong
    obj.showProtectedVar();   // right
    return 0;
}
```

<br>

💻 **Ví dụ 2:**
```cpp
#include <iostream>
#include <string>

using namespace std;

class SinhVien{
    protected:
        string ten;
        string chuyenNganh;
        int id;
        double GPA;

    public:
        SinhVien(string ten, string chuyenNganh);

        string getName(){           // getter method
            return ten;
        }

        string getChuyenNganh(){
            return chuyenNganh;
        }

        void setGPA(double gpa){    // setter method
            GPA = gpa;
        }
        double getGPA(){            
            return GPA;
        }

        int getID(){
            return id;
        }
};

SinhVien::SinhVien(string ten, string chuyenNganh){
    static int id = 1;
    SinhVien::id = id;
    id++;

    SinhVien::ten = ten;
    SinhVien::chuyenNganh = chuyenNganh;
}

int main(int argc, char const *argv[])
{
    SinhVien sv1("Trung", "DTVT");

    // sv1.ten = "Tuan";        // wrong
    // sv1.id = 10;             // wrong
    // sv1.chuyenNganh = "TDH"; // wrong

    cout << "ID: " << sv1.getID() << endl;
    cout << "Name: " << sv1.getName() << endl;
    cout << "Chuyen nganh: " << sv1.getChuyenNganh() << endl;
    return 0;
}
```

<br>

</p>
</details>

</p>
</details>

<details><summary><b>3.3. Phương thức khởi tạo (Constructor)</b></summary>
<p>

Constructor là một method có tên trùng với tên của class.

Constructor có thể được khai báo ở cả ba phạm vi public, protected, private.

Constructor thường được sử dụng để khởi tạo giá trị ban đầu cho các property trong class.

**Constructor trong phạm vi public**: 

- Constructor sẽ được tự động gọi khi khởi tạo object.
- Constructor có thể có tham số truyền vào hoặc không.

💻 **Constructor không có tham số truyền vào**
```cpp
#include <iostream>

using namespace std;

class HinhChuNhat{
    public:
        double chieuDai;
        double chieuRong;

        // HinhChuNhat(){				// cách 1
        //     chieuDai  = 10;
        //     chieuRong = 9;
        // }

        HinhChuNhat(): chieuDai(10), chieuRong(9){}	// cách 2

        // Hàm tính diện tích
        double tinhDienTich(){
            return chieuDai * chieuRong;
        }
};
int main(){
    HinhChuNhat hinh1;
    cout << "Chieu dai: " << hinh1.chieuDai << '\n';
    cout << "Chieu rong: " << hinh1.chieuRong << '\n';
    cout << "Dien tich: " << hinh1.tinhDienTich() << '\n';
    return 0;
}
```

<br>

💻 **Constructor có tham số truyền vào**
```cpp
#include <iostream>

using namespace std;

class HinhChuNhat{
    public:
        double chieuDai;
        double chieuRong;

        HinhChuNhat(int dai = 3, int rong = 5);

        double tinhDienTich(){
            return chieuDai * chieuRong;
        }
};

HinhChuNhat::HinhChuNhat(int dai, int rong){
    chieuDai = dai;
    chieuRong = rong;
}

int main(){
    HinhChuNhat hinh1; 		// lấy giá trị mặc định (3,5)
    cout << "Chieu dai: " << hinh1.chieuDai << '\n';
    cout << "Chieu rong: " << hinh1.chieuRong << '\n';
    cout << "Dien tich: " << hinh1.tinhDienTich() << '\n';

    HinhChuNhat hinh2(10,20); 	// lấy giá trị mới
    cout << "Chieu dai: " << hinh2.chieuDai << '\n';
    cout << "Chieu rong: " << hinh2.chieuRong << '\n';
    cout << "Dien tich: " << hinh2.tinhDienTich() << '\n';

    int a,b;
    cout << "Nhap chieu dai: ";  cin >> a;
    cout << "Nhap chieu rong: "; cin >> b;
    HinhChuNhat hinh3(a,b); 	// lấy giá trị mới nhập từ bàn phím
    cout << "Chieu dai: " << hinh3.chieuDai << '\n';
    cout << "Chieu rong: " << hinh3.chieuRong << '\n';
    cout << "Dien tich: " << hinh3.tinhDienTich() << '\n';
    return 0;
}
```
**Kết quả**:
```cpp
Chieu dai: 3
Chieu rong: 5
Dien tich: 15

Chieu dai: 10
Chieu rong: 20
Dien tich: 200

Nhap chieu dai: 7
Nhap chieu rong: 8
Chieu dai: 7
Chieu rong: 8
Dien tich: 56
```

📝 Khi constructor có tham số truyền vào và gán giá trị cụ thể:

📝 Nếu object được khởi tạo không có tham số truyền vào thì mặc định lấy 2 giá trị đã khởi tạo phía trên.

📝 Nếu object được khởi tạo và truyền vào giá trị mới thì nó sẽ ghi đè lên giá trị cũ và các lệnh thực thi phía sau sẽ lấy giá trị mới.


- **Constructor trong phạm vi protected**: object không thể được khởi tạo trực tiếp từ bên ngoài class, mà chỉ có thể được khởi tạo từ các class kế thừa hoặc các method bên trong class đó.

💻 **Ví dụ:**
```cpp
#include <iostream>

using namespace std;

class Base {
    protected:
        Base(){
            cout << "Protected constructor called!" << endl;
        }
};

class Derived : public Base {
    public:
        Derived() : Base(){  // Có thể gọi constructor protected của class Base
            cout << "Derived constructor called!" << endl;
        }
};

int main() {
    // Base obj;  // Lỗi: Không thể khởi tạo đối tượng Base từ bên ngoài
    Derived obj;  // Hợp lệ: Có thể khởi tạo Derived, class con của Base
    return 0;
}
```

- **Constructor trong phạm vi private**: không ai có thể khởi tạo đối tượng của class từ bên ngoài, kể cả class con. Thường được sử dụng trong các singleton pattern hoặc các trường hợp mà muốn kiểm soát chặt chẽ việc tạo đối tượng.

💻 **Ví dụ:**
```cpp
class MyClass {
private:
    MyClass() {
        cout << "Private constructor called!" << endl;
    }

public:
    static MyClass createInstance() {
        return MyClass();  // Có thể khởi tạo từ bên trong class với method static
    }
};

int main() {
    // MyClass obj;  // Lỗi: Không thể khởi tạo đối tượng từ bên ngoài
    MyClass obj = MyClass::createInstance();  // Hợp lệ: Sử dụng phương thức static để tạo đối tượng
    return 0;
}
```

</p>
</details>

<details><summary><b>3.4. Phương thức hủy (Destructor)</b></summary>
<p>

- Tương tự với Constructor, Destructor cũng là một method và có tên trùng với tên class nhưng có thể ký tự **~** ở phía trước tên.
- Destructor khi khai báo trong phạm vi public sẽ được tự động gọi khi object được giải phóng.
- Trong class, object bản chất cũng là 1 biến cục bộ nên nó sẽ được lưu trong phân vùng Stack và khi kết thúc hàm thì cũng bị thu hồi nhưng đối với **destructor**, trước khi thu hồi sẽ tự động khởi chạy.

💻 **Ví dụ:**
```cpp
#include <iostream>
#include <string>

using namespace std;

class HinhChuNhat {
    public:
    string name;
    double chieuDai;
    double chieuRong;

    HinhChuNhat(string ten){ 
        HinhChuNhat::name = ten;
        cout << "Khoi tao object: " << name << endl;
    }

    ~HinhChuNhat() {
        std::cout << "Destructor: " << name << '\n';
    }


    // Hàm tính diện tích
    double tinhDienTich() {
        return chieuDai * chieuRong;
    }
};

void test(){
    HinhChuNhat hinh1("Hinh1");
    HinhChuNhat hinh2("Hinh2");
}

int main(int argc, char const *argv[])
{
    test();
    return 0;
}
```
**Kết quả:**
```cpp
Khoi tao object: Hinh1
Khoi tao object: Hinh2
Destructor: Hinh2
Destructor: Hinh1
```

📝 ``` hinh1("Hinh1") ```, ``` hinh2("Hinh2") ``` bản chất đều 2 biến cục bộ và lưu trong Stack

📝 object ``` hinh1("Hinh1") ``` được khởi tạo trước nên được đưa vào Stack trước, còn object ``` hinh2("Hinh2") ``` khởi tạo sau nên đưa vào Stack sau cùng nhưng khi lấy ra thì object ``` hinh2("Hinh2") ``` lại được lấy ra trước.

<br> 

</p>
</details>

<details><summary><b>3.5. Static trong Class</b></summary>
<p>

<details><summary><b>📚 Static property</b></summary>
<p>

Khi một property trong class được khai báo với từ khóa static:

- Tất cả các object sẽ dùng chung địa chỉ của property.
- Địa chỉ của property phải được khởi tạo trước khi khởi tạo object.

💻 **Ví dụ:**
```cpp
#include <iostream>
#include <string>

using namespace std;

class HinhChuNhat { 

    public:
        double chieuDai;
        double chieuRong;
        static int var;
};

int HinhChuNhat::var; // 0xa1

int main()
{
    HinhChuNhat hinh1; // 0x01
    HinhChuNhat hinh2; // 0x08

    cout << "address of object hinh 1: " << &hinh1 << '\n';
    cout << "address of chieu dai 1: " << &hinh1.chieuDai << '\n'; 
    cout << "address of chieu rong 1: " << &hinh1.chieuRong << '\n';
    cout << "address of chieu dai 2: " << &hinh2.chieuDai << '\n'; 
    cout << "address of chieu rong 12: " << &hinh2.chieuRong << '\n';

    cout << "address of var: " << &hinh1.var << '\n'; 
    cout << "address of var: " << &hinh2.var << '\n'; 

    return 0;
}
```
**Kết quả**:
```cpp
address of object hinh 1: 0x7609ffb80
address of chieu dai 1: 0x7609ffb80
address of chieu rong 1: 0x7609ffb88
address of chieu dai 2: 0x7609ffb70
address of chieu rong 12: 0x7609ffb78
address of var: 0x7ff70cbc7030
address of var: 0x7ff70cbc7030
```

![image](https://github.com/user-attachments/assets/1ed9e3c7-c0b0-483a-8df2-cc0e9283a1fe)


</p>
</details>

<details><summary><b>📚 Static method</b></summary>
<p>

- Static method là một method thuộc về class thay vì object của class.
- Static method có thể gọi ra mà không cần tạo đối tượng của class.
- Static method chỉ có thể truy cập các thành viên static khác của class (cả thuộc tính và phương thức).
- Static method sẽ không có con trỏ ``` this ``` vì không được gọi từ đối tượng.

💻 **Ví dụ:**
```cpp
#include <iostream>

using namespace std;

class MyClass {
public:
    // Static method
    static void showMessage() {
        cout << "This is a static method." << endl;
    }

    // Static variable
    static int count;

    // Static method to manipulate static variable
    static void increaseCount() {
        count++;
    }
};

// Khởi tạo biến static
int MyClass::count = 0;

int main()
{
    // Gọi static method mà không cần tạo đối tượng
    MyClass::showMessage();

    // Gọi static method để thao tác với biến static
    MyClass::increaseCount();
    cout << "Count: " << MyClass::count << endl;

    return 0;
}
```

<br>

</p>
</details>

</p>
</details>

</p>
</details>

<br>

<details><summary><b>4. Hướng đối tượng (OOP)</b></summary>
<p>

<details><summary><b>4.1. Tính đóng gói (Encapsulation)</b></summary>
<p>

- Là việc che giấu thông tin bên trong đối tượng, chỉ cung cấp những gì cần thiết ra bên ngoài thông qua các phương thức (method). Điều này giúp bảo vệ dữ liệu và giảm rủi ro không mong muốn.
- Những thông tin được lưu ở các thuộc tính (property) sẽ bị ẩn đi bằng cách khai báo ở quyền truy cập ``` private ```.
- Trong trường hợp muốn đọc hoặc ghi các property này thì chỉ có thể truy cập gián tiếp thông qua các method ở quyền truy cập ``` public ```.

<br>

💻 **Ví dụ:**
```cpp
#include <iostream>
#include <string>

using namespace std;

class Student{
    private:
        string Name;
        double GPA;
        int StudentID;

    public:
        Student(string name);

        string getName(){               // getter   // đọc giá trị của property
            return Student::Name;
        }

        void setGPA(double gpa){        // setter   // ghi giá trị của property
            Student::GPA = gpa;
        }
        double getGPA(){                // getter
            return Student::GPA;
        }

        int getID(){                    // getter
            return Student::StudentID;
        }
};

Student::Student(string name){
    Student::Name = name;
    static int id = 1000;
    Student::StudentID = id;
    ++id;
}

int main(){
    Student student1("Trung");
    Student student2("Thai");
    Student student3("Thao");

    cout << "ID: " << student1.getID() << endl;
    cout << "Name: " << student1.getName() << endl;

    cout << "ID: " << student2.getID() << endl;
    cout << "Name: " << student2.getName() << endl;

    cout << "ID: " << student3.getID() << endl;
    cout << "Name: " << student3.getName() << endl;
    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>4.2. Tính kế thừa (Inheritance)</b></summary>
<p>

- Kế thừa cho phép một class mới có thể sử dụng lại các thuộc tính và phương thức của một class đã có, và có thể mở rộng thêm tính năng. (tạm gọi là class con kế thừa từ class cha).
- Để kế thừa từ class khác, ta dùng ký tự ``` : ```.
- Tất cả thuộc tính và phương thức có quyền truy cập là ``` public ``` và ``` protected ``` ở class cha sẽ được class con kế thừa.
- Có 3 kiểu kế thừa là public, private và protected.

<details><summary><b>📚 Kế thừa public</b></summary>
<p>

- Các member **public** của **class cha** vẫn sẽ là **public** trong **class con**.
- Các member **protected** của **class cha** vẫn sẽ là **protected** trong **class con**.
- Các member **private** của class cha **không thể truy cập trực tiếp từ class con** nhưng có thể được truy cập gián tiếp qua các phương thức public hoặc protected của class cha

<br>

💻 **Ví dụ 1:**
```cpp
#include <iostream>
#include <string>

using namespace std;

class Person{
    protected:
        // các member nằm trong protected thì object sẽ không truy cập đến được
        // nhưng các class con kế thừa từ class cha thì có thể truy cập được
        string Name;
        int Age;
        string Home_Address;

    public:
        string getName(){           // getter   // đọc giá trị của property
            return Person::Name;
        }
        void setName(string name){  // setter   // ghi giá trị của property
            Person::Name = name;
        }

        int getAge(){               // getter
            return Person::Age;
        }
        void setAge(int age){       // setter
            Person::Age = age;
        }

        string getAddress(){                // getter
            return Person::Home_Address;
        }
        void setAddress(string address){    // setter
            Person::Home_Address = address;
        }

        void displayInfo(){
            cout << "Name: " << Person::Name << endl;
            cout << "Age: " << Person::Age << endl;
            cout << "Address: " << Person::Home_Address << endl;
        }
};

class Student : public Person{
    private:
        string School_Name;
        double GPA;
        int StudentID;

    public:
        Student(){
            static int id = 1000;
            Student::StudentID = id;
            id++;
        }

        string getSchoolName(){
            return Student::School_Name;
        }
        void setSchoolName(string school_name){
            Student::School_Name = school_name;
        }

        double getGPA(){
            return Student::GPA;
        }
        void setGPA(double gpa){
            Student::GPA = gpa;
        }

        int getID(){
            return Student::StudentID;
        }

        void displayInfo(){ // override
            cout << "Name: " << Student::Name << endl;
            cout << "Age: " << Student::Age << endl;
            cout << "Address: " << Student::Home_Address << endl;
            cout << "School name: " << Student::School_Name << endl;
            cout << "GPA: " << Student::GPA << endl;
        }
};

int main(){
    Person person1;
    person1.setName("Trung");
    person1.setAge(20);
    person1.setAddress("HCM");
    person1.displayInfo();

    cout << "-----------------------" << endl;
    
    Student student1;
    student1.setName("Trungg");
    student1.setAge(24);
    student1.setAddress("HCMM");
    student1.setGPA(8.1);
    student1.setSchoolName("DinhTienHoang");
    student1.displayInfo();
    return 0;
}
```

<br>

💻 **Ví dụ 2:**
```cpp
#include <iostream>
#include <string>

using namespace std;

class DoiTuong{ 
    // private:
    //     int id;   
    //     string name;

    protected:
        int id;   
        string name;      

    public:
        DoiTuong(string newName){
            static int _id = 1;
            id = _id;
            _id++;

            // check chuỗi nhập vào, tránh có ký tự đặc biệt, số, space,...
            name = newName;
        }

        // getter
        int getID(){
            return id;
        }

        string getName(){
            return name;
        }

        void display(){
            cout << "Ten: " << getName() << endl;
            cout << "ID: " << getID() << endl;
        }
};

class SinhVien : public DoiTuong{
    private:
        string chuyenNganh;

    public:
        SinhVien(string name, string chuyenNganh): DoiTuong(name){
            SinhVien::chuyenNganh = chuyenNganh;
        }

        string getChuyenNganh(){
            return chuyenNganh;
        }

        void display(){
            cout << "Ten: " << SinhVien::getName() << endl;
            cout << "ID: " << SinhVien::getID() << endl;
            cout << "Chuyen nganh: " << SinhVien::getChuyenNganh() << endl;
        }
};

class HocSinh : public DoiTuong{
    public:
        string lop;

        HocSinh(string name, string lop): DoiTuong(name){
            HocSinh::lop = lop;
        }

        string getLop(){
            return lop;
        }

        void display(){
            cout << "Ten: " << getName() << endl;
            cout << "ID: " << getID() << endl;
            cout << "Lop: " << getLop() << endl;
        }
};

class GiaoVien : public DoiTuong{
    public:
        string chuyenMon;

        GiaoVien(string name, string chuyenMon): DoiTuong(name){
            GiaoVien::chuyenMon = chuyenMon;
        }

        string getChuyenMon(){
            return chuyenMon;
        }

        void display(){
            cout << "Ten: " << getName() << endl;
            cout << "ID: " << getID() << endl;
            cout << "Chuyen mon: " << getChuyenMon() << endl;
        }
};

int main(int argc, char const *argv[])
{
    DoiTuong dt1("Hoang");
    dt1.display();
    cout << endl;

    SinhVien sv1("Tuan","DTVT");
    sv1.display();
    cout << endl;

    HocSinh hs1("Trung", "12A1");
    hs1.display();
    cout << endl;

    GiaoVien gv1("Bao", "Toan");
    gv1.display();
    cout << endl;

    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>📚 Kế thừa protected</b></summary>
<p>

- Các member **public, protected** của class cha sẽ là **protected** trong class con.
- Các member private của class cha không thể truy cập trực tiếp từ class con nhưng có thể được truy cập gián tiếp qua các phương thức public hoặc protected của class cha.

<br>

💻 **Ví dụ 1:**
```cpp
#include <iostream>

using namespace std;

class Base{
    protected:
        int protectedVar;
    public:
        Base(): protectedVar(10){}
        void showProtectedVar(){
            cout << "Protected variable: " << protectedVar << endl;
        }
};

class Derived : protected Base{
    public:
        void accessBaseMembers(){
            // Có thể truy cập protectedVar vì nó được kế thừa dưới dạng protected
            cout << "Accessing protectedVar from Base: " << protectedVar << endl;
        }
};

int main(){
    Derived obj;
    obj.accessBaseMembers();

    // Không thể truy cập trực tiếp thành viên protected từ đối tượng class con
    // obj.protectedVar; // Lỗi: không thể truy cập trực tiếp
}
```

<br>

💻 **Ví dụ 2:**
```cpp
#include <iostream>
#include <string>

using namespace std;

class DoiTuong{ 
    // private:
    //     int id;   
    //     string name;

    protected:
        int id;   
        string name;      

    public:
        DoiTuong(string newName){
            static int _id = 1;
            id = _id;
            _id++;

            // check chuỗi nhập vào, tránh có ký tự đặc biệt, số, space,...
            name = newName;
        }

        // getter
        int getID(){
            return id;
        }

        string getName(){
            return name;
        }

        void display(){
            cout << "Ten: " << getName() << endl;
            cout << "ID: " << getID() << endl;
        }
};

class SinhVien : protected DoiTuong{
    private:
        string chuyenNganh;

    public:
        SinhVien(string name, string chuyenNganh): DoiTuong(name){
            SinhVien::chuyenNganh = chuyenNganh;
        }

        string getChuyenNganh(){
            return chuyenNganh;
        }

        void display(){
            cout << "Ten: " << SinhVien::getName() << endl;
            cout << "ID: " << SinhVien::getID() << endl;
            cout << "Chuyen nganh: " << SinhVien::getChuyenNganh() << endl;
        }
};

int main(int argc, char const *argv[])
{
    DoiTuong dt1("Hoang");
    dt1.display();

    cout << endl;

    SinhVien sv1("Tuan","DTVT");
    sv1.display();
    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>📚 Kế thừa private</b></summary>
<p>

- Các member **public, protected** của class cha sẽ trở thành **private** trong class con.
- Các member private của class cha không thể truy cập trực tiếp từ class con nhưng có thể được truy cập gián tiếp qua các phương thức public hoặc protected của class cha.

<br>

💻 **Ví dụ**
```cpp
#include <iostream>

using namespace std;

class Base{
    protected:
        int protectedVar;
    public:
        int publicVar;
        Base(): protectedVar(10), publicVar(20){}
};

class Derived : private Base{
    public:
        void accessBaseMembers(){
            // Có thể truy cập protectedVar và publicVar bên trong class con
            cout << "protectedVar: " << protectedVar << endl;
            cout << "publicVar: " << publicVar << endl;
        }
};

int main(){
    Derived obj;
    obj.accessBaseMembers();

    // Không thể truy cập trực tiếp thành viên của Base qua đối tượng Derived
    // obj.publicVar; // Lỗi: không thể truy cập vì publicVar đã trở thành private trong Derived
}
```

<br>

</p>
</details>

<details><summary><b>📚 Kế thừa ảo</b></summary>
<p>

- Khi một lớp con kế thừa từ hai lớp cha, và hai lớp cha này đều kế thừa từ một lớp chung (gốc), thì lớp chung đó có thể bị kế thừa nhiều lần, dẫn đến việc dữ liệu từ lớp chung bị sao chép, gây ra các xung đột dữ liệu hoặc không nhất quán. Đây được gọi là vấn đề kim cương (Diamond problem).

💻 **Ví dụ:**
```cpp
#include <iostream>

using namespace std;

class A {
    public:
        A(){ cout << "Constructor A\n"; }

        void hienThiA(){ cout << "Day la lop A\n"; }
};

class B : public A{
    public:
        B(){ cout << "Constructor B\n"; }

        void hienThiB(){ cout << "Day la lop B\n"; }
};

class C : public A {
    public:
        C(){ cout << "Constructor C\n"; }

        void hienThiC(){ cout << "Day la lop C\n"; }
};

class D : public B, public C{
    public:
        D(){ cout << "Constructor D\n"; }

        void hienThiD(){ cout << "Day la lop D\n"; }
};

int main()
{
    D d;
    d.hienThiA();
    return 0;
}
```
**Kết quả**:
``` error: request for member 'hienThiA' is ambiguous ```

📝 lớp D sẽ có hai phiên bản của class A, một từ B và một từ C, dẫn đến vấn đề đa kế thừa "diamond problem". 

📝 Muốn gọi method ``` hienThiA ``` thì ta phải chỉ định rõ ràng là gọi từ class nào. Ví dụ: ``` d.B::hienThiA() ``` hoặc ``` d.C::hienThiA() ```

📝 Không thể gọi trực tiếp method ``` hienThiA ``` vì lúc này compiler không biết nên gọi từ class nào.

<br>

Để khắc phục vấn đề trên thì ngoài việc chỉ định rõ ràng là gọi từ class nào, ta có thể thêm vào từ khóa ``` virtual ``` khi các class cha (class cơ sở) kế thừa từ class chung. Điều này giúp tạo ra một bản sao duy nhất của các thành phần được kế thừa từ class chung.

💻 **Ví dụ:**
```cpp
#include <iostream>

using namespace std;

class A {
    public:
        A(){ cout << "Constructor A\n"; }

        void hienThiA(){ cout << "Day la lop A\n"; }
};

class B : virtual public A{
    public:
        B(){ cout << "Constructor B\n"; }

        void hienThiB(){ cout << "Day la lop B\n"; }
};

class C : virtual public A {
    public:
        C(){ cout << "Constructor C\n"; }

        void hienThiC(){ cout << "Day la lop C\n"; }
};

class D : public B, public C{
    public:
        D(){ cout << "Constructor D\n"; }

        void hienThiD(){ cout << "Day la lop D\n"; }
};

int main() {
    D d;

    d.hienThiA();

    // Gọi phương thức từ lớp A qua B và C
    // d.B::hienThiA(); // Gọi hàm hienThiA từ lớp A thông qua B
    // d.C::hienThiA(); // Gọi hàm hienThiA từ lớp A thông qua C

    // d.hienThiB();
    // d.hienThiC();
    // d.hienThiD();

    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>📚 Đa kế thừa</b></summary>
<p>
	
- Đa kế thừa trong C++ cho phép một class kế thừa từ nhiều class khác.
- Đa kế thừa thường dùng để kết hợp các chức năng từ nhiều class.

💻 **Ví dụ:**
```cpp
#include <iostream>

using namespace std;

class Sensor{
    public:
        void initialize(){
            cout << "Initializing sensor" << endl;
            // code khởi tạo cảm biến
        }

        int readData(){
            cout << "Reading sensor data" << endl;
            // code đọc dữ liệu cảm biến
            return 30;
        }
};

class Communication{
    public:
        void setupCommunication() {
            cout << "Setting up communication protocol" << endl;
            // code thiết lập giao thức truyền thông (SPI, I2C, UART,...)
        }

        void sendData(int data) {
            cout << "Sending data: " << data << endl;
            // code gửi dữ liệu qua các giao thức
        }
};

class Control : public Sensor, public Communication{
    public:
        Control(){
            setupCommunication();

            initialize();

            int data = readData();

            sendData(data);
        }
};

int main(int argc, char const *argv[])
{
    Control sensorControl;
    return 0;
}
```

<br>

</p>
</details>

</p>
</details>

<details><summary><b>4.3. Tính đa hình (Polymorphism)</b></summary>
<p>

- Tính đa hình có nghĩa là "nhiều dạng" và nó xảy ra khi chúng ta có nhiều class có liên quan với nhau thông qua tính kế thừa.

Tính đa hình có thể được chia thành hai loại chính:
- Đa hình tại thời điểm biên dịch (Compile-time Polymorphism).
- Đa hình tại thời điểm chạy (Run-time Polymorphism).

<details><summary><b>📚 Đa hình compile-time</b></summary>
<p>

<details><summary><b>📚📚 Nạp chồng hàm (Function Overloading)</b></summary>
<p>

Nạp chồng hàm (Function Overloading) xảy ra khi các phương thức trong cùng một lớp có cùng tên nhưng khác nhau về số lượng hoặc kiểu tham số. Compiler sẽ xác định method nào sẽ được gọi dựa trên tham số truyền vào.

💻 **Ví dụ:**
```cpp
#include <iostream>
#include <string>

using namespace std;

// 1 method có thể có nhiều input parameter khác nhau
class TinhToan{
    private:
        int a;
        int b;
    public:
        int tong(int a, int b){
            return a+b;
        }
        double tong(int a, int b, int c, double d){
            return (double)a+b+c+d;
        }
        double tong(int a, double b){
            return (double)a+b;
        }
};

int main(int argc, char const *argv[]){

    TinhToan th, th1, th2;
    cout << th.tong(2, 5) << endl;
    cout << th1.tong(2, 5, 7, 6.7) << endl;
    cout << th2.tong(2, 3.5) << endl;

    return 0;
}
```
📝 Viết method tính tổng, có thể là tổng của 2,3,4 hoặc nhiều hơn, hoặc là các tham số truyền vào khác nhau. Khi khởi tạo object và gọi method thì lúc đó nó sẽ căn cứ vào tham số truyền vào để biết là nó sẽ lấy method nào.

📝 Quá trình trên đểu xảy ra ở compiler, nghĩa là khi ta đưa các tham số đầu vào thì nó căn cứ vào đó và bắt đầu generate ra code.

📝 Trong C++ thì việc nhiều hàm có cùng tên nhưng khác nhau tham số đầu vào và kiểu dữ liệu trả về vẫn có thể nằm toàn cục chứ không nhất thiết phải nằm trong class.

<br>

</p>
</details>

<details><summary><b>📚📚 Nạp chồng toán tử (Operator Overloading)</b></summary>
<p>

Nạp chồng toán tử (Operator Overloading) trong C++ là một tính năng cho phép bạn định nghĩa lại các toán tử để chúng hoạt động theo cách mà bạn mong muốn đối với các kiểu dữ liệu do người dùng định nghĩa (như class hoặc struct).

Khi bạn muốn nạp chồng một toán tử, bạn cần định nghĩa một hàm có từ khóa ``` operator ``` theo sau là ``` ký hiệu toán tử ``` mà bạn muốn nạp chồng.

Cú pháp: 

```cpp
<return_type> operator symbol (parameters) {
	// logic của toán tử
}
```

Trong đó:

- **return_type**: Kiểu dữ liệu trả về của hàm (method).
- **operator symbol**: Toán tử mà bạn muốn nạp chồng.
- **parameters**: Các tham số truyền vào (có thể là một hoặc nhiều đối số, phụ thuộc vào toán tử).

Các toán tử có thể overload được:

```cpp
+	–	*	/	%	^	&	|
~	!	=	<	>	+=	-=	*=
/=	%=	^=	&=	|=	<<	>>	>>=
<<=	==	!=	<=	>=	&&	||	++
—	->*	,	->	[]	()	new	delete
new[]	delete[]
```

Một số toán tử sau không overload được:

- Toán tử . (chấm)
- Toán tử phạm vi ::
- Toán tử điều kiện ?:
- Toán tử sizeof

<br>

💻 **Ví dụ 1: Số phức**
```cpp
#include <iostream>

using namespace std;

class Complex{
    private:
        double realPart;    // phần thực
        double imagPart;    // phần ảo
    
    public:
        Complex(double real = 0, double imag = 0): realPart(real), imagPart(imag){}

        // nạp chồng toán tử +
        Complex operator + (const Complex& other){
            Complex result;
            result.realPart = this->realPart + other.realPart;
            result.imagPart = this->imagPart + other.imagPart;
            return result;
        }

        // nạp chồng toán tử so sánh bằng (==)
        bool operator == (const Complex& other) const{
            return (this->realPart == other.realPart && this->imagPart == other.imagPart);
        }

        // hàm hiển thị
        void display(){
            cout << realPart << " + " << imagPart << "i" << endl;
        }
};

int main() 
{
    Complex c1(3,4);
    Complex c2(5,6);
    Complex c3 = c1 + c2;
    c1.display();
    c2.display();
    c3.display();

    if (c1 == c2){
        cout << "Hai số phức bằng nhau" << endl;
    }
    else{
        cout << "Hai số phức không bằng nhau" << endl;
    }
    return 0;
}
```

📝 **con trỏ ``` this ```**:

- this là một con trỏ đặc biệt có sẵn trong tất cả các method thành viên của class. Nó trỏ đến object hiện tại đang gọi method đó.
- this là hằng con trỏ (constant pointer), nghĩa là this luôn trỏ tới đối tượng hiện tại và bạn không thể thay đổi để this trỏ tới đối tượng khác. Tuy nhiên, giá trị của đối tượng mà this trỏ tới có thể thay đổi trừ khi hàm thành viên được khai báo là const.
- this chỉ tồn tại trong các method của class.
- this tự động được truyền khi bạn gọi method của một object.

<br>

💻 **Ví dụ 2:**
```cpp
#include <iostream>

using namespace std;

class PhanSo{
    private:
        int numerator;   // Tử số
        int denominator; // Mẫu số

        // Hàm tìm Ước Chung Lớn Nhất (Greatest Common Divisor - GCD)
        int gcd(int a, int b) const {
            return (b == 0) ? a : gcd(b, a % b);
        }

        // Hàm rút gọn phân số
        void simplify(){
            int divisor = gcd(numerator, denominator);
            numerator /= divisor;
            denominator /= divisor;
        }

    public:
        PhanSo(int num = 0, int den = 1): numerator(num), denominator(den){
            if (!denominator){
                throw invalid_argument("Mẫu số phải khác 0!");
            }
        }

        // nạp chồng toán tử nhân (*)
        PhanSo operator * (const PhanSo& other){
            // other.tu = 11;
            // other.mau = 13;
            PhanSo ketqua;
            ketqua.numerator   = this->numerator   * other.numerator;
            ketqua.denominator = this->denominator * other.denominator;
            ketqua.simplify();
            return ketqua;
        }
        
        // nạp chồng toán tử cộng (+)
        PhanSo operator + (const PhanSo& other){
            PhanSo ketqua;
            ketqua.numerator = this->numerator * other.denominator + this->denominator * other.numerator;
            ketqua.denominator = this->denominator * other.denominator;
            ketqua.simplify();
            return ketqua;
        }

        // nạp chồng toán tử so sánh bằng (==)
        bool operator == (PhanSo other){
            return (this->numerator == other.numerator && this->denominator == other.denominator);
        }

        // nạp chồng toán tử nhập (>>)
        friend istream& operator >> (istream& input, PhanSo& f){
            cout << "Nhập tử số: ";
            input >> f.numerator;

            cout << "Nhập mẫu số: ";
            input >> f.denominator;

            if (!f.denominator){
                throw invalid_argument("Mẫu số phải khác 0!");
            }
            return input;
        }

        // nạp chồng toán tử xuất (<<)
        friend ostream& operator << (ostream& output, const PhanSo& f){
            output << f.numerator << "/" << f.denominator;
            return output;
        }

        void display(){
            cout << "Tu: " << numerator << " , mau: " << denominator << endl;
        }
};

int main(int argc, char const *argv[])
{
    try{
        PhanSo p1(1, 2);             // 0xc8
        PhanSo p2(4, 6);             // 0xa5
        PhanSo p3 = p1 * p2;

        p1.display();
        p2.display();
        p3.display();

        if (p1 == p2){
            cout << "p1 = p2" << endl;
        }
        else{
            cout << "p1 # p2" << endl;
        }

        PhanSo p4, p5, p6;
        cout << "Nhập phân số p4:\n";
        cin >> p4;

        cout << "Nhập phân số p5:\n";
        cin >> p5;

        p6 = p4 + p5;
        cout << p6;
    }
    catch (const invalid_argument& e) {       // Bắt ngoại lệ invalid_argument
        cout << "Lỗi: " << e.what() << endl;  // Hiển thị thông báo lỗi
    }
    
    return 0;
}
```
**Kết quả**:
```cpp
Tu: 1 , mau: 2
Tu: 4 , mau: 6
Tu: 1 , mau: 3
p1 # p2
Nhập phân số p4:
Nhập tử số: 2
Nhập mẫu số: 3
Nhập phân số p5:
Nhập tử số: 5
Nhập mẫu số: 9
11/9
```
📝 **Toán tử cộng (+)**: dùng để cộng 2 phân số.

📝 **Toán tử nhân (*)**: dùng để nhân 2 phân số.

📝 Sau khi tính toán sẽ tự động rút gọn phân số kết quả thông qua gọi method ``` simplify() ```.

📝 **Toán tử so sánh bằng (==)**: so sánh hai phân số đã được rút gọn để xem chúng có bằng nhau hay không. Nếu tử số và mẫu số của hai phân số bằng nhau, thì chúng bằng nhau.

📝 **Toán tử nhập (>>)**: Toán tử nhập cho phép người dùng nhập tử số và mẫu số từ bàn phím. 

📝 **Toán tử xuất (<<)**: Toán tử xuất hiển thị phân số theo định dạng "tử số/mẫu số".

📝 **try**: một khối lệnh chứa các câu lệnh mà bạn muốn theo dõi các ngoại lệ. Nếu một ngoại lệ xảy ra trong khối try, chương trình sẽ lập tức dừng thực thi các lệnh tiếp theo trong khối try và chuyển sang tìm kiếm khối catch thích hợp để xử lý ngoại lệ.

📝 **catch**: bắt các ngoại lệ được ném ra từ khối try. Nếu một ngoại lệ xảy ra, nó sẽ được bắt ở khối catch tương ứng, và chương trình sẽ thực thi các lệnh trong khối catch để xử lý ngoại lệ đó.

📝 **throw**: là từ khóa được sử dụng để ném một ngoại lệ (exception). Khi một vấn đề xảy ra, chương trình có thể phát hiện lỗi đó và ném ngoại lệ với từ khóa throw. Ngoại lệ này sẽ được chuyển lên chuỗi các lệnh cho đến khi nó được bắt bởi một khối lệnh catch tương ứng.

</p>
</details>

</p>
</details>

<details><summary><b>📚 Đa hình run-time</b></summary>
<p>

Đa hình tại thời điểm chạy hay còn gọi là đa hình ghi đè (**overriding**), đa hình động xảy ra khi một lớp con ghi đè lại một phương thức của lớp cha và phương thức này được gọi thông qua con trỏ hoặc tham chiếu đến lớp cha. Phương thức của lớp con sẽ được gọi tại thời điểm thực thi, không phải lúc biên dịch.

**(Sẽ nói cụ thể ở bài Virtual Function)**

</p>
</details>

</p>
</details>

<details><summary><b>4.4. Tính trừu tượng (Abstraction)</b></summary>
<p>

- Trừu tượng hóa và việc chỉ ra những thông tin quan trọng nhất của đối tượng, giấu đi nhưng chi tiết phức tạp không cần thiết (logic xử lý, thuật toán để đưa ra kết quả cuối cùng), giúp tập trung vào cách sử dụng đối tượng hơn là cách nó được thực hiện.

<br>

💻 **Ví dụ:**
```cpp
#include <iostream>
#include <string>
#include <cmath>

using namespace std;

class PTBH{
    private:    // x1, x2 là tính đóng gói
        double x1;
        double x2;
        double a,b,c;
        double delta(){
            return (double)(b*b - 4*a*c);
        }
        void ketqua(){
            if (PTBH::delta() < 0){
                cout << "PTVN" << endl;
            }
            else if (PTBH::delta() == 0){
                cout << "x1 = x2 = " << (double)(-b/(2*a)) << endl;
            }
            else{
                cout << "x1 = " << (-b + sqrt(delta()))/(2*a) << endl;
                cout << "x2 = " << (-b - sqrt(delta()))/(2*a) << endl;
            }
        }
    public:
        void printResult(double a, double b, double c){
            PTBH::a = a;
            PTBH::b = b;
            PTBH::c = c;
            ketqua();
        }
};

int main(int argc, char const *argv[]){
    PTBH phuongtrinh1;
    phuongtrinh1.printResult(1,2,10);
    return 0;
}
```

📝 Tính trừu tượng giống với tính đóng gói nhưng khác nhau về ý nghĩa.

📝 Tính đóng gói: những property ở private thì phải truy cập gián tiếp thông qua các method, constructor, getter, setter.

📝 Tính trừu tượng: khi thiết kế một class cho người dùng sử dụng thì người dùng chỉ được phép sử dụng những hàm do lập trình viên quy định , quá trình tạo ra kết quả cuối cùng không được phép truy cập đến, bị ẩn đi mặc dù nằm trong private.

<br>

</p>
</details>

</p>
</details>

<br>

<details><summary><b>5. Hàm ảo (Virtual Function)</b></summary>
<p>

<details><summary><b>5.1. Khái niệm</b></summary>
<p>

- Virtual Function (hàm ảo) là một hàm thành viên được khai báo trong lớp cha với từ khóa ``` virtual ```.
- Khi một hàm là virtual, nó có thể được ghi đè (``` override ```) trong các lớp con.
- Khi gọi một hàm ảo thông qua một con trỏ hoặc tham chiếu đến lớp con, hàm sẽ được quyết định dựa trên đối tượng thực tế mà con trỏ hoặc tham chiếu đang trỏ tới chứ không dựa vào kiểu của con trỏ.

💻 **Ví dụ:**
```cpp
class Base{
	public:
	    virtual void display(){
	        cout << "Display from Base class" << endl;
	    }
};
```

<br>

</p>
</details>

<details><summary><b>5.2. Override (Ghi đè hàm ảo)</b></summary>
<p>

- Các lớp con có thể ghi đè hàm ảo bằng cách định nghĩa lại nó.
- Khi một hàm ảo được ghi đè, hành vi của nó sẽ phụ thuộc vào kiểu của đối tượng thực tế, chứ không phải kiểu của con trỏ hay tham chiếu.

<br>

💻 **Ví dụ 1:**
```cpp
#include <iostream>
using namespace std;

class cha{
    public:
        virtual void display(){                            // Hàm ảo
            cout << "display from class cha" << endl;
        }
};

class con : public cha{
    public:
        void display() override{                           // Ghi đè hàm ảo
            cout << "display from class con" << endl;
        }
};

int main(){
    cha *ptr;
    con obj;

    // trỏ con trỏ class cha đến đối tượng class con
    ptr = &obj;

    // Gọi hàm ảo
    ptr->display();
}
```
**Kết quả**:
```cpp
display from class con
```

<br>

💻 **Ví dụ 2:**
```cpp
#include <iostream>
#include <string>

using namespace std;

class DoiTuong{
    protected:
        string ten;
        int id;

    public:
        DoiTuong(){  
            static int ID = 1;
            id = ID;
            ID++;
        }

        void setName(string _ten){
            // check chuỗi nhập vào
            ten = _ten;
        }

        virtual void display(){
            cout << "ten: " << ten << endl;
            cout << "id: " << id << endl;
        }
};

class SinhVien : public DoiTuong{
    protected:
        string chuyenNganh;

    public:
        void setChuyenNganh(string _nganh){
            chuyenNganh = _nganh;
        }

        void display() override {
            cout << "ten: " << ten << endl;
            cout << "id: " << id << endl;
            cout << "chuyen nganh: " << chuyenNganh << endl;
        }
};

class HocSinh : public DoiTuong{
    protected:
        string lop;
   
    public:
        void setLop(string _lop){
            lop = _lop;
        }

        void display() override {
            cout << "ten: " << ten << endl;
            cout << "id: " << id << endl;
            cout << "lop: " << lop << endl;
        }
};

class GiaoVien : public DoiTuong{
    protected:
        string chuyenMon;

    public:
        void setChuyenMon(string _mon){
            chuyenMon = _mon;
        }

        void display() override {
            cout << "ten: " << ten << endl;
            cout << "id: " << id << endl;
            cout << "chuyen mon: " << chuyenMon << endl;
        }
};

int main(int argc, char const *argv[])
{
    SinhVien sv1;
    sv1.setName("Trung");
    sv1.setChuyenNganh("TDH");

    HocSinh hs1;
    hs1.setName("Tuan");
    hs1.setLop("12A1");

    GiaoVien gv1;
    gv1.setName("Hoang");
    gv1.setChuyenMon("Toan");

    DoiTuong *dt;

    dt = &sv1;
    dt->display();

    dt = &hs1;
    dt->display();

    dt = &gv1;
    dt->display();


    // DoiTuong *ptr[] = {&sv1, &hs1, &gv1};

    // ptr[0]->display();
    // ptr[1]->display();
    // ptr[2]->display();

    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>5.3. Hàm ảo thuần túy (Pure Virtual Function)</b></summary>
<p>

- Hàm ảo thuần túy là một hàm ảo không có phần định nghĩa trong class cha, được khai báo với cú pháp = 0 và khiến class cha trở thành class trừu tượng, nghĩa là không thể tạo đối tượng từ class này.

💻 **Ví dụ:**
```cpp
#include <iostream>
using namespace std;

class Base{
    public:
        virtual void display() = 0;
};

int main(int argc, char const *argv[])
{
    Base baseObj;
    return 0;
}
```
**Kết quả**:
```cpp
error: cannot declare variable 'baseObj' to be of abstract type 'Base'
```

<br>

- Hàm ảo thuần túy phải được ghi đè trong class con và chỉ các class con đã ghi đè tất cả các hàm ảo thuần túy mới có thể tạo ra đối tượng.

💻 **Ví dụ:**
```cpp
#include <iostream>
using namespace std;

class cha{
    public:
        virtual void display() = 0; // Hàm ảo thuần túy
};

class con : public cha{
    public:
        void display() override{   // Ghi đè hàm ảo thuần túy
            cout << "display from class con" << endl;
        }
};

int main(){
    cha *ptr;
    con obj;

    ptr = &obj;
    ptr->display(); // Output: display from class con

    return 0;
}
```

📝 Con trỏ ptr là của class cha, nhưng nó trỏ đến đối tượng của class con.

📝 Method display() trong class cha được khai báo là hàm thuần ảo, vì vậy khi gọi ``` ptr->display() ```, C++ sẽ kiểm tra xem đối tượng thực sự mà ptr trỏ tới là gì, và cụ thể ở đây là object ``` obj ``` của class con và nó gọi hàm ``` display() ``` của class con.

📝 Đây là **đa hình**: Cùng một giao diện, nhưng hành vi thực tế khác nhau dựa trên kiểu của đối tượng thực sự.

<br>

💻 **Ví dụ:**
```cpp
#include <iostream>
#include <string>

using namespace std;

class Xe{
    public:
        virtual void hienThiThongTin() = 0;
};

class Toyota : public Xe{
    private:
        string model;
        int namSanXuat;
        string dongCo;

    public:
        Toyota(string m, int nam, string dongCo): model(m), namSanXuat(nam), dongCo(dongCo){}

        void hienThiThongTin() override {
            cout << "Hang xe: Toyota\n";
            cout << "Model: " << model << "\n";
            cout << "Nam san xuat: " << namSanXuat << "\n";
            cout << "Dong co: " << dongCo << "\n";
        }
};

class Honda : public Xe{
    private:
        string model;
        int namSanXuat;
        string mauSac;

    public:
        Honda(string m, int nam, string mau): model(m), namSanXuat(nam), mauSac(mau){}

        void hienThiThongTin() override {
            cout << "Hang xe: Honda\n";
            cout << "Model: " << model << "\n";
            cout << "Nam san xuat: " << namSanXuat << "\n";
            cout << "Mau sac: " << mauSac << "\n";
        }
};

class Mazda : public Xe{
    private:
        string model;
        int namSanXuat;
        string loaiDanDong;

    public:
        Mazda(string m, int nam, string loaiDanDong): model(m), namSanXuat(nam), loaiDanDong(loaiDanDong){}

        void hienThiThongTin() override {
            cout << "Hang xe: Mazda\n";
            cout << "Model: " << model << "\n";
            cout << "Nam san xuat: " << namSanXuat << "\n";
            cout << "Loai dan dong: " << loaiDanDong << "\n";
        }
};

int main() {
    // Toyota to;
    // Honda ho;
    // Mazda mz;
    // Xe *xe;

    // xe = &to;
    // xe->hienThiThongTin();
   
    Mazda cx3("CX-3", 2019, "Dan dong 4 banh");

    Xe *ptr[] = {new Toyota("campry", 2020, "V6"),
                 new Honda("civic", 2019, "do"),
                 new Mazda("CX-5", 2021, "Dan dong 4 banh"),
                 new Toyota("corolla", 2018, "I4 1.8L"),
                 new Honda("accord", 2020, "den"),
                 &cx3};
   
    for (int i=0; i<6; i++){
        ptr[i]->hienThiThongTin();
        cout << "--------------" << endl;
        delete ptr[i];
    }

    return 0;
}
```

<br>

</p>
</details>

</p>
</details>

<br>

<details><summary><b>6. Template</b></summary>
<p>

Template là một tính năng mạnh mẽ giúp viết mã tổng quát, có thể làm việc với nhiều kiểu dữ liệu khác nhau mà không cần phải viết lại mã cho từng kiểu cụ thể. Có hai loại template chính trong C++:

<details><summary><b>6.1. Function Template (Hàm mẫu)</b></summary>
<p>

Hàm mẫu cho phép viết một hàm tổng quát có thể hoạt động với nhiều kiểu dữ liệu khác nhau.

💻 **Ví dụ 1:**
```cpp
#include <iostream>

using namespace std;

template <typename T>

T sum(T a, T b){
    return a+b;
}

int main(int argc, char const *argv[])
{
    cout << "Sum: " << sum(1, 5) << endl;
    cout << "Sum: " << sum(1.5, 5.8) << endl;
    // cout << "Sum: " << sum(1, 5.8) << endl; // wrong
    return 0;
}
```

📝 Khi gọi hàm sum, compiler sẽ suy luận kiểu dựa trên các tham số truyền vào.

📝 Hàm trên tính tổng 2 số nhưng tham số truyền vào phải cùng kiểu dữ liệu ( (int, int) hoặc (double, double) hoặc (float, float) ...). Nếu cố tình truyền vào 2 tham số với kiểu dữ liệu khác nhau thì compiler sẽ báo lỗi ``` error: no matching function for call to 'sum(int, double)' ```

📝 Nếu muốn tính tổng 2 số có kiểu dữ liệu khác nhau thì phải khai báo một template khác.

<br>

💻 **Ví dụ 2:**
```cpp
#include <iostream>

using namespace std;

template <typename typeA, typename typeB>

auto sum(typeA a, typeB b){
    return a+b;
}

int main(int argc, char const *argv[])
{
    cout << "Sum: " << sum(1, 5) << endl;
    cout << "Sum: " << sum(1.5, 5.8) << endl;
    cout << "Sum: " << sum(1.6, 5) << endl; 
    return 0;
}
```

📝 Từ khóa auto để cho phép trình biên dịch tự động suy ra kiểu trả về của phép cộng giữa a và b.

📝 Phiên bản này tổng quát hơn so với phiên bản ban đầu vì nó có thể xử lý hai kiểu dữ liệu khác nhau.

<br>

</p>
</details>

<details><summary><b>6.2. Class Template (Lớp mẫu)</b></summary>
<p>

Lớp mẫu cho phép định nghĩa các lớp chung chung có thể hoạt động với các kiểu dữ liệu khác nhau.

💻 **Ví dụ:**
```cpp
#include <iostream>

using namespace std;

template <typename T>

class Sensor{
    private:
        T value;
    
    public: 
        Sensor(T init): value(init){}

        void readSensor(T newValue){
            value = newValue;
        }

        T getValue(){
            return value;
        }

        void display(){
            cout << "Gia tri cam bien: " << value << endl;
        }
};

int main(int argc, char const *argv[])
{
    Sensor<double> tempSensor(25.7);
    tempSensor.display();

    Sensor<int> lightSensor(512);
    lightSensor.display();

    Sensor<string> stateSensor("OFF");
    stateSensor.display();
    return 0;
}
```

</p>
</details>

</p>
</details>

<br>

<details><summary><b>7. Namespace</b></summary>
<p>

<details><summary><b>7.1. Khái niệm</b></summary>
<p>

- Namespace là từ khóa trong C++ được sử dụng để định nghĩa một phạm vi nhằm mục đích phân biệt các biến, hàm, class, ... cùng tên trong các phần của chương trình hoặc các thư viện khác nhau.
- Cú pháp:
```cpp
namespace name_of_namespace{
    /* source code */
}
```

💻 **Ví dụ 1:**
File B.cpp
```cpp
#include <iostream>

using namespace std;

namespace fileB{
    void display(int id){
        cout << "This is file B.cpp, id = " << id << endl;
    }
}
```

File C.cpp
```cpp
#include <iostream>

using namespace std;

namespace fileC{
    void display(int id){
        cout << "This is file C.cpp, id = " << id << endl;
    }
}
```

File A.cpp
```cpp
#include <iostream>
#include "fileB.cpp"
#include "fileC.cpp"

using namespace std;

int main(int argc, char const *argv[])
{
    fileB::display(2);
    fileC::display(2);
    return 0;
}
```

<br>

💻 **Ví dụ 2:**
```cpp
#include <iostream>


using namespace std;


namespace A{
    char *name = (char*)"Trung 20";


    void display(){
        cout << "Name: " << name << endl;
    }
}


namespace B{
    char *name = (char*)"Trung 21";


    void display(){
        cout << "Name: " << name << endl;
    }


    class SinhVien{
        private:
            string name;
            int id;
        
        public:
            SinhVien(){
                static int _ID = 1;
                id = _ID;
                _ID++;
            }


            void setName(string newName){
                name = newName;
            }


            string getName() const{
                return name;
            }


            int getID() const{
                return id;
            }


            void display(){
                cout << "Tên sinh viên: " << getName() << endl;
                cout << "ID: " << getID() << endl;
            }
    };
}


int main(int argc, char const *argv[])
{
    cout << "Name: " << A::name << endl;
    cout << "Name: " << B::name << endl;


    A::display();
    B::display();


    B::SinhVien sv1;
    sv1.setName("Trung");
    sv1.display();
    return 0;
}
```

📝 Chuỗi "Trung 21": nằm ở rdata (data read-only) hoặc text tùy compiler.

📝 char *name: biến con trỏ toàn cục, đã khởi tạo nên lưu ở data.

📝 string name: một biến cục bộ trong mỗi đối tượng SinhVien. Khi một đối tượng SinhVien được tạo, vùng nhớ cho name sẽ được cấp phát trong:

- stack: nếu đối tượng SinhVien được khai báo tĩnh hoặc cục bộ trong một hàm.
- Heap: nếu đối tượng SinhVien được cấp phát động bằng new.

📝 int id: Tương tự name, biến id là biến thành viên non-static của class, nên sẽ được lưu trong stack hoặc heap tùy vào cách cấp phát đối tượng SinhVien.

📝 static int _ID: biến static cục bộ trong constructor SinhVien. Biến static cục bộ được lưu trữ trong data segment và tồn tại suốt thời gian chạy của chương trình.


</p>
</details>

<details><summary><b>7.2. Từ khóa <b>using</b> trong namespace</b></summary>
<p>

- Từ khóa using cho phép bạn khai báo namespace để sử dụng các thành viên trong namespace mà không cần phải sử dụng toán tử '::' mỗi khi truy cập.
- Chỉ sử dụng using namespace khi member muốn truy cập đến là duy nhất.

💻 **Ví dụ:**
```cpp
#include <iostream>

using namespace std;

namespace A{
    char *name = (char*)"Trung 20";
}

namespace B{
    char *name = (char*)"Trung 21";
}

using namespace A;

// using namespace B; // error: tham chiếu không rõ ràng

int main(int argc, char const *argv[])
{
    cout << "Name: " << name << endl;
    cout << "Name: " << B::name << endl;
    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>7.3. Namespace lồng nhau</b></summary>
<p>

- C++ cho phép tạo các namespace lồng nhau, nghĩa là một namespace có thể chứa một namespace khác bên trong nó.

💻 **Ví dụ:**
```cpp
#include <iostream>

using namespace std;

namespace A{
    char *name = (char*)"Trung 20";

    namespace C{
        char *str = (char*)"Hoang";
    }
}

namespace B{
    char *name = (char*)"Trung 21";
}

using namespace A::C;

int main(int argc, char const *argv[])
{
    cout << "Name: " << A::name << endl;
    cout << "Name: " << B::name << endl;
    cout << "Name: " << A::C::str << endl;

    str = (char*)"Hello World";
    cout << "Name: " << str << endl;
    return 0;
}
```

</p>
</details>

<details><summary><b>7.4. Namespace mở rộng</b></summary>
<p>

- Namespace có thể được mở rộng bằng cách khai báo nhiều lần cùng một tên namespace trong các phần khác nhau của chương trình. Các khai báo này sẽ được trình biên dịch ghép lại thành một namespace duy nhất.

💻 **Ví dụ:**

**File lcd1.hpp**
```cpp
#ifndef _LCD1_HPP
#define _LCD1_HPP

namespace LCD{
    int temp;

    class lcd{
        private:

        public:
            lcd(/* args */);
            ~lcd();
    };

    lcd::lcd(){}

    lcd::~lcd(){}
}

#endif
```

**File lcd2.hpp**
```cpp
#ifndef _LCD2_HPP
#define _LCD2_HPP

namespace LCD{
    char *text;
}

#endif
```

**File main.cpp**
```cpp
#include <iostream>
#include "lcd1.hpp"
#include "lcd2.hpp"

using namespace std;

int main(int argc, char const *argv[])
{
    LCD::lcd;
    LCD::temp;
    LCD::text;
    return 0;
}

```

</p>
</details>

<details><summary><b>7.5. Namespace tiêu chuẩn (std)</b></summary>
<p>

- Một trong những namespace quan trọng và phổ biến nhất trong C++ là std. Tất cả các thành phần của thư viện chuẩn C++ (như cout, cin, vector, string) đều được định nghĩa bên trong namespace std.

💻 **Ví dụ:**
```cpp
#include <iostream>

using namespace std;

namespace std{
    struct{
        int x;
        int y;
    } point;

    void display(){
        cout << "x = " << point.x << endl;
        cout << "y = " << point.y << endl;
    }
}

int main(int argc, char const *argv[])
{
    std::cout << "Hello world!" << std::endl;

    cout << "Hello world!" << endl;
   
    std::point.x = 10;
    std::point.y = 20;
    std::display();

    return 0;
}
```

</p>
</details>

</p>
</details>

<br>

<details><summary><b>8. Standard Template Library (STL)</b></summary>
<p>

<details><summary><b>8.1. Khái niệm</b></summary>
<p>

STL là một thư viện trong ngôn ngữ lập trình C++ cung cấp một tập hợp các template classes và functions để thực hiện nhiều loại cấu trúc dữ liệu và các thuật toán phổ biến. STL đã trở thành một phần quan trọng của ngôn ngữ C++ và làm cho việc lập trình trở nên mạnh mẽ, linh hoạt và hiệu quả.

Một số thành phần chính của STL:

- Container
- Iterator
- Algorithms
- Functor

</p>
</details>

<details><summary><b>8.2. Vector</b></summary>
<p>

- Là một trong những container quan trọng nhất trong STL.
- Bản chất của vector là một class template. Nó cung cấp một mảng động với khả năng thay đổi kích thước một cách linh hoạt.
- Có thể truy cập các phần tử của mảng bằng cách sử dụng chỉ số.
- Có thể chèn hoặc xóa bất kỳ phần tử nào trong mảng.
- Một số method của vector:

+ **at()**: truy cập để đọc hoặc thay đổi giá trị phần tử của vector.
+ **size()**: trả về kích thước của vector.
+ **resize()**: thay đổi kích thước của vector.
+ **begin()**: Trả về một iterator trỏ đến địa chỉ phần tử đầu tiên của vector.
+ **end()**: Trả về một iterator trỏ đến địa chỉ sau phần tử cuối cùng của vector.
+ **push_back()**: thêm phần tử vào vị trí cuối của vector.
+ **pop_back()**: xóa phần tử ở vị trí cuối của vector.
+ **insert()**: thêm phần tử vào vị trí bất kỳ.
+ **erase()**: xoá phần tử ở ví trí bất kỳ hoặc xóa các phần tử trong phạm vi được chỉ định.
+ **clear()**: xóa toàn bộ phần tử của vector (thu hồi tất cả địa chỉ).

<br>

💻 **Ví dụ:**
```cpp
#include <iostream>
#include <vector>

using namespace std;

int main() {
    // Khởi tạo vector bằng danh sách khởi tạo {}
    vector<int> vec = {1, 2, 3, 4, 5};

    // Thêm phần tử vào cuối vector bằng push_back
    vec.push_back(6);
    vec.push_back(7);

    // Thay đổi kích thước của vector với resize
    vec.resize(10); // Tăng kích thước vector lên 10 phần tử, các phần tử mới được khởi tạo với 0
    vec.at(8) = 50; // ghi giá trị mới
    vec.at(9) = 25;

    // In vector sau khi resize bằng vòng lặp for cải tiến và phương thức at()
    cout << "Vector sau khi resize: ";
    for (auto item : vec) {
        cout << item << " ";
    }
    cout << endl;

    // Thêm phần tử vào vị trí thứ 3 (sau phần tử thứ 2) bằng insert
    vec.insert(vec.begin() + 2, 99);

    // Thêm một phần tử khác vào vị trí thứ 5
    vec.insert(vec.begin() + 4, 88);

    // Xóa phần tử thứ 4 (sau phần tử đầu tiên thêm vào) bằng erase
    vec.erase(vec.begin() + 3);

    // Xóa thêm một phần tử khác tại vị trí thứ 6
    vec.erase(vec.begin() + 5);

    // Sử dụng pop_back() để xóa phần tử cuối cùng
    vec.pop_back();

    // In vector sau khi sử dụng insert, erase, và pop_back, sử dụng iterator
    cout << "Vector sau khi thêm, xóa và pop_back: ";
    for (vector<int>::iterator it = vec.begin(); it != vec.end(); ++it) {
        cout << *it << " ";
    }
    cout << endl;

    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>8.3. List</b></summary>
<p>

- Triển khai dưới dạng danh sách liên kết hai chiều (linked list), nghĩa là gồm những nodes có địa chỉ ngẫu nhiên liên kết với nhau.
- Chỉ cỏ thể truy cập các phần tử một cách tuần tự (không hỗ trợ truy cập ngẫu nhiên).
- Có thể chèn/xóa ở bất kỳ vị trí nào trong danh sách.
- Một số method của list:

+ **size()**: trả về kích thước của list hay số lượng node trong list.
+ **begin()**: Trả về iterator trỏ đến địa chỉ node đầu tiên của list.
+ **end()**: Trả về iterator trỏ đến địa chỉ node cuối cùng của list.
+ **push_back()**: thêm node vào vị trí cuối của list.
+ **push_front()**: thêm node vào vị trí đầu của list.
+ **pop_back()**: xóa node ở vị trí cuối của list.
+ **pop_front()**: xóa node ở vị trí đầu của list.
+ **insert()**: thêm node ở vị trí bất kỳ
+ **erase()**: xoá node ở ví trí bất kỳ hoặc xóa các node trong phạm vi được chỉ định.
+ **remove()**: Xóa các node với giá trị được chỉ định.

<br>

💻 **Ví dụ:**
```cpp
#include <iostream>
#include <list>

using namespace std;

int main() {
    // Khởi tạo list bằng danh sách khởi tạo {}
    list<int> lst = {1, 2, 3, 4, 5};

    // Thêm phần tử vào cuối list bằng push_back
    lst.push_back(6);
    lst.push_back(7);

    // Thêm phần tử vào đầu list bằng push_front
    lst.push_front(0);
    lst.push_front(-1);

    // Thay đổi kích thước của list bằng resize
    lst.resize(12, 0); // Tăng kích thước list lên 12 phần tử, các phần tử mới được khởi tạo với 0

    list<int>::iterator it;

    // In list sau khi resize bằng vòng lặp for cải tiến
    cout << "List sau khi push_front và resize: ";
    for (auto it = lst.begin(); it != lst.end(); ++it) {
        cout << *it << " ";
    }
    cout << endl;

    // Thêm phần tử vào vị trí thứ 3 (sau phần tử thứ 2) bằng insert
    it = lst.begin();
    for (int i = 0; i < 2; ++i) {
        ++it;
    }
    lst.insert(it, 99);

    // Thêm một phần tử khác vào vị trí thứ 5
    it = lst.begin();
    for (int i = 0; i < 4; ++i) {
        ++it;
    }
    lst.insert(it, 88);

    // Xóa phần tử thứ 4 (sau phần tử đầu tiên thêm vào) bằng erase
    it = lst.begin();
    for (int i = 0; i < 3; ++i) {
        ++it;
    }
    lst.erase(it);

    // Xóa thêm một phần tử khác tại vị trí thứ 6
    it = lst.begin();
    for (int i = 0; i < 5; ++i) {
        ++it;
    }
    lst.erase(it);

    // Sử dụng pop_back() để xóa phần tử cuối cùng
    lst.pop_back();

    // Sử dụng pop_front() để xóa phần tử đầu tiên
    lst.pop_front();

    // Kiểm tra list có rỗng không bằng empty()
    if (lst.empty()) {
        cout << "List hiện tại rỗng." << endl;
    } else {
        cout << "List sau khi thêm, xóa, pop_back và pop_front: ";
        for (auto it = lst.begin(); it != lst.end(); ++it) {
            cout << *it << " ";
        }
        cout << endl;
    }

    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>8.4. Map</b></summary>
<p>

- Cung cấp một cấu trúc dữ liệu ánh xạ key – value, trong đó value giữa các object có thể giống nhau nhưng key là duy nhất trong map, nếu có 2 value cùng key thì lấy key và value sau cùng.
- Có thể thêm phần tử mới vào map bằng cách sử dụng operator [] hoặc hàm insert(). Để xóa phần tử, bạn có thể sử dụng hàm erase().
- Có thể sử dụng iterator để duyệt qua các phần tử của map.

<br>

💻 **Ví dụ 1:**
```cpp
#include <iostream>
#include <map>

using namespace std;

int main(int argc, char const *argv[]){    
    map<int, string> array; // key: int, value: string

    array[1] = "Hoang";
    array[2] = "Tuan";
    array[3] = "Anh";

    for (auto item : array){
        cout << "key: " << item.first << " - value: " << item.second << endl; 
    }

    return 0;
}
```

<br>

💻 **Ví dụ 2:**
```cpp
#include <iostream>
#include <map>
#include <string>

using namespace std;

int main(int argc, char const *argv[]){
    map<string, int> myMap;

    // Thêm phần tử vào map
    myMap["one"] = 1;
    myMap["two"] = 2;
    myMap["three"] = 3;

    for (auto item : myMap){
        cout << "Key: " << item.first << " , " << "Value: " << item.second << endl;
    }

    cout << "---------------" << endl;

    map<string, int> ::iterator it; // it = myMap.begin()

    // myMap.insert(make_pair("four", 4));
    myMap.insert({"four", 4});
    myMap.erase("one");

    for (it = myMap.begin(); it != myMap.end(); it++){
        cout << "Key: " << (*it).first << " , " << "Value: " << (*it).second << endl;
    }

    return 0;
}
```

<br>

💻 **Ví dụ 3:**
```cpp
#include <iostream>
#include <map>
#include <string>

using namespace std;

typedef struct{
    string ten;
    int    tuoi;
    string lop;
} SinhVien;

int main(int argc, char const *argv[]){
    map<string, SinhVien> Database = {
        {
            "SV100", {
                "Hoang",
                20,
                "DDT"
            }
        },
        {
            "SV101", {
                "Tuan",
                21,
                "CDT"
            }
        },
        {
            "SV102", {
                "Anh",
                22,
                "KTMT"
            }
        }
    };

    for (auto item : Database){
        cout << "ID: " << item.first << " - Ten: " << item.second.ten << " - Tuoi: " << item.second.tuoi << " - Lop: " << item.second.lop << endl;
    }
    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>8.5. Iterator</b></summary>
<p>

- Iterator là một đối tượng đại diện cho vị trí trong container và cho phép duyệt qua các phần tử của container (như std::vector, std::list, std::map, v.v.). Chúng có thể được xem là một công cụ để truy cập tuần tự các phần tử mà không cần biết cấu trúc nội bộ của container. Iterator sẽ lưu địa chỉ đầu tiên của list và cho phép duyệt qua từng phần tử.
- Iterator là một class, bên trong quản lý một con trỏ. Con trỏ này là thành phần giúp iterator biết được nó đang trỏ tới phần tử nào trong container. Con trỏ này lưu trữ địa chỉ của phần tử đầu tiên trong vector hay node đầu tiên trong list, và tử đó duyệt qua từng phần tử.
- Khi bạn muốn in ra địa chỉ mà một iterator đang trỏ tới, bạn cần sử dụng &(*it).

+ *it: (*) là toán tử truy cập giá trị đã được định nghĩa lại (overload) bên trong iterator.
+ &(*it): lấy địa chỉ của giá trị mà iterator it trỏ tới.

- Các thư viện như std::vector, std::list, và nhiều container khác trong C++ Standard Library đều định nghĩa một class iterator riêng bên trong chúng. Điều này giúp mỗi container có một loại iterator phù hợp với cách tổ chức dữ liệu và cách duyệt qua các phần tử của nó.

</p>
</details>

</p>
</details>

<br>

<details><summary><b>9. Lambda</b></summary>
<p>

</p>
</details>

<br>

<details><summary><b>10. Design Patterns</b></summary>
<p>

<details><summary><b>10.1. Khái niệm</b></summary>
<p>

Design Patterns là các giải pháp tổng quát cho các vấn đề phổ biến trong phát triển phần mềm. Chúng là một dạng "công thức" giúp các lập trình viên xử lý các tình huống thường gặp trong quá trình thiết kế.

Design Patterns không phải là ngôn ngữ cụ thể nào cả. Nó có thể thực hiện được ở phần lớn các ngôn ngữ lập trình, chẳng hạn như C/C++, C#, Java,...

Design Patterns không phải là ngôn ngữ cụ thể nào cả. Nó có thể thực hiện được ở phần lớn các ngôn ngữ lập trình, chẳng hạn như Java, C#, thậm chí là Javascript hay bất kỳ ngôn ngữ lập trình nào khác.

Design Patterns được chia thành 3 nhóm chính:

- Creational Pattern (mẫu khởi tạo): Quản lý việc khởi tạo đối tượng, ví dụ: **Singleton**, **Factory**, Prototype,...
- Behavioral Pattern (mẫu tương tác): Xác định cách các đối tượng tương tác với nhau ví dụ: **Observer**, **MVP**, Iterator,...
- Structural Pattern (mẫu cấu trúc): Tổ chức cấu trúc của các lớp và đối tượng, ví dụ: **Decorator**, Adapter, Composite,...

<br>

</p>
</details>

<details><summary><b>10.2. Singleton Pattern</b></summary>
<p>

- Singleton là một mẫu thiết kế thuộc nhóm Creational (mẫu khởi tạo), nó đảm bảo rằng một lớp chỉ có duy nhất một instance và cung cấp một điểm truy cập toàn cục đến instance này.
- Singleton thường sử dụng cho những hệ thống chỉ cần một phiên bản duy nhất như: kết nối cơ sở dữ liệu, bộ nhớ đệm (cache), logger để ghi log, hoặc cấu hình hệ thống.

<br>

💻
```cpp
#include <iostream>

void gpioInit(){
    std::cout << "GPIO Initialized" << std::endl;
}

void gpioSetPin(int pin, bool value){
    std::cout << "Pin " << pin << " set to " << (value ? "HIGH" : "LOW") << std::endl;
}

void gpioReadPin(int pin){
    std::cout << "Reading Pin " << pin << std::endl;
}

class GPIOManager{
    private:
        GPIOManager(){}

        static GPIOManager* instance;

        void init(){
            gpioInit();
        }

    public:
        static GPIOManager *getInstance(){
            if(!instance){
                instance = new GPIOManager(); // 0xc8
                instance->init();
                // bổ sung thêm tính năng
            }
            return instance;
        }
        
        void setPin(int pin, bool value){
            gpioSetPin(pin, value);
        }

        void readPin(int pin){
            gpioReadPin(pin);
        }
};

GPIOManager* GPIOManager::instance = nullptr; // 0xc8 : địa chỉ cố định

int main(int argc, char const *argv[])
{
    GPIOManager* gpioManager1 = GPIOManager::getInstance();

    gpioManager1->setPin(1, true);

    gpioManager1->readPin(2);

    GPIOManager* gpioManager2 = GPIOManager::getInstance();

    return 0;
}
```
📝 gpioManager1 và gpioManager2 đều cùng trỏ đến cùng một đối tượng ``` instance ``` có địa chỉ là 0xc8, được gọi thông qua method ``` getIntance() ```, nghĩa là chúng đều cùng trỏ đến 1 vùng làm việc chung.

📝 Constructor ``` GPIOManager() ``` nằm ở private để không cho phép khởi tạo object thông thường.

📝 ``` static GPIOManager* instance ```: con trỏ instance thuộc class GPIOManager và phải được khởi tạo trước vì là static trong class: ``` GPIOManager* GPIOManager::instance = nullptr ```, khởi tạo ban đầu là con trỏ NULL.

📝 Khi ``` gpioManager1 ``` cấu hình Pin hay đọc giá trị Pin nào thì ``` gpioManager2 ``` cũng sẽ thực hiện tương tự.

<br>

💻
```cpp
#include <iostream>
#include "stm32f10x.h"  

class UART{
    private:
        static UART* instance;  
        
        UART(){
            initUART();
        }

        void initUART(){
            RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1, ENABLE);
            USART_InitTypeDef USART_InitStructure;

            USART_InitStructure.USART_BaudRate = 115200;
            USART_InitStructure.USART_WordLength = USART_WordLength_8b;
            USART_InitStructure.USART_StopBits = USART_StopBits_1;
            USART_InitStructure.USART_Parity = USART_Parity_No;
            USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None;
            USART_InitStructure.USART_Mode = USART_Mode_Rx | USART_Mode_Tx;

            USART_Init(USART1, &USART_InitStructure);
            USART_Cmd(USART1, ENABLE);
        }

    public:
        static UART* getInstance(){
            if (instance == nullptr){
                instance = new UART();  // Tạo instance nếu chưa có
            }
            return instance;
        }

        void sendData(uint8_t data){
            while (USART_GetFlagStatus(USART1, USART_FLAG_TXE) == RESET);
            USART_SendData(USART1, data);
        }

        uint8_t receiveData(){
            while (USART_GetFlagStatus(USART1, USART_FLAG_RXNE) == RESET);
            return USART_ReceiveData(USART1);
        }
};

UART* UART::instance = nullptr;

int main()
{
    UART* uart1 = UART::getInstance();

    // Gửi một ký tự qua UART
    uart1->sendData('H');

    UART* uart2 = UART::getInstance();

    // Nhận một ký tự từ UART
    uint8_t received = uart2->receiveData();

    while (1) {
        // Thực hiện công việc liên quan khác
    }
}
```
📝 uart1 và uart2 cùng trỏ đến một đối tượng ``` instance ``` thông qua method ``` getInstance ``` nên sẽ có chung cấu hình, điều này đảm bảo rằng cấu hình UART là nhất quán trong toàn bộ hệ thống.

<br>

**Ứng dụng**:

- Quản lý tài nguyên hệ thống (cơ sở dữ liệu, tập tin, máy in).
- Các bộ nhớ đệm (cache), quản lý trạng thái ứng dụng.
- Đăng ký logger cho toàn bộ ứng dụng.

<br>

</p>
</details>

<details><summary><b>10.3. Observer Pattern</b></summary>
<p>

Observer là một mẫu thiết kế thuộc nhóm Behavioral, cho phép một đối tượng (gọi là Subject) thông báo cho nhiều đối tượng khác (gọi là Observer) về sự thay đổi trạng thái mà không cần biết chi tiết về các đối tượng đó.

**Đặc điểm chính của Observer Pattern**:

1. **Mối quan hệ của Subject và Observer**:

- Subject giữ một danh sách các Observer. Các Observer đăng ký nhận thông báo từ Subject khi có sự thay đổi trạng thái. Observer có thể thêm, xoá hoặc cập nhật trong danh sách này.

2. **Tự động thông báo (Push Notification)**:

- Khi trạng thái của Subject thay đổi, nó sẽ tự động thông báo cho tất cả các Observer đã đăng ký. Các Observer không cần chủ động kiểm tra trạng thái của Subject mà sẽ nhận thông báo ngay khi có thay đổi.

3. **Tính linh hoạt và mở rộng**:

- Observer Pattern cho phép dễ dàng thêm hoặc xóa các Observer mà không cần thay đổi Subject hoặc Observer có thể dễ dàng ngừng nhận thông báo từ Subject bằng cách hủy đăng ký, giúp kiểm soát tốt hơn việc quản lý tài nguyên và sự kiện trong hệ thống.

4. **Giảm sự phụ thuộc chặt chẽ**:

- Subject không cần biết chính xác về các Observer mà nó quản lý, chỉ cần biết rằng chúng tuân theo một giao diện chung để nhận thông báo. Điều này giúp giảm sự phụ thuộc chặt chẽ giữa các đối tượng và làm cho mã dễ bảo trì hơn.

5. **Nhiều Observer có thể theo dõi một hoặc nhiều Subject**:

- Nhiều Observer có thể cùng theo dõi một Subject. Điều này cho phép cùng một sự kiện trong Subject có thể ảnh hưởng đến nhiều đối tượng khác nhau.
- Một Observer có thể đăng ký để nhận thông báo từ nhiều Subject khác nhau, và mỗi Subject sẽ thông báo cho Observer khi có sự thay đổi liên quan.

<br>

💻
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

// Interface for observers (display, logger, etc.)
class Observer{
    public:
        virtual void update(float temperature, float humidity, float light) = 0;
};

// Subject (SensorManager) holds the state and notifies observers
class SensorManager{
        float temperature;
        float humidity;
        float light;
        vector<Observer*> observers;

    public:
        void registerObserver(Observer* observer){
            observers.push_back(observer);
        }

        void removeObserver(Observer* observer){
            observers.erase(std::remove(observers.begin(), observers.end(), observer), observers.end());
        }

        void notifyObservers(){
            for (auto observer : observers){
                observer->update(temperature, humidity, light);
            }
        }

        void setMeasurements(float temp, float hum, float lightLvl) {
            temperature = temp;
            humidity = hum;
            light = lightLvl;
            notifyObservers();  // Push notification to all registered observers
        }
};

// Another Subject class
class WeatherStation{
        float windSpeed;
        vector<Observer*> observers;

    public:
        void registerObserver(Observer* observer){
            observers.push_back(observer);
        }

        void removeObserver(Observer* observer){
            observers.erase(std::remove(observers.begin(), observers.end(), observer), observers.end());
        }

        void notifyObservers(){
            for (auto observer : observers){
                observer->update(windSpeed, 0.0, 0.0);  // update for wind speed only
            }
        }

        void setWindSpeed(float speed){
            windSpeed = speed;
            notifyObservers();  // Notify all observers of wind speed change
        }
};

// Display component (an observer)
class Display : public Observer {
    public:
        void update(float temperature, float humidity, float light) override {
            cout << "Display: Temperature: " << temperature 
                 << ", Humidity: " << humidity 
                 << ", Light: " << light << endl;
        }
};

// Logger component (an observer)
class Logger : public Observer {
    public:
        void update(float temperature, float humidity, float light) override {
            cout << "Logging data... Temp: " << temperature 
                 << ", Humidity: " << humidity 
                 << ", Light: " << light << endl;
        }
};

int main() 
{
    Display display;
    Logger  logger;

    SensorManager  sensorManager;
    WeatherStation weatherStation;

    // register observers to SensorManager
    sensorManager.registerObserver(&display);
    sensorManager.registerObserver(&logger);

    // register observers to WeatherManager
    weatherStation.registerObserver(&display);
    weatherStation.registerObserver(&logger);

    // simulate sensor data update
    sensorManager.setMeasurements(25.0, 60.0, 700.0);
    sensorManager.setMeasurements(26.0, 65.0, 800.0);
    cout << "\n";

    // simulate wind speed update
    weatherStation.setWindSpeed(15.0);
    weatherStation.setWindSpeed(25.4);
    cout << "\n";

    // Remove Logger from SensorManager notifications
    sensorManager.removeObserver(&logger);

    // Update sensor data again to see only Display receiving notifications
    sensorManager.setMeasurements(27.0, 70.0, 900.0);

    return 0;
}
```
**Kết quả**:
```cpp
Display: Temperature: 25, Humidity: 60, Light: 700
Logging data... Temp: 25, Humidity: 60, Light: 700
Display: Temperature: 26, Humidity: 65, Light: 800
Logging data... Temp: 26, Humidity: 65, Light: 800

Display: Temperature: 15, Humidity: 0, Light: 0
Logging data... Temp: 15, Humidity: 0, Light: 0
Display: Temperature: 25.4, Humidity: 0, Light: 0
Logging data... Temp: 25.4, Humidity: 0, Light: 0

Display: Temperature: 27, Humidity: 70, Light: 900
```
📝 Các property ``` temperature, humidity, light ``` và ``` vector<Observer*> observers ``` mặc định nằm ở pham vi truy cập private.

📝 **SensorManager** và **WeatherStation** (Subject) quản lý danh sách các **Observer**. Các Observer có thể đăng ký thông qua phương thức ``` registerObserver() ``` và có thể bị xóa bằng ``` removeObserver() ```. Điều này đảm bảo việc quản lý danh sách Observer một cách linh hoạt.

📝 Khi trạng thái thay đổi trong SensorManager (ví dụ: nhiệt độ, độ ẩm, ánh sáng), tất cả các Observer được thông báo qua phương thức ``` notifyObservers() ``` mà không cần chúng chủ động kiểm tra.

📝 Bất kỳ Observer nào cũng có thể dễ dàng được thêm hoặc xóa khỏi Subject mà không cần thay đổi cấu trúc của Subject hoặc Observer. Ví dụ: Sau khi Logger được xóa khỏi danh sách Observer của SensorManager, chỉ có Display nhận được thông báo khi có sự thay đổi.

📝 SensorManager không cần biết chi tiết về Display hay Logger, chỉ cần biết rằng chúng tuân theo giao diện Observer (với việc override method ``` update() ```). Điều này giúp giảm sự phụ thuộc chặt chẽ giữa các đối tượng và tăng khả năng bảo trì.

📝 Display và Logger theo dõi cả SensorManager và WeatherStation. Khi bất kỳ trạng thái nào trong hai Subject này thay đổi, các Observer sẽ nhận được thông báo tương ứng.

<br>

💻
```cpp
#include <iostream>
#include <vector>

using namespace std;

// Observer interface
class ButtonObserver{
    public:
        virtual void update(int buttonID, bool state) = 0;
};

// Subject class - Button
class Button{
        int buttonID;
        bool state;
        vector<ButtonObserver*> observers;

    public:
        Button(int id): buttonID(id), state(false){}

        void registerObserver(ButtonObserver* observer){
            observers.push_back(observer);
        }

        void notifyObservers(){
            for (auto observer : observers){
                observer->update(buttonID, state);
            }
        }

        void pressButton(){
            state = true;
            notifyObservers();
        }

        void releaseButton(){
            state = false;
            notifyObservers();
        }
};

// LED device acting as an observer
class LED : public ButtonObserver{
    public:
        void update(int buttonID, bool state) override {
            cout << "LED reacts to Button " << buttonID << " being " << (state ? "pressed" : "released") << std::endl;
        }
};

// Buzzer device acting as an observer
class Buzzer : public ButtonObserver{
    public:
        void update(int buttonID, bool state) override {
            cout << "Buzzer reacts to Button " << buttonID << " being " << (state ? "pressed" : "released") << std::endl;
        }
};

int main() 
{
    Button button1(1);
    Button button2(2);

    Buzzer buzzer;
    LED    led;

    // Register observers
    button1.registerObserver(&led);
    button1.registerObserver(&buzzer);

    button2.registerObserver(&led);

    // Simulate button presses
    button1.pressButton();
    button1.releaseButton();
    cout << "\n";

    button2.pressButton();
    button2.releaseButton();

    return 0;
}
```
**Kết quả**:
```cpp
LED reacts to Button 1 being pressed
Buzzer reacts to Button 1 being pressed
LED reacts to Button 1 being released
Buzzer reacts to Button 1 being released

LED reacts to Button 2 being pressed
LED reacts to Button 2 being released
```

**Ứng dụng**:

- Giao diện đồ họa (cập nhật giao diện khi dữ liệu thay đổi).
- Hệ thống thông báo (quản lý sự kiện, hệ thống báo lỗi).
- Trong Automotive, Observer có thể được dùng để phát hiện và thông báo các thay đổi trạng thái của sensor hoặc các mô-đun điều khiển.

<br>

</p>
</details>

<details><summary><b>10.4. Factory Pattern</b></summary>
<p>

Factory Pattern là một mẫu thiết kế (design pattern) thuộc nhóm creational patterns, cung cấp một cơ chế để tạo ra các đối tượng mà không cần chỉ rõ lớp cụ thể của các đối tượng đó. Thay vì khởi tạo trực tiếp các đối tượng, Factory Pattern sử dụng một phương thức hoặc một lớp trung gian (Factory) để quyết định loại đối tượng nào sẽ được khởi tạo dựa trên tham số đầu vào hoặc logic cụ thể.

Đặc điểm của Factory Pattern:

- **Tính trừu tượng**: Factory Pattern ẩn đi chi tiết về cách các đối tượng được tạo ra, giúp chương trình tách biệt giữa việc khởi tạo đối tượng và việc sử dụng đối tượng đó.
- **Tính mở rộng**: Factory Pattern giúp hệ thống dễ dàng mở rộng khi cần thêm các lớp con mới mà không làm ảnh hưởng đến mã nguồn hiện có.
- **Tính linh hoạt**: Khi hệ thống cần thay đổi hoặc thêm mới các đối tượng cụ thể, chúng ta chỉ cần cập nhật factory mà không cần sửa đổi mã nguồn chính.
- **Giảm sự phụ thuộc**: Factory Pattern giúp mã nguồn giảm sự phụ thuộc vào các lớp cụ thể, từ đó tăng tính module và khả năng tái sử dụng.

<br>

💻
```cpp
#include <iostream>
#include <string>

using namespace std;

// Abstract class
class Sensor{
    public:
        virtual void readData() = 0;
};

// Temperature class
class TemperatureSensor : public Sensor{
    public:
        void readData() override {
            cout<<"reading temp data: "<<endl;
        }
};

// Humidity class
class HumiditySensor : public Sensor{
    public:
        void readData() override {
            cout<<"reading humidity data: "<<endl;
        }
};

// Factory class (Creator)
class SensorFactory{
    public:
        static Sensor* createSensor(const string& sensorType){
            if(sensorType == "temp"){
                return new TemperatureSensor();     // trả về đối tượng TemperatureSensor
            }
            else if (sensorType == "humi"){
                return new HumiditySensor();        // trả về đối tượng PressureSensor
            }
            else{
                return nullptr;                     // trả về con trỏ null
            }
        }
};


int main(int argc, char const *argv[])
{
    Sensor* sensor = SensorFactory::createSensor("humi");
    sensor->readData();
    return 0;
}
```

📝 **Abstract class** ``` Sensor ```: Đây là lớp cơ sở trừu tượng cho tất cả các loại cảm biến. Lớp này định nghĩa phương thức readData() là phương thức ảo thuần túy (pure virtual), yêu cầu các lớp con phải triển khai nó.

📝  Các lớp con cụ thể như ``` TemperatureSensor ```, ``` PressureSensor ```, và ``` HumiditySensor ``` triển khai phương thức ``` readData() ``` theo cách riêng để đọc dữ liệu từ các cảm biến tương ứng.

📝 **Factory class** ``` SensorFactory ```: Lớp này chứa một phương thức tĩnh ``` createSensor() ``` để tạo và trả về đối tượng cảm biến dựa trên loại cảm biến được chỉ định qua chuỗi ký tự đầu vào. Đây chính là tính trừu tượng vì người dùng chỉ được phép truyền vào tên cảm biến còn quá trình triển khai cảm biến như thế nào đã bị ẩn đi.

📝 Trong hàm main(), ta chỉ cần gọi phương thức ``` SensorFactory::createSensor() ``` và cung cấp loại cảm biến mà ta cần. Factory sẽ trả về đối tượng cảm biến phù hợp mà không cần khởi tạo thủ công từng lớp cụ thể

<br>

Ta có thể thay thế ``` class SensorFactory ``` như sau:

```cpp
enum class SensorType {
    TEMPERATURE,
    PRESSURE,
    HUMIDITY
};

class SensorFactory {
public:
    // Factory method sử dụng enum
    static Sensor* createSensor(SensorType type) {
        switch (type) {
            case SensorType::TEMPERATURE:
                return new TemperatureSensor();  // Trả về đối tượng TemperatureSensor
            case SensorType::PRESSURE:
                return new PressureSensor();     // Trả về đối tượng PressureSensor
            case SensorType::HUMIDITY:
                return new HumiditySensor();     // Trả về đối tượng HumiditySensor
            default:
                std::cout << "Invalid sensor type!\n";
                return nullptr;                  // Trường hợp không hợp lệ
        }
    }
};

int main() 
{
    Sensor* sensor = SensorFactory::createSensor(SensorType::PRESSURE);

    if (sensor) {
        sensor->readData(); // Interact with the sensor
        delete sensor;      // Clean up memory
    }

    return 0;
}
```
📝 Enum ``` SensorType ```: sử dụng một enum để đại diện cho các loại cảm biến khác nhau (Temperature, Pressure, Humidity). Điều này giúp giảm thiểu lỗi liên quan đến việc nhập sai chuỗi (string) và làm mã dễ bảo trì hơn.

📝 **Factory Method** ``` createSensor() ```: method này giờ đây nhận tham số kiểu SensorType (enum) thay vì một chuỗi. Dựa trên giá trị của enum, nó khởi tạo và trả về đối tượng cảm biến tương ứng.

📝 Trong hàm ``` main() ```, thay vì sử dụng chuỗi để chỉ định loại cảm biến, ta truyền vào một giá trị của SensorType enum, giúp mã dễ hiểu và tránh lỗi không mong muốn.

<br>

</p>
</details>

<details><summary><b>10.5. Decorator Pattern</b></summary>
<p>

Decorator Pattern là một mẫu thiết kế thuộc nhóm structural patterns, cho phép thêm các chức năng hoặc hành vi mới cho một đối tượng mà không cần thay đổi cấu trúc của lớp đối tượng đó. Thay vì kế thừa để mở rộng tính năng, Decorator sử dụng sự kết hợp của đối tượng để thêm các hành vi cho đối tượng hiện có.

Đặc điểm của Decorator Pattern:

- **Tính linh hoạt**: Decorator Pattern cho phép thêm hành vi mới vào đối tượng một cách linh hoạt mà không làm thay đổi các đối tượng khác.
- **Tính mở rộng**: Decorator Pattern cho phép mở rộng tính năng mà không cần thay đổi mã gốc.
- **Dễ bảo trì**: Bạn có thể dễ dàng bổ sung hoặc thay thế các tính năng bằng cách thay đổi các decorator mà không ảnh hưởng đến các lớp khác.
- **Giảm sự phức tạp của kế thừa**: Thay vì tạo ra nhiều lớp con để mở rộng hành vi, Decorator Pattern cho phép kết hợp các hành vi một cách linh hoạt bằng cách xếp chồng các decorator.

<br>

💻
```cpp
#include <iostream>

using namespace std;

// Base component
class Sensor{
    public:
        virtual void readData() = 0;
};
/*
 * lớp cơ sở (abstract base class) dùng để định nghĩa một giao diện chung cho các loại sensor khác nhau.
 * Sensor có một phương thức thuần ảo (pure virtual function) tên là readData(). Phương thức này yêu cầu các lớp kế thừa từ Sensor phải cài đặt nó.
 * Mục đích của lớp Sensor là để đại diện cho các loại cảm biến khác nhau trong hệ thống, có thể là cảm biến nhiệt độ, độ ẩm, áp suất,...
 */


// Concrete component: temperature class
class TemperatureSensor : public Sensor{
    public:
        void readData() override {
            cout<<"Reading temperature data...\n";
        }
};
/*
 * Lớp TemperatureSensor kế thừa từ lớp Sensor và cài đặt phương thức readData().
 * Phương thức readData() trong lớp này sẽ in ra thông báo "Reading temperature data...". Đây là lớp cụ thể đại diện cho cảm biến nhiệt độ, thực hiện hành động đọc dữ liệu nhiệt độ.
 */


// Base Decorator
class SensorDecorator : public Sensor{
    protected:
        Sensor* wrappedSensor;

    public:
        SensorDecorator(Sensor* sensor) : wrappedSensor(sensor){}

        virtual void readData() override {
            wrappedSensor->readData();
        }
};
/*
 * Mục đích của lớp này là cung cấp khả năng mở rộng chức năng cho các lớp cảm biến mà không thay đổi cấu trúc ban đầu của chúng.
 * Thuộc tính Sensor* wrappedSensor được sử dụng để giữ tham chiếu đến đối tượng Sensor gốc.
 * Phương thức readData() được định nghĩa để gọi lại phương thức readData() của wrappedSensor, cho phép các lớp con kế thừa lớp SensorDecorator có thể thêm các chức năng bổ sung mà không can thiệp vào logic cơ bản.
 */


// Concrete Decorator: add logging function
class LoggingSensor : public SensorDecorator{
    public:
        LoggingSensor(Sensor* sensor) : SensorDecorator(sensor){}

        void readData() override{
            SensorDecorator::readData();
            logData();
        }

        void logData(){
            cout << "Logging temperature data...\n";
            // code to log sensor data
        }
};
/*
 * Mục đích của lớp này là cung cấp khả năng mở rộng chức năng cho các lớp cảm biến mà không thay đổi cấu trúc ban đầu của chúng.
 * Thuộc tính Sensor* wrappedSensor được sử dụng để giữ tham chiếu đến đối tượng Sensor gốc.
 * Phương thức readData() được định nghĩa để gọi lại phương thức readData() của wrappedSensor, cho phép các lớp con kế thừa lớp SensorDecorator có thể thêm các chức năng bổ sung mà không can thiệp vào logic cơ bản.
 */


// Concrete Decorator: add limit check function
class LimitCheckSensor : public SensorDecorator{
    public:
        LimitCheckSensor(Sensor* sensor) : SensorDecorator(sensor){}

        void readData() override{
            SensorDecorator::readData();
            checkLimits();
        }

        void checkLimits(){
            cout << "Checking temperature limits...\n";
            // code to check temperature limits
        }
};


int main(int argc, char const *argv[])
{
    // Base sensor object
    Sensor* tempSensor = new TemperatureSensor();	// 0xc8

    // Adding logging function via decorator
    tempSensor = new LoggingSensor(tempSensor);		// 0xa1

    // // Adding limit checking function via decorator
    tempSensor = new LimitCheckSensor(tempSensor);	// 0xf4

    // // Reading data with all added function
    tempSensor->readData();

    delete tempSensor;

    return 0;
}
```

📝 **Base Component** ``` Sensor ```: Đây là giao diện trừu tượng của cảm biến, định nghĩa phương thức readData() mà mọi lớp con phải thực hiện.

📝 **Concrete Component** ``` TemperatureSensor ```: class cảm biến nhiệt độ, thực hiện việc đọc dữ liệu từ cảm biến thực tế.

📝 **Base Decorator** ``` SensorDecorator ```: Đây là lớp trừu tượng cho các decorator, kế thừa từ Sensor và chứa một tham chiếu đến Sensor. Lớp này cho phép các lớp con mở rộng hành vi của đối tượng Sensor được gói bên trong nó.

📝 **Concrete Decorator** ``` LoggingSensorDecorator ``` và ``` LimitCheckSensorDecorator ```: Đây là các lớp decorator cụ thể, chúng mở rộng hành vi của cảm biến bằng cách thêm chức năng ghi log (LoggingSensorDecorator) và kiểm tra giới hạn (LimitCheckSensorDecorator). Mỗi decorator vẫn gọi hàm readData() của cảm biến gốc, nhưng thêm các hành vi bổ sung vào quá trình.

<br>

</p>
</details>

<details><summary><b>10.6. MVP Pattern</b></summary>
<p>

</p>
</details>

</p>
</details>

<br>

<details><summary><b>11. Smart Pointer</b></summary>
<p>

<details><summary><b>11.1. Unique pointer</b></summary>
<p>

- unique pointer là một smart pointer quản lý bộ nhớ tự động trong C++.
- unique pointer đảm bảo rằng chỉ có duy nhất một con trỏ sở hữu đối tượng tại một thời điểm (quyền sở hữu độc quyền).
- Khi unique pointer bị hủy hoặc được gán cho một con trỏ khác, đối tượng mà nó sở hữu sẽ tự động được giải phóng.

💻
```cpp
#include <iostream>
#include <memory>  // Cần thiết để sử dụng smart pointer

using namespace std;

int main() 
{
    unique_ptr<int> ptr = make_unique<int>(10);
    
    cout << "Value: " << *ptr << endl;

    return 0;
}
```

📝 ``` make_unique<int>(10) ```: tạo ra một đối tượng kiểu int với giá trị khởi tạo là 10 và địa chỉ được cấp phát nằm trên vùng nhớ heap, sau đó trả về một con trỏ thông minh ``` unique_ptr ``` trỏ tới đối tượng đó.

📝 ``` unique_ptr<int> ```: chỉ ra rằng unique pointer này sẽ quản lý một đối tượng kiểu **int**.

📝 ``` ptr ```: tên của object thuộc class unique_ptr, nó được tạo ra và sở hữu vùng nhớ vừa được cấp phát. Từ thời điểm này, ptr có trách nhiệm quản lý vùng nhớ của đối tượng kiểu int.

<br>

Những phương thức chính mà ``` std::unique_ptr ``` hỗ trợ:

<b>1. operator*() và operator->()**</b>

- ``` operator*() ```: dereference con trỏ để truy cập giá trị của đối tượng mà **unique_ptr** đang quản lý.
- ``` operator->() ```: truy cập thành viên của đối tượng mà **unique_ptr** trỏ tới.


💻
```cpp
#include <iostream>
#include <memory>  // Cần thiết để sử dụng smart pointer

using namespace std;

class Example{
    private:
        int data;

    public:
        Example(int value): data(value){
            cout << "Constructor called, data = " << data << endl;
        }

        ~Example(){
            cout << "Destructor called, data = " << data << endl;
        }

        void display(){
            cout << "Value: " << data << endl;
        }
};

int main() 
{
    unique_ptr<Example> ptr = make_unique<Example>(10);
    ptr->display();

    *ptr = 20;
    (*ptr).display();
    return 0;
}
```

<br>

<b>2. get()</b>

- Trả về con trỏ thô (raw pointer) đến đối tượng mà **unique_ptr** đang quản lý.
-  Con trỏ thô trả về bởi **get()** không chuyển quyền sở hữu, tức là đối tượng vẫn được quản lý bởi **unique_ptr**.

💻
```cpp
#include <iostream>
#include <memory>  // Cần thiết để sử dụng smart pointer

using namespace std;

class Example{
    private:
        int data;

    public:
        Example(int value): data(value){
            cout << "Constructor called, data = " << data << endl;
        }

        ~Example(){
            cout << "Destructor called, data = " << data << endl;
        }

        void display(){
            cout << "Value: " << data << endl;
        }
};

int main() 
{
    unique_ptr<Example> ptr;
    ptr = make_unique<Example>(30);

    Example *rawPtr2 = ptr.get();   
    rawPtr2->display();
    return 0;
}
```

<br>

<b>3. release()</b>

- Giải phóng quyền sở hữu đối với đối tượng mà unique_ptr đang quản lý (``` unique_ptr ``` giờ đây trở thành ``` con trỏ null ```(nullptr)) và trả về con trỏ thô (raw pointer) đến đối tượng đó.
- Sau khi gọi ``` release() ```, unique_ptr không còn quản lý đối tượng nữa, và trách nhiệm giải phóng bộ nhớ thuộc về người gọi (giải phóng thủ công với ``` delete ```).

💻
```cpp
#include <iostream>
#include <memory>  // Cần thiết để sử dụng smart pointer

using namespace std;

class Example{
    private:
        int data;

    public:
        Example(int value): data(value){
            cout << "Constructor called, data = " << data << endl;
        }

        ~Example(){
            cout << "Destructor called, data = " << data << endl;
        }

        void display(){
            cout << "Value: " << data << endl;
        }
};

int main() 
{
    unique_ptr<Example> ptr = make_unique<Example>(30);

    Example *rawPtr = ptr.release();
    if (!ptr){
        cout << "ptr đã bị tách quyền sở hữu và trở thành nullptr\n";
    }
    rawPtr->display();  
    delete rawPtr;      
    return 0;
}
```

<br>

<b>4. reset()</b>

- Giải phóng đối tượng mà unique_ptr đang quản lý (nếu có) và có thể quản lý một đối tượng mới (nếu được cung cấp).
- Nếu không cung cấp đối tượng mới, reset() sẽ khiến unique_ptr trở thành nullptr.

💻
```cpp
#include <iostream>
#include <memory>  // Cần thiết để sử dụng smart pointer

using namespace std;

class Example{
    private:
        int data;

    public:
        Example(int value): data(value){
            cout << "Constructor called, data = " << data << endl;
        }

        ~Example(){
            cout << "Destructor called, data = " << data << endl;
        }

        void display(){
            cout << "Value: " << data << endl;
        }
};

int main() 
{
    unique_ptr<Example> ptr = make_unique<Example>(30);

    ptr.reset(new Example(20)); // ptr quản lý đối tượng mới
    ptr->display();

    ptr.reset();
    if (!ptr){
           cout << "ptr trở thành nullptr\n";
    }
    return 0;
}
```

<br>

<b>5. swap()</b>

Hoán đổi (swap) nội dung của hai unique_ptr với nhau, tức là đổi đối tượng mà hai unique_ptr quản lý.

💻
```cpp
#include <iostream>
#include <memory>  // Cần thiết để sử dụng smart pointer

using namespace std;

class Example{
    private:
        int data;

    public:
        Example(int value): data(value){
            cout << "Constructor called, data = " << data << endl;
        }

        ~Example(){
            cout << "Destructor called, data = " << data << endl;
        }

        void display(){
            cout << "Value: " << data << endl;
        }
};

int main() 
{
    unique_ptr<Example> ptr1 = make_unique<Example>(30);
    unique_ptr<Example> ptr2 = make_unique<Example>(60);
    ptr1.swap(ptr2);

    ptr1->display();
    ptr2->display();
    return 0;
}
```

<br>

<b>6. move()</b>

Chuyển nhượng quyền sở hữu tài nguyên (move semantics). Sau khi chuyển nhượng (move), con trỏ gốc sẽ trở thành nullptr và quyền sở hữu tài nguyên được chuyển sang **unique_ptr** mới.

💻
```cpp
#include <iostream>
#include <memory>  // Cần thiết để sử dụng smart pointer

using namespace std;

class Example{
    private:
        int data;

    public:
        Example(int value): data(value){
            cout << "Constructor called, data = " << data << endl;
        }

        ~Example(){
            cout << "Destructor called, data = " << data << endl;
        }

        void display(){
            cout << "Value: " << data << endl;
        }
};

int main() 
{
    unique_ptr<Example> ptr1 = make_unique<Example>(30);
    unique_ptr<Example> newPtr = move(ptr); // chuyển quyền sở hữu từ ptr sang newPtr

    if (!ptr){
        cout << "ptr đã trở thành nullptr sau khi chuyển quyền sở hữu\n";
    }
    newPtr->display();  // newPtr giờ sở hữu đối tượng
    return 0;
}
```

<br>

</p>
</details>

<details><summary><b>11.2. Shared pointer</b></summary>
<p>

- shared pointer (shared_ptr) là một smart pointer hỗ trợ chia sẻ quyền sở hữu đối với một đối tượng.
- Nhiều shared_ptr có thể cùng sở hữu một đối tượng. Đối tượng chỉ được giải phóng khi không còn shared_ptr nào sở hữu nó (đếm tham chiếu đạt giá trị 0).

<br>

Những phương thức chính mà ``` std::shared_ptr ``` hỗ trợ:

<b>1. operator*() và operator->()</b>: tương tự ``` unique_ptr ```

<b>2. get()</b>: tương tự ``` unique_ptr ```

<b>3. reset()</b>: tương tự ``` unique_ptr ```

<b>4. swap()</b>: tương tự ``` unique_ptr ```

<b>5. operator=</b>

Sao chép hoặc di chuyển quyền sở hữu giữa các ``` shared_ptr ```.

<b>6. use_count()</b>

Trả về số lượng shared_ptr đang cùng quản lý đối tượng (bộ đếm tham chiếu).

<br>

💻
```cpp
#include <iostream>
#include <memory>

using namespace std;

int main(int argc, char const *argv[])
{
    shared_ptr<int> ptr1 = make_shared<int>(20); // 0x01
    shared_ptr<int> ptr2 = ptr1;
    shared_ptr<int> ptr3 = ptr1;

    {
        shared_ptr<int> ptr4 = ptr1;
        cout << "Count: " << ptr1.use_count() << endl;
    }

    cout << "ptr1 = " << *ptr1 << endl;
    cout << "ptr2 = " << *ptr2 << endl;
    cout << "ptr3 = " << *ptr3 << endl;

    cout << "Count: " << ptr1.use_count() << endl;
    
    int *ptr = ptr1.get();
    cout << "ptr: " << *ptr << endl;
    *ptr = 50;
    cout << "ptr: " << *ptr << endl;
    /*
     * trả về 1 con trỏ int
     */

    shared_ptr<int> a = make_shared<int>(40);   // 0xa1
    shared_ptr<int> b = make_shared<int>(50);   // 0xb3
    a.swap(b);
    cout << "a = " << *a << endl;
    cout << "b = " << *b << endl;
    
    return 0;
}
```
**Kết quả:**
```cpp
Count: 4
ptr1 = 20
ptr2 = 20
ptr3 = 20
Count: 3
ptr: 20
ptr: 50
a = 50
b = 40
```

<br>

</p>
</details>

<details><summary><b>11.3. Weak pointer</b></summary>
<p>

- weak pointer là một smart pointer yếu (non-owning), không sở hữu đối tượng mà nó trỏ tới. Nó chỉ đóng vai trò theo dõi đối tượng được quản lý bởi một **shared_ptr** mà không làm tăng bộ đếm tham chiếu.

<br>

Những phương thức chính mà ``` std::shared_ptr ``` hỗ trợ:

<b>1. use_count()</b>: tương tự ``` shared_ptr ```

<b>2. reset()</b>: tương tự ``` shared_ptr ``` và ``` unique_ptr ```

<b>3. swap()</b>: tương tự ``` shared_ptr ``` và ``` unique_ptr ```

<b>4. operator=</b>

- Gán một ``` shared_ptr ``` hoặc một ``` weak_ptr ``` khác cho ``` weak_ptr ```.
- Khi gán một shared_ptr cho một weak_ptr sẽ không làm tăng bộ đếm tham chiếu.

<b>5. lock()</b>

Trả về shared_ptr trỏ tới đối tượng mà weak_ptr theo dõi, hoặc null nếu đối tượng đã bị hủy.

<b>6. expired()</b>

Trả về true nếu đối tượng mà weak_ptr theo dõi đã bị hủy.

<br>

💻
```cpp
#include <iostream>
#include <memory>

using namespace std;

int main(int argc, char const *argv[])
{
    // Tạo hai shared_ptr cùng quản lý một đối tượng int
    shared_ptr<int> ptr1 = make_shared<int>(20); // 0x01
    shared_ptr<int> ptr2 = ptr1;

    // Tạo một weak_ptr trỏ tới đối tượng được quản lý bởi shared_ptr
    weak_ptr<int> ptr3 = ptr1;

    cout << "Count: " << ptr1.use_count() << endl;

    cout << "ptr3: " << *(ptr3.lock()) << endl;

    ptr1.reset();   // giải phóng quyền sở hữu của ptr1 đối với đối tượng tại địa chỉ 0x01

    cout << "check: " << ptr3.expired() << endl;
    /*
     * nếu 0x01 còn tồn tại thì trả về false
     * ngược lại thì trả về true
     */

    ptr2.reset();
    cout << "check: " << ptr3.expired() << endl;
    
    return 0;
}
```
**Kết quả:**
```cpp
Count: 2
ptr2: 20
check: 0
check: 1
```

<br>

</p>
</details>

</p>
</details>

<br>

<details><summary><b>12. Đa luồng (Thread)</b></summary>
<p>

<details><summary><b>12.1. Process</b></summary>
<p>

- Process (tiến trình) là một đơn vị thực thi độc lập, có không gian địa chỉ bộ nhớ riêng. Mỗi process chạy trong một không gian bộ nhớ tách biệt, và việc giao tiếp giữa các process thường khó khăn và yêu cầu các phương thức như IPC (Inter-Process Communication).
- Bản chất Process là 1 chương trình đang chạy trên hệ thống hoặc một tập hợp các công việc được thực hiện trên máy tính, có phân vùng RAM riêng, có thể sử dụng những phần cứng bên dưới (bàn phím, chuột, ...). Vì mỗi chương trình có vùng RAM riêng các chương trình không thể truy cập vùng RAM lẫn nhau.
- Mỗi tiến trình có thể bao gồm một hoặc nhiều luồng (thread) của việc thực hiện công việc.

</p>
</details>

<details><summary><b>12.2. Thread</b></summary>
<p>

<details><summary><b>📚 Khái niệm</b></summary>
<p>

- Thread là đơn vị nhỏ hơn của một process, còn được gọi là "luồng". Một process có thể chứa nhiều thread, và các thread này chia sẻ cùng không gian địa chỉ bộ nhớ của process đó.
- Ví dụ: mở hai chương trình khác nhau trên máy tính, ví dụ như một trình duyệt web và một trình soạn thảo văn bản. Mỗi chương trình là một process riêng biệt, có không gian bộ nhớ và tài nguyên hệ thống độc lập.
- Các luồng trong cùng một tiến trình có thể chia sẻ cùng một không gian bộ nhớ và các tài nguyên khác của tiến trình, bao gồm cả biến toàn cục và biến cục bộ.

</p>
</details>

<details><summary><b>📚 Tạo và khởi chạy một thread</b></summary>
<p>

```cpp
    thread task1(test1, 1000);     // task1: đại diện cho luồng 1
    thread task2(test2, 3000);     // task2: đại diện cho luồng 2
```

- task1, task2: là các object thuộc class thread. Mỗi object sẽ tương ứng với một luồng được tạo ra và mỗi một luồng sẽ thực thi một hàm hay tác vụ nào đó.
- Tham số 1: địa chỉ hàm muốn thực thi trong luồng.
- Tham số 2, 3, …: tham số truyền vào của hàm.  

<br>

💻 **Ví dụ:**
```cpp
#include <iostream>
#include <thread>
#include <chrono>

using namespace std;

void delay(uint32_t time){
    this_thread::sleep_for(chrono::seconds(time));
    /**************************************************************************
     * Hàm này mô phỏng một tác vụ tạm dừng (delay) trong khoảng thời gian time (ms).
     * std::this_thread::sleep_for() tạm dừng luồng hiện tại trong thời gian được chỉ định.
     * Ví dụ, nếu time = 1000, luồng sẽ bị tạm dừng trong 1 giây.
     *************************************************************************/
}

void test1(uint32_t time){
    int i = 0;
    while (1){
        cout << "task 1, i = " << i++ << endl;
        delay(time);
    }
}

void test2(uint32_t time){
    int i = 0;
    while (1){
        cout << "task 2, i = " << i++ << endl;
        delay(time);
    }
}

int main(int argc, char const *argv[])
{
    thread task1(test1, 1);     // t1: đại diện cho luồng 1
    thread task2(test2, 2);     // t2: đại diện cho luồng 2

    /* luồng chính giúp chương trình chạy liên tục */
    while(1){
        cout << "This is main\n";
        delay(1);
    }

    return 0;
}
```

</p>
</details>

<details><summary><b>📚Quản lý thread</b></summary>
<p>

<details><summary><b>📚📚 join()</b></summary>
<p>

- Phương thức join() được sử dụng để chờ cho một luồng kết thúc. Khi gọi join() trên một luồng, nó đảm bảo rằng luồng hiện tại sẽ không tiếp tục thực hiện cho đến khi luồng được join() hoàn tất. Điều này hữu ích khi bạn cần chắc chắn rằng một luồng đã hoàn thành công việc trước khi tiếp tục với luồng chính.
- Mỗi luồng chỉ có thể gọi method join() duy nhất 1 lần.
- Nếu một luồng đã được join(), nó không còn khả năng tham gia (joinable) nữa.

<br>

💻 **Ví dụ:**
```cpp
#include <iostream>
#include <thread>
#include <chrono>

using namespace std;

void delay(uint32_t time){
    this_thread::sleep_for(chrono::seconds(time));
}

void test1(uint32_t time){
    int i = 0;
    for (int j = 0; j < 10; j++){
        cout << "task 1, i = " << i++ << endl;
        delay(time);
    }
}

void test2(uint32_t time){
    int i = 0;
    for (int j = 0; j < 10; j++){
        cout << "task 2, i = " << i++ << endl;
        delay(time);
    }
}

int main(int argc, char const *argv[])
{
    thread task1(test1, 1);     // t1: đại diện cho luồng 1
    thread task2(test2, 2);     // t2: đại diện cho luồng 2

    task1.join();
    task2.join();

    /* luồng chính giúp chương trình chạy liên tục */
    while(1){
        cout << "This is main\n";
        delay(1);
    }

    return 0;
}
```

📝 Đặt luồng chính (while(1)) sau khi gọi join().

📝 Luồng 1 (thực thi hàm test1) và luồng 2 (thực thi hàm test2) sẽ chạy song song với nhau.

📝 Luồng chính (while(1)) chỉ bắt đầu chạy khi luồng 1 kết thúc và luồng 2 cũng kết thúc.


</p>
</details>

<details><summary><b>📚📚 joinable()</b></summary>
<p>

- Kiểm tra xem một thread đã kết thúc hoặc đã gọi join() hay chưa. Nếu chưa thì trả về true, khi này ta có thể bắt đầu khởi chạy thread. Ngược lại, nếu thread đã kết thúc rồi thì trả về false.

💻 **Ví dụ:**
```cpp
#include <iostream>
#include <thread>
#include <chrono>

using namespace std;

void delay(uint32_t time){
    this_thread::sleep_for(chrono::seconds(time));
}

void test1(uint32_t time){
    int i = 0;
    for (int j = 0; j < 5; j++){
        cout << "task 1, i = " << i++ << endl;
        delay(time);
    }
}

void test2(uint32_t time){
    int i = 0;
    for (int j = 0; j < 5; j++){
        cout << "task 2, i = " << i++ << endl;
        delay(time);
    }
}

int main(int argc, char const *argv[])
{
    thread task1(test1, 1);     // t1: đại diện cho luồng 1
    thread task2(test2, 2);     // t2: đại diện cho luồng 2

    task1.join();

    if (task1.joinable()){
        cout << "Thread 1 chưa kết thúc\n";
    }
    else{
        cout << "Thread 1 đã kết thúc\n";
    }

    task2.join();

    if (task1.joinable()){
        cout << "Thread 2 chưa kết thúc\n";
    }
    else{
        cout << "Thread 2 đã kết thúc\n";
    }    

    /* luồng chính giúp chương trình chạy liên tục */
    while(1){
        cout << "This is main\n";
        delay(1);
    }

    return 0;
}
```

</p>
</details>

<details><summary><b>📚📚 detach()</b></summary>
<p>

- Phương thức detach() tách luồng khỏi luồng chính và cho phép nó chạy độc lập. Khi một luồng được tách ra, luồng chính không chờ luồng đó kết thúc nữa.
- Sau khi gọi detach(), khi luồng chính kết thúc, những luồng khác cũng sẽ kết thúc theo.
- Không thể join() một luồng đã được detach(). Nếu một luồng đã được tách ra, nó không còn khả năng tham gia (joinable) nữa.

💻 **Ví dụ:**
```cpp
#include <iostream>
#include <thread>
#include <chrono>

using namespace std;

void delay(uint32_t time){
    this_thread::sleep_for(chrono::seconds(time));
}

void test1(uint32_t time){
    int i = 0;
    for (int j = 0; j < 5; j++){
        cout << "task 1, i = " << i++ << endl;
        delay(time);
    }
}

void test2(uint32_t time){
    int i = 0;
    for (int j = 0; j < 5; j++){
        cout << "task 2, i = " << i++ << endl;
        delay(time);
    }
}

int main(int argc, char const *argv[])
{
    thread task1(test1, 1);     // t1: đại diện cho luồng 1
    thread task2(test2, 2);     // t2: đại diện cho luồng 2

    task1.detach();
    task2.detach();

    /* luồng chính giúp chương trình chạy liên tục */
    int i = 0;
    while(1){
        cout << "This is main, i = " << i++ << endl;
        delay(1);
    }

    return 0;
}
```

📝 Luồng task1, luồng task2 và luồng chính chạy song song và độc lập với nhau.

📝 Luồng chính không còn chờ luồng task1 và luồng task2 chạy xong rồi mới thực hiện.

</p>
</details>

</p>
</details>

</p>
</details>

<details><summary><b>12.3. Đồng bồ hóa các luồng</b></summary>
<p>

<details><summary><b>📚 Mutex</b></summary>
<p>

- Mutex (Mutual Exclusion): Là một đối tượng đồng bộ hóa cung cấp cơ chế để giới hạn truy cập tài nguyên chỉ cho một luồng tại một thời điểm. Khi một luồng khóa (lock) mutex, các luồng khác phải chờ cho đến khi mutex được mở khóa (unlock) mới có thể tiếp tục truy cập tài nguyên.
- Trạng thái của mutex: Một mutex có hai trạng thái chính:

+ Khóa (locked): Khi một luồng đã chiếm giữ mutex.
+ Mở khóa (unlocked): Khi không có luồng nào chiếm giữ mutex, các luồng khác có thể tiếp tục khóa nó.

- std::mutex được định nghĩa trong thư viện <mutex>. Các phương thức cơ bản bao gồm:

+ lock(): Khóa mutex. Nếu mutex đã bị khóa bởi một luồng khác, luồng hiện tại sẽ bị chặn (block) cho đến khi mutex được mở khóa.
+ unlock(): Mở khóa mutex. Điều này cho phép các luồng khác có thể tiếp tục khóa mutex.
+ try_lock(): Thử khóa mutex. Nếu mutex chưa bị khóa, nó sẽ khóa mutex và trả về true. Nếu đã bị khóa bởi một luồng khác, nó sẽ không chặn luồng hiện tại mà trả về false.

**Ví dụ 1:**

```cpp
#include <iostream>
#include <thread>
#include <mutex>

using namespace std;

int counter = 0;
mutex mtx;

void increment(int num_iterations) {
    for (int i = 0; i < num_iterations; ++i) {
        mtx.lock();     // Khóa mutex trước khi truy cập biến chia sẻ
        ++counter;      // Thao tác trên biến chia sẻ
        mtx.unlock();   // Mở khóa mutex sau khi truy cập
    }
}

int main() {
    const int num_iterations = 10;

    thread t1(increment, num_iterations);
    thread t2(increment, num_iterations);

    t1.join();
    t2.join();

    cout << "Final counter value: " << counter << endl;

    return 0;
}
```

<br>

- std::lock_guard là một lớp RAII (Resource Acquisition Is Initialization) tự động khóa mutex khi được tạo và tự động mở khóa khi đối tượng bị hủy (ra khỏi phạm vi).

**Ví dụ 2:**

```cpp
#include <iostream>
#include <thread>
#include <chrono>
#include <mutex>

using namespace std;

mutex mtx;

void delay(uint32_t time){
    this_thread::sleep_for(chrono::seconds(time));
}

void display(int id){
    while (1){
        lock_guard<mutex> lock(mtx);
        cout << "This is task display: " << id << endl;
        delay(1);
    }
}

int main(int argc, char const *argv[])
{
    thread t1(display, 1);
    thread t2(display, 2);
    thread t3(display, 3);

    t1.join();
    t2.join();
    t3.join();

    return 0;
}
```

<br>

- std::unique_lock tự động khóa mutex khi được tạo nhưng cung cấp tính linh hoạt hơn std::lock_guard, cho phép mở khóa thủ công hoặc tự động mở khóa khi ra khỏi phạm vi, hoặc thử khóa với thời gian chờ.

**Ví dụ 3:**

```cpp
#include <iostream>
#include <thread>
#include <chrono>
#include <mutex>

using namespace std;

mutex mtx;

bool lock_test = false;

void delay(uint32_t time){
    this_thread::sleep_for(chrono::seconds(time));
}

void display(int id){
    while (1){
        unique_lock<mutex> lock(mtx);   
        cout << "This is task display: " << id << endl;
        delay(1);
        lock.unlock();
    }
}

int main(int argc, char const *argv[])
{
    thread t1(display, 1);
    thread t2(display, 2);
    thread t3(display, 3);

    t1.join();
    t2.join();
    t3.join();
    return 0;
}
```

</p>
</details>

<details><summary><b>📚 Atomic operator</b></summary>
<p>

- std::atomic là một struct template trong C++ được thiết kế để thực hiện các thao tác trên các biến mà các thao tác này không thể bị gián đoạn bởi các luồng khác. Điều này giúp đảm bảo tính nhất quán của dữ liệu và tránh tình trạng race condition khi nhiều luồng cùng truy cập và thay đổi dữ liệu chia sẻ.
- Các thao tác atomic:

+ Gán và đọc giá trị (store và load): Ghi và đọc giá trị của biến atomic.
+ Tăng và giảm giá trị (++, --): Tăng hoặc giảm giá trị của biến atomic.
+ Cộng và trừ giá trị (+=, -=): Thực hiện phép cộng, trừ giá trị nguyên tử.

**Ví dụ:**

```cpp
#include <iostream>
#include <thread>
#include <chrono>
#include <mutex>
#include <atomic>

using namespace std;

mutex mtx;
bool lock_test = false;

atomic<int> counter(0);

void delay(uint32_t time){
    this_thread::sleep_for(chrono::seconds(time));
}

void display(int id){
    while (1){
        unique_lock<mutex> lock(mtx);   //lock_test = true;
        cout << "This is task display: " << id << endl;
        delay(1);
        lock.unlock();


        lock_guard<mutex> lock1(mtx);
        cout << "Counter: " << ++counter << endl;
        delay(1);
    }


}

/********************************************
 * atomic: dùng cho biến toàn cục
 * lock_guard, unique_lock: dùng cho ngoại vi
 *******************************************/

int main(int argc, char const *argv[])
{
    thread t1(display, 1);
    thread t2(display, 2);
    thread t3(display, 3);

    t1.join();
    t2.join();
    t3.join();

    return 0;
}
```

</p>
</details>

<details><summary><b>📚 Condition variable</b></summary>
<p>

- Khi hai hoặc nhiều luồng cần giao tiếp và đồng bộ hóa với nhau, condition variable là một lựa chọn thích hợp trong C++. 
std::condition_variable cho phép một luồng chờ cho đến khi một điều kiện cụ thể được đáp ứng, và một luồng khác có thể thông báo khi điều kiện đó được thỏa mãn. Điều này rất hữu ích trong việc giao tiếp và đồng bộ hóa dữ liệu giữa các luồng.
- Cách hoạt động của std::condition_variable:

a) Một luồng chờ (wait) cho đến khi có tín hiệu từ một luồng khác:

- Luồng này sẽ chờ trong trạng thái chờ điều kiện, tránh lãng phí tài nguyên CPU.
- Để sử dụng wait(), cần có một std::unique_lock<std::mutex> và một đối tượng std::condition_variable, vì nó phải tạm thời mở khóa mutex để các luồng khác có thể truy cập vào mutex và thay đổi điều kiện. Sau khi điều kiện được thỏa mãn và luồng được đánh thức, std::unique_lock sẽ tự động khóa lại mutex để đảm bảo tính nhất quán trước khi tiếp tục.
- Lưu ý: std::lock_guard không hỗ trợ mở khóa và khóa lại mutex như vậy. Khi một std::lock_guard được tạo, nó sẽ giữ mutex cho đến khi đối tượng bị hủy (khi ra khỏi phạm vi), do đó không phù hợp để sử dụng với wait.

b) Một luồng khác thông báo (notify_one() hoặc notify_all()) rằng điều kiện đã được đáp ứng:

- notify_one(): Chỉ đánh thức một luồng đang chờ.
- notify_all(): Đánh thức tất cả các luồng đang chờ.

<br>

**Ví dụ 1:**

```cpp

#include <iostream>
#include <thread>               // tạo ra các luồng
#include <mutex>                // khóa dữ liệu khi có nhiều luồng cùng truy cập
#include <condition_variable>   // sử dụng biến điều kiện giúp đồng bộ hóa giữa các luồng
#include <chrono>               // quản lý thời gian

using namespace std;

void delay(uint32_t time){
    this_thread::sleep_for(chrono::seconds(time));
}


/************************************************************************************************
 * biến toàn cục lưu trữ dữ liệu được đọc từ cảm biến, khởi tạo bằng 0
 ***********************************************************************************************/
int sensor_data = 0;


/************************************************************************************************
 * biến boolean dùng để kiểm tra xem dữ liệu đã được đọc hay chưa
 ***********************************************************************************************/
bool check_data = false;


/************************************************************************************************
 * mtx: 1 đối tượng khóa (mutex) dùng để bảo vệ tài nguyên chia sẻ (sensor_data và check_data) 
 * giữa các luồng, đảm bảo rằng không có hai luồng nào cùng lúc truy cập vào các tài nguyên này
 ***********************************************************************************************/
mutex mtx;


/************************************************************************************************
 * cv: object điều kiện (condition variable) giúp đồng bộ hóa giữa các luồng. Nó được sử dụng để 
 * thông báo cho các luồng khác biết khi nào có sự thay đổi về dữ liệu.
 ***********************************************************************************************/
condition_variable cv;


/************************************************************************************************
 * sensor_read: hàm thực thi trong 1 luồng riêng để mô phỏng quá trình đọc dữ liệu từ cảm biến
 * 
 * unique_lock<mutex> lock(mtx): 
 *      + khóa đối tượng mtx để bảo vệ tài nguyên chia sẻ
 *      + unique_lock giúp tự động giải phóng khóa khi ra khỏi phạm vi
 * 
 * sensor_data = rand() % 100: 
 *      + cập nhật giá trị của sensor_data với một số ngẫu nhiên từ 0 đến 99
 * 
 * check_data = true:
 *      + đánh dấu rằng dữ liệu đã được cập nhật và in ra màn hình 1 thông báo
 * 
 * lock.unlock(): 
 *      + Lệnh lock.unlock() được thực hiện trước khi gọi cv.notify_one() để  đảm bảo rằng mutex 
 *        sẽ được mở khóa trước khi cv.notify_one() được gọi
 *      + Giảm độ nghẽn: Bằng cách mở khóa mutex trước khi thông báo, sensor_read đảm bảo rằng 
 *        ngay sau khi gọi cv.notify_all(), các luồng khác có thể tiếp tục xử lý ngay lập tức
 * 
 * cv.notify_one(): 
 *      + thông báo cho một luồng khác đang chờ đợi rằng dữ liệu mới đã sẵn sàng để xử lý
 ***********************************************************************************************/
void sensor_read(){
    while (1){
        delay(2);
        unique_lock<mutex> lock(mtx);
        sensor_data = rand() % 100;
        check_data = true;
        cout << "Read data done!\n";
        lock.unlock();
        cv.notify_one();
    }
}


/************************************************************************************************
 * process_data: hàm thực thi trong 1 luồng riêng để xử lý dữ liệu từ cảm biến
 * 
 * unique_lock<mutex> lock(mtx): 
 *      + khóa đối tượng mtx để truy cập vào tài nguyên chia sẻ
 *      + unique_lock giúp tự động giải phóng khóa khi ra khỏi phạm vi
 * 
 * cv.wait(lock, []{return check_data;}): 
 *      + []{return check_data;}: con trỏ hàm, trả về kiểu boolean
 *      + chờ cho đến khi check_data là true để đảm bảo rằng luồng này sẽ không tiếp tục cho đến 
 *        khi có dữ liệu mới và in ra màn hình dữ liệu cảm biến
 * 
 * check_data = false:
 *      + đặt lại biến check_data để chuẩn bị cho lần đọc dữ liệu tiếp theo
 * 
 * lock.unlock(): 
 *      + ngay sau khi truy cập vào màn hình console để in dữ liệu thì mở khóa ngay để tiếp tục 
 *        đọc dữ liệu cho lần tiếp theo, giúp giảm độ nghẽn trong quá trình xử lý dữ liệu
 ***********************************************************************************************/
void process_data(){
    while (1){
        unique_lock<mutex> lock(mtx);
        cv.wait(lock, []{return check_data;});
        cout << "Data: " << sensor_data << endl;
        lock.unlock();
        check_data = false; 
    }
}


int main(int argc, char const *argv[])
{
    thread task1(sensor_read);
    thread task2(process_data);

    task1.join();
    task2.join();

    /************************************************************************************************
     * Luồng task1 (sensor_read) mô phỏng việc đọc dữ liệu từ cảm biến mỗi 2 giây, sau đó cập nhật 
     * biến sensor_data và thông báo rằng dữ liệu đã sẵn sàng.
     * 
     * Luồng task2 (process_data) chờ dữ liệu từ task1. Khi sensor_read thông báo, process_data sẽ 
     * lấy giá trị sensor_data, hiển thị ra màn hình, và đánh dấu rằng dữ liệu đã được xử lý.
     ***********************************************************************************************/
    return 0;
}
```

<br>

**Ví dụ 2:**

```cpp
#include <iostream>
#include <thread>               // tạo ra các luồng
#include <mutex>                // khóa dữ liệu khi có nhiều luồng cùng truy cập
#include <condition_variable>   // sử dụng biến điều kiện giúp đồng bộ hóa giữa các luồng
#include <chrono>               // quản lý thời gian

using namespace std;

void delay(uint32_t time){
    this_thread::sleep_for(chrono::seconds(time));
}

int sensor_data = 0;

bool check_data = false;

mutex mtx;

condition_variable cv;

void sensor_read(){
    while (1){
        delay(2);
        sensor_data = rand() % 100;
        check_data = true;
        unique_lock<mutex> lock(mtx);    // có thể ko cần
        cout << "Read data done!\n";
        lock.unlock();
        cv.notify_all();
    }
}

void process_data_1(){
    while (1){
        unique_lock<mutex> lock(mtx);
        cv.wait(lock, []()->bool{return check_data;}); // trả về true -> break
        cout << "Process Data 1: " << sensor_data << endl;
        lock.unlock();
        check_data = false;  
    }
}

void process_data_2(){
    while (1){
        unique_lock<mutex> lock(mtx);
        cv.wait(lock, []()->bool{return check_data;}); // trả về true -> break
        cout << "Process Data 2: " << sensor_data << endl;
        lock.unlock();  // giảm độ nghẽn
        check_data = false;       
    }
}

int main(int argc, char const *argv[])
{
    thread task1(sensor_read);
    thread task2(process_data_1);
    thread task3(process_data_2);

    task1.join();
    task2.join();
    task3.join();

    return 0;
}
```

</p>
</details>

</p>
</details>

<details><summary><b>12.4. Bất đồng bộ</b></summary>
<p>

<details><summary><b>📚 Khái niệm</b></summary>
<p>

- Bất đồng bộ (asynchronous programming) là một kỹ thuật lập trình cho phép thực hiện các tác vụ mà không cần chờ chúng hoàn thành trước khi tiếp tục thực hiện các tác vụ khác. Bằng cách này, chương trình có thể tận dụng tối đa tài nguyên hệ thống và cải thiện hiệu suất.
- Trong C++, lập trình bất đồng bộ cho phép chạy các tác vụ trong các luồng riêng biệt, xử lý các tác vụ mất thời gian (như I/O, tính toán nặng) mà không làm gián đoạn luồng chính. Các thành phần chính để hỗ trợ lập trình bất đồng bộ trong C++ bao gồm:

+ ``` std::async ```: Khởi chạy một tác vụ bất đồng bộ, trả về đối tượng.
+ ``` std::future ```: Được sử dụng để lưu trữ kết quả của một tác vụ sẽ hoàn thành trong tương lai.
+ ``` std::shared_future ```: Cho phép nhiều luồng cùng truy cập kết quả của một tác vụ bất đồng bộ.

</p>
</details>

<details><summary><b>📚 Tạo và khởi chạy luồng bất đồng bộ</b></summary>
<p>

- std::async là một hàm trong C++ (từ C++11 trở đi) tạo ra một luồng để thực thi một công việc (task) có thể chạy bất đồng bộ, dựa trên một hàm hoặc biểu thức lambda mà bạn cung cấp. Kết quả của công việc này được trả về dưới dạng std::future, kiểu dữ liệu cho phép truy xuất giá trị của tác vụ sau khi nó hoàn tất.
- Cú pháp:
``` std::future<T> std::async(std::launch policy, Callable&& func, Args&&... args) ```

- policy: chế độ chạy:

+ std::launch::async: hàm sẽ chạy ngay lập tức trên một luồng mới.
+ std::launch::deferred: hàm chỉ chạy khi kết quả của std::future được yêu cầu (lazy evaluation), nghĩa là khi bạn gọi future.get().

- func:  Hàm hoặc biểu thức lambda cần thực hiện bất đồng bộ.
- args: Các tham số truyền vào func (nếu có).

<br>

</p>
</details>

<details><summary><b>📚 Truy cập kết quả luồng bất đồng bộ</b></summary>
<p>

- Khi std::async được gọi, một tác vụ bất đồng bộ được tạo ra và vùng bộ nhớ cho kết quả sẽ được cấp phát động (trên heap).
- std::future hoặc std::shared_future sẽ nắm quyền quản lý kết quả này và giữ một con trỏ đến vùng nhớ chứa kết quả.
- Khi get()được gọi, giá trị được trả về từ bộ nhớ này. Nếu là std::future, get() sẽ chỉ có thể gọi một lần. Nếu là std::shared_future, get() có thể gọi nhiều lần.
- Sự khác nhau giữa std::future và std::shared_future

+ std::future: Đại diện cho một giá trị sẽ có trong tương lai và chỉ cho phép một luồng duy nhất lấy giá trị đó. Sau khi phương thức get() được gọi, giá trị sẽ được "di chuyển" và không thể lấy lại từ std::future lần nữa. Nếu có nhiều thread cố gắng gọi get() trên cùng một std::future, chương trình sẽ bị lỗi.
+ std::shared_future: Cho phép nhiều luồng cùng chia sẻ và truy cập kết quả của một tác vụ bất đồng bộ. Đối tượng shared_future có thể được sao chép và chia sẻ giữa các thread, mỗi thread có thể gọi get() mà không làm mất kết quả.
Khi khởi tạo bằng std::async, std::future là lựa chọn mặc định. Nếu cần chia sẻ kết quả giữa nhiều thread, bạn phải chuyển std::future sang std::shared_future.

**Ví dụ 1:**

```cpp
#include <iostream>
#include <thread>
#include <future>
#include <mutex>

using namespace std;

int sensor_data = 0;

mutex mtx;

void delay(uint32_t time){
    this_thread::sleep_for(chrono::seconds(time));
}

int sensor_read(){
    for (int i=0; i<7; i++){
        unique_lock<mutex> ulock(mtx);
        cout << "sensor read, i = " << i << endl;
        ulock.unlock();
        delay(1);
    }
    sensor_data = rand() % 100;
    cout << "Read data done!\n";
    return sensor_data;
}

void task1(uint32_t time){
    int i = 0;
    while(1){
        unique_lock<mutex> ulock(mtx);
        cout << "task 1, i = " << i++ << endl;
        ulock.unlock();
        delay(time);
    }
}

void task2(uint32_t time){
    int i = 0;
    while(1){
        unique_lock<mutex> ulock(mtx);
        cout << "task 2, i = " << i++ << endl;
        ulock.unlock();
        delay(time);
    }
}

int main(int argc, char const *argv[])
{
    future<int> sensor_future = async(launch::async, sensor_read);
    /*
    std::async với launch::async sẽ khởi chạy sensor_read trong một thread riêng biệt,
    nghĩa là hàm `sensor_read` chạy trong một thread riêng mà không làm ảnh hưởng chương trình chính.
    Kiểu trả về là future<int>, cho phép lấy giá trị kết quả từ sensor_read sau khi hoàn thành.
    */

    thread t1(task1, 1);     // t1: đại diện cho luồng 1
    thread t2(task2, 3);     // t2: đại diện cho luồng 2

    int i = 0;
    while (i<10){
        unique_lock<mutex> ulock(mtx);
        cout << "This is main, i = " << i++ << endl;
        ulock.unlock();
        delay(1);
    }

    cout << "Data đã hoàn thành 1: " << sensor_future.get() << endl;// delete
    // cout << "Data đã hoàn thành 2: " << sensor_future.get() << endl;

    t1.join();
    t2.join();

    return 0;
}
```

<br>

**Ví dụ 2:**

```cpp
#include <iostream>
#include <thread>
#include <future>
#include <mutex>
#include <chrono>

using namespace std;

mutex mtx;

int asyncTask(){
    this_thread::sleep_for(chrono::seconds(3));
    return 100;
}

void processResult(int id, shared_future<int> f){
    unique_lock<mutex> lock(mtx);
    cout << "Task " << id << ": " << f.get() << endl;
    lock.unlock();
}
/*********************************************************************************************************************
 * Hàm này nhận hai tham số: id là một số nguyên đại diện cho ID của tác vụ và f là một shared_future<int> dùng để lấy kết 
 * quả từ tác vụ bất đồng bộ.
 * Trong hàm, f.get() được gọi để lấy giá trị từ shared_future và in ra kết quả. Kết quả này sẽ là 100 vì đó là giá trị 
 * trả về của asyncTask.
 * shared_future cho phép nhiều luồng cùng truy cập vào kết quả của một tác vụ bất đồng bộ.
 ********************************************************************************************************************/

int main(int argc, char const *argv[])
{
    shared_future<int> shared_ft = async(launch::async, asyncTask).share();
    /*********************************************************************************************************************
     * Phương thức .share() được gọi trên đối tượng future để chuyển đổi nó thành std::shared_future<int>. Điều này cho 
     * phép nhiều luồng cùng chia sẻ kết quả của tác vụ mà không làm mất giá trị.
     ********************************************************************************************************************/

    thread t1(processResult, 1, shared_ft);
    thread t2(processResult, 2, shared_ft);
    /*********************************************************************************************************************
     * t1 và t2 được khởi tạo để chạy hàm processResult với các tham số tương ứng là 1 và 2 cho id, cùng với shared_ft 	 
     * (kết quả chia sẻ).
     * Cả hai thread sẽ chạy song song và gọi f.get() từ shared_ft để lấy kết quả từ tác vụ asyncTask. Vì shared_future 
     * cho phép chia sẻ kết quả, nên việc gọi f.get() trên các thread khác nhau vẫn an toàn và có thể thực hiện đồng 
     * thời.t1 và t2 được khởi tạo để chạy hàm processResult với các tham số tương ứng là 1 và 2 cho id, cùng với 
     * shared_ft (kết quả chia sẻ).
     * Cả hai thread sẽ chạy song song và gọi f.get() từ shared_ft để lấy kết quả từ tác vụ asyncTask. Vì shared_future 
     * cho phép chia sẻ kết quả, nên việc gọi f.get() trên các thread khác nhau vẫn an toàn và có thể thực hiện đồng thời.
     *********************************************************************************************************************/


    t1.join();
    t2.join();

    return 0;
}
```

</p>
</details>

</p>
</details>

</p>
</details>

<br>

</p>
</details>
