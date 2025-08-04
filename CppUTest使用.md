CppUTest 是C/C++ 的单元测试框架

### **1. CppUTest 安装**

#### **1.1 apt-get 安装**

$ sudo apt-get install cpputest

#### **1.2 源码安装**

- 官网: [http://cpputest.github.io/](http://cpputest.github.io/)
- git clone https://github.com/cpputest/cpputest.git

```shell
git clone https://github.com/cpputest/cpputest.git
cd cpputest
mkdir cpputest_build
cmake -B cpputest_build
cmake --build cpputest_build
make && make install
```


### **2. CppUTest 介绍**

#### **2.1 构造待测试代码 (C语言)**

```
// file: math_operations.c

#include "math_operations.h"
#include <stdlib.h>
#include <string.h>

// C接口函数实现
int add(int a, int b) {
    return a + b;
}

int subtract(int a, int b) {
    return a - b;
}

int multiply(int a, int b) {
    return a * b;
}

int divide(int a, int b) {
    if (b != 0) {
        return a / b;
    }
    return 0; // 简单处理除零情况
}

// 字符串处理函数实现
char* concatenate_strings(const char* str1, const char* str2) {
    if (str1 == NULL || str2 == NULL) {
        return NULL;
    }
    
    size_t len1 = strlen(str1);
    size_t len2 = strlen(str2);
    char* result = (char*)malloc(len1 + len2 + 1);
    
    if (result == NULL) {
        return NULL;
    }
    
    strcpy(result, str1);
    strcat(result, str2);
    
    return result;
}

int string_length(const char* str) {
    if (str == NULL) {
        return 0;
    }
    // 泄漏点
    char* result = (char*)malloc(strlen(str));

    return (int)strlen(str);
}
```

```
// file: math_operations.h
#ifndef MATH_OPERATIONS_H
#define MATH_OPERATIONS_H

#ifdef __cplusplus
extern "C" {
#endif

// C接口函数声明
int add(int a, int b);
int subtract(int a, int b);
int multiply(int a, int b);
int divide(int a, int b);

// 字符串处理函数
char* concatenate_strings(const char* str1, const char* str2);
int string_length(const char* str);

#ifdef __cplusplus
}
#endif

#endif // MATH_OPERATIONS_H
```



#### **2.2 测试用例的组成, 写法**

CPPUTest 中C文件使用TEST_GROUP_C_SETUP, TEST_GROUP_C_TEARDOWN, TEST_C等, 基于`TestHarness_c.h`头文件

C++文件使用 TEST_GROUP, 然后定义属于这个 TEST_GROUP 的 TEST

如果要将C用例包裹, 则需要使用TEST_GROUP_C_WRAPPER, TEST_C_WRAPPER等


```
// file:TestMathOperations.c
#include "CppUTest/TestHarness_c.h"
#include "CppUTest/MemoryLeakDetectorMallocMacros.h" // 测试泄露
#include "math_operations.h"
#include <stdio.h>

TEST_GROUP_C_SETUP(MathOperationsTestGroup)
{
    // printf("setup\n");
}

TEST_GROUP_C_TEARDOWN(MathOperationsTestGroup)
{
    // printf("teardown\n");
}

TEST_C(MathOperationsTestGroup, TestAdd)
{
    int result = add(3, 4);
    CHECK_EQUAL_C_LONG(7, result);
}

TEST_C(MathOperationsTestGroup, TestSubtract)
{
    int result = subtract(10, 3);
    CHECK_EQUAL_C_LONG(7, result);
}

TEST_C(MathOperationsTestGroup, TestMultiply)
{
    int result = multiply(3, 4);
    CHECK_EQUAL_C_LONG(12, result);
}

TEST_C(MathOperationsTestGroup, TestDivide)
{
    int result = divide(12, 4);
    CHECK_EQUAL_C_LONG(3, result);
}

TEST_C(MathOperationsTestGroup, TestDivideByZero)
{
    int result = divide(12, 0);
    CHECK_EQUAL_C_LONG(0, result);
}

TEST_C(MathOperationsTestGroup, TestStringLength)
{
    int result = string_length("Hello");
    CHECK_EQUAL_C_LONG(5, result);
}

TEST_C(MathOperationsTestGroup, TestStringLengthWithNull)
{
    int result = string_length(NULL);
    CHECK_EQUAL_C_LONG(0, result);
}

TEST_C(MathOperationsTestGroup, TestConcatenateStrings)
{
    char* result = concatenate_strings("Hello", "World");
    CHECK_C(result != NULL);
    CHECK_EQUAL_C_STRING("HelloWorld", result);
    free(result);
}

TEST_C(MathOperationsTestGroup, TestConcatenateStringsWithNull)
{
    char* result = concatenate_strings(NULL, "World");
    CHECK_EQUAL_C_POINTER(NULL, result);
    
    result = concatenate_strings("Hello", NULL);
    CHECK_EQUAL_C_POINTER(NULL, result);
}
```

```
// file: TestMathOperationsWrapper.cpp 对C接口进行封装 
#include "CppUTest/TestHarness.h"
#include "CppUTest/TestHarness_c.h"

// 包装C测试组和测试用例
TEST_GROUP_C_WRAPPER(MathOperationsTestGroup){TEST_GROUP_C_SETUP_WRAPPER(MathOperationsTestGroup) TEST_GROUP_C_TEARDOWN_WRAPPER(MathOperationsTestGroup)};

TEST_C_WRAPPER(MathOperationsTestGroup, TestAdd)
TEST_C_WRAPPER(MathOperationsTestGroup, TestSubtract)
TEST_C_WRAPPER(MathOperationsTestGroup, TestMultiply)
TEST_C_WRAPPER(MathOperationsTestGroup, TestDivide)
TEST_C_WRAPPER(MathOperationsTestGroup, TestDivideByZero)
TEST_C_WRAPPER(MathOperationsTestGroup, TestStringLength)
TEST_C_WRAPPER(MathOperationsTestGroup, TestStringLengthWithNull)
TEST_C_WRAPPER(MathOperationsTestGroup, TestConcatenateStrings)
TEST_C_WRAPPER(MathOperationsTestGroup, TestConcatenateStringsWithNull)
```

主程序

```MainTest.cpp
#include "CppUTest/CommandLineTestRunner.h"

int main(int argc, char** argv)
{
    // 默认运行所有测试
    // return CommandLineTestRunner::RunAllTests(argc, argv);
    
    // 也可以通过编程方式指定运行特定的测试组
    // 例如只运行CalculatorTestGroup组:
    // return CommandLineTestRunner::RunAllTests(argc, argv, "xxxxxxATestGroup");
    
    // 或者运行多个指定的测试组:
    // const char* groupFilters[] = {"xxxATestGroup", "xxxBTestGroup", nullptr};
    // return CommandLineTestRunner::RunAllTests(argc, argv, groupFilters);
    
    // 当前默认行为：运行所有测试
    return CommandLineTestRunner::RunAllTests(argc, argv);
}
```
#### **2.3 测试用例结果判断 ( fail, 各种assert等等)**

测试完成后, 可以用 CPPUTest 提供的宏来判断测试结果是否和预期一致.

CPPUTest 提供的用于判断的宏如下(见`UtestMacros.h`): (C接口见`TestHarness_c.h`)

|                                              |                                                     |
| -------------------------------------------- | --------------------------------------------------- |
| **Assertion 宏**                              | **含义**                                              |
| CHECK(boolean condition)                     | condition==True则成功; 反之失败                            |
| CHECK_TEXT(boolean condition, text)          | condition==True则成功; 反之失败, 并且失败时输出 text信息            |
| CHECK_EQUAL(expected, actual)                | expected==actual则成功; 反之失败                           |
| CHECK_THROWS(expected_exception, expression) | 抛出的异常 expected_exception==exception则成功; 反之失败        |
| STRCMP_EQUAL(expected, actual)               | 字符串 expected==actual则成功; 反之失败                       |
| LONGS_EQUAL(expected, actual)                | 数字 expected==actual则成功; 反之失败                        |
| BYTES_EQUAL(expected, actual)                | 数字 expected==actual则成功; 反之失败 (数字是 8bit 宽)           |
| POINTERS_EQUAL(expected, actual)             | 指针 expected==actual则成功; 反之失败                        |
| DOUBLES_EQUAL(expected, actual, tolerance)   | double型 expected和actual在误差范围内(tolerance)相等则成功; 反之失败 |
| FAIL(text)                                   | 总是失败, 并输出 text 信息                                   |

#### **2.4 运行测试用例时的编译选项配置

```
cmake_minimum_required(VERSION 3.10)
project(CppUTestExample C CXX)

# 设置C++标准
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# 添加编译选项
add_compile_options(-Wall -Wextra)

# CppUTest路径设置
set(CPPUTEST_HOME /usr/local CACHE PATH "CppUTest installation directory")

# 设置CppUTest的包含目录和库
set(CPPUTEST_INCLUDE_DIR ${CPPUTEST_HOME}/include)
set(CPPUTEST_LIBRARY_DIR ${CPPUTEST_HOME}/lib)

# 检查CppUTest是否存在
if(NOT EXISTS "${CPPUTEST_INCLUDE_DIR}/CppUTest/TestHarness.h")
    message(FATAL_ERROR "CppUTest headers not found. Please set CPPUTEST_HOME to the correct path or install CppUTest.")
endif()

# 包含目录
include_directories(${CPPUTEST_INCLUDE_DIR})

# 源文件
set(SRCS
    math_operations.c
)

# 测试源文件
set(TEST_SRCS
    TestMathOperations.c
    TestMathOperationsWrapper.cpp
    MainTest.cpp
)

# 创建可执行文件
add_executable(TestRunner ${SRCS} ${TEST_SRCS})

# 链接CppUTest库
target_link_libraries(TestRunner
    ${CPPUTEST_LIBRARY_DIR}/libCppUTest.a
    ${CPPUTEST_LIBRARY_DIR}/libCppUTestExt.a
)

# 如果在Unix系统上，可能还需要链接pthread
if(UNIX)
    target_link_libraries(TestRunner pthread)
endif()

# 添加测试目标
add_custom_target(test
    COMMAND ${CMAKE_BINARY_DIR}/TestRunner
    DEPENDS TestRunner
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
)

# 添加清理目标
add_custom_target(clean_build
    COMMAND ${CMAKE_COMMAND} --build ${CMAKE_BINARY_DIR} --target clean
)

# 添加安装目标
install(TARGETS TestRunner
    RUNTIME DESTINATION bin
)

# 打印构建信息
message(STATUS "CppUTest Home: ${CPPUTEST_HOME}")
message(STATUS "CppUTest Include Dir: ${CPPUTEST_INCLUDE_DIR}")
message(STATUS "CppUTest Library Dir: ${CPPUTEST_LIBRARY_DIR}")
message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")

```

#### **2.5 运行测试用例, 查看结果的方法**

运行可执行文件 TestRunner 就可以实施测试.
```shell
$ ./TestRunner    <-- 默认执行, 没有参数

TestMathOperationsWrapper.cpp:14: error: Failure in TEST(MathOperationsTestGroup, TestConcatenateStrings)
TestConcatenateStrings:14: error:
        Deallocating non-allocated memory
   allocated at file: <unknown> line: 0 size: 0 type: unknown
   deallocated at file: /root/work/testCppUtest/TestMathOperations.c line: 63 type: free


..............
Errors (1 failures, 15 tests, 15 ran, 23 checks, 0 ignored, 0 filtered out, 0 ms)
```

其他用法
```shell
$ ./TestRunner -c   <-- -c 执行结果加上颜色 (成功绿色, 失败红色)

$ ./TestRunner -v  <-- -v 显示更为详细的信息

$ ./TestRunner -r 2   <-- -r 指定测试执行的次数, 这里把测试重复执行2遍

$ ./TestRunner -g MathOperationsTestGroup    <-- -g 指定执行的 TEST_GROUP 名称

$ ./TestRunner -n TestAdd    <-- -s 指定执行其中一个 TEST, 名称为 TestAdd

$ ./TestRunner -v -n TestAdd  <-- 参数也可以搭配使用
```

#### **2.7 内存泄漏检测插件**

内存泄漏一直是C/C++代码中令人头疼的问题, 还好, CPPUTest 中提供了检测内存泄漏的插件, 使用这个插件, 可使我们的代码更加健壮.

使用内存检测插件时, **测试代码** 和 **待测代码** 在编译时都要引用.

`CppUTest/MemoryLeakDetectorMallocMacros.h`
