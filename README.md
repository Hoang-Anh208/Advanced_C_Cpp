# C
<details><summary>Nhấp vào để xem chi tiết</summary>
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

</p>
</details>

<details><summary><b>📚 Ứng dụng con trỏ</b></summary>
<p>

**Nhập số từ bàn phím**
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

**Hoán đổi 2 số**
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

**Ví dụ**
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

</p>
</details>

</p>
</details>



# C++
<details><summary>Nhấp vào để xem chi tiết</summary>
<p>

</p>
</details>
