---
title: MinUnit - C
date: 2022-06-02 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - cpp
---

# 参考网址

https://mp.weixin.qq.com/s/CklfQPoyvJCKv8ohu-oqOw

# 例程

```cc
// minunit.h
#define mu_assert(message, test) do { if (!(test)) return message; } while (0)
 #define mu_run_test(test) do { char *message = test(); tests_run++; \
                                if (message) return message; } while (0)
 extern int tests_run;
```

```cc
// test.cc
 #include <stdio.h>
 #include "minunit.h"
 
 int tests_run = 0;
 
 int foo = 7;
 int bar = 4;
 
 static char * test_foo() {
     mu_assert("error, foo != 7", foo == 7);
     return 0;
 }
 
 static char * test_bar() {
     mu_assert("error, bar != 5", bar == 5);
     return 0;
 }
 
 static char * all_tests() {
     mu_run_test(test_foo);
     mu_run_test(test_bar);
     return 0;
 }
 
 int main(int argc, char **argv) {
     char *result = all_tests();
     if (result != 0) {
         printf("%s\n", result);
     }
     else {
         printf("ALL TESTS PASSED\n");
     }
     printf("Tests run: %d\n", tests_run);
 
     return result != 0;
 }
```

# 结果

```shell
error, bar != 5
Tests run: 2
```

# 参考之后自己写的cc版本的

```cc
#include<iostream>

// 改写成类似于C语言的宏定义
class MuTest {
public:
    static int tests_run;
    static char *run_test(char *(*test)()) {
        char *message = test();
        tests_run++;
        if (message)
            return message;
        return 0;
    }

    static char *assert_true(bool test, char *message) {
        if (!test)
            return message;
        return 0;
    }
};


int MuTest::tests_run = 0;


// 测试用例
char *test_fail() {
    return MuTest::assert_true(false, (char *)"fail");
}


char *test_pass() {
    return MuTest::assert_true(true, (char *)"pass");
}


// 测试用例集
char *all_tests() {
    char *message = 0;
    message = MuTest::run_test(test_fail);
    if (message)
        return message;
    message = MuTest::run_test(test_pass);
    if (message)
        return message;
    return 0;
}


int main() {
    char *message = all_tests();
    if (message)
        std::cout << message << std::endl;
    else
        std::cout << "ALL TESTS PASSED" << std::endl;
    std::cout << "Tests run: " << MuTest::tests_run << std::endl;
    return 0;
}
```

# 结果

```shell
fail
Tests run: 1
```

