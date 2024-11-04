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

</p>
</details>

<br>

<details><summary><b>6. Template</b></summary>
<p>

</p>
</details>

<br>

<details><summary><b>7. Namespace</b></summary>
<p>

</p>
</details>

<br>

<details><summary><b>8. Standard Template Library (STL)/b></summary>
<p>

</p>
</details>

<br>

<details><summary><b>9. Design Patterns</b></summary>
<p>

</p>
</details>

<br>

<details><summary><b>10. Smart Pointer</b></summary>
<p>

</p>
</details>

<br>

<details><summary><b>11. Đa luồng (Thread)</b></summary>
<p>

</p>
</details>

<br>

</p>
</details>
