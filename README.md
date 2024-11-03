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
