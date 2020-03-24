Unity Test API
==============

[![Unity Build Status](https://api.travis-ci.org/ThrowTheSwitch/Unity.png?branch=master)](https://travis-ci.org/ThrowTheSwitch/Unity)
__Copyright (c) 2007 - 2020 Unity Project by Mike Karlesky, Mark VanderVoord, and Greg Williams__

Getting Started
===============
The [docs](docs/) folder contains a [getting started guide](docs/UnityGettingStartedGuide.md)
and much more tips about using Unity. 

Unity Assertion Summary
=======================
For the full list, see [UnityAssertionsReference.md](docs/UnityAssertionsReference.md).


Unity是一个单元测试框架。目标是精简而强大。Unity测试框架的内核是三个文件：单个C
文件和两个头文件。它们相互合作提供许多函数和宏以使得测试更顺滑。
Unity的设计是跨平台的。它尽可能地遵从C标准，同时提供对许多不守规矩的嵌入式C编译器
的支持。Unity已被用在许多编译器上，包括GCC、IAR、Clang、Green Hills、 Microchip和
MS Visual Studio。将其移植到一个新目标上不需要太多工作量。

> 目录结构

src - 这是你关心的代码！这个文件夹包含一个C文件以及两个头文件。这三个文件 是 Unity。

docs - 你正在读这个文档，所以可能你是自己进到这个文件夹中的。这里放着所有的文档。

examples - 这里包含一些使用Unity的示例。

extras - 这些是可选的Unity插件，它们不是内核工程的一部分。如果你是通过James Grenning的
书找到我们的，你会想看看这里。

test - 这里是Unity和它的所有脚本被测试的地方。如果你只是在用Unity，很可能你永远不需要
看这个文件夹。如果你是团队中负责把Unity移植到新工具链的中奖者，这里可以帮助你验证所有东西
都被合理地配置了。

auto - 在这里你将发现一些有用的Ruby脚本，它们可以简化你的测试工作。他们完全只是可选的，
不用它们照样可以使用Unity。

> 怎么创建一个测试文件

测试文件都是C文件。大部分情况下，你将为每个你想要测试的C模块创建一个测试文件。
测试文件应该include unity.h以及你要测试的C模块的头文件。

下一步，一个测试文件将包含一个setUp()以及tearDown()函数。setUp函数可以包含任何你
想要在每个测试之前运行的东西。tearDown函数可以包含任何你想要在每个测试之后运行的东西。
两个函数都不接受任何参数，也不返回东西。如果你在使用一个将这些函数配置为可选的编译器，
你也许可以不实现他们。不确定？试一试不就知道了。如果你的编译器抱怨说它在链接时找不到
setUp或tearDown，那你就知道你起码需要为它们创建一个空的函数了。

文件的主体将会是一系列的测试函数。测试函数遵从以下命名约定，以"test_"或"spec_"为开头。
不是必须按照这种方式命名它们，但是这样会使得其他开发者更容易看出哪些函数是测试。同样的，
Unity或Ceedling自带的自动化脚本将默认通过这种前缀的方式来寻找测试函数。测试函数都不接受
任何参数，也不返回东西。所有的测试计数是由Unity内部处理的。

最终，在你的测试文件的最底下，你将写一个main()函数。这个函数将调用UNITY_BEGIN()，
然后为每个测试调用RUN_TEST，然后最终是UNITY_END()。这是实际上触发每个测试函数运行
的地方，所以重要的是，每个函数都要有它自己的RUN_TEST。

把每个测试加进main函数的过程真是很无聊。如果你喜欢在构建过程中使用helper脚本，你可以
考虑用我们特有用的generate_test_runner.rb脚本。它会为你创建main函数以及所有的调用，
前提是你遵从建议的命名约定。这种情况下，你根本不需要将main函数包含进你的测试文件中。
当你完成上述步骤，你的测试文件应该看起来像这样：

```c
#include "unity.h"
#include "file_to_test.h"

void setUp(void) {
    // 在这里配置东西
}

void tearDown(void) {
    // 在这里清理东西
}

void test_function_should_doBlahAndBlah(void) {
    //测试
}

void test_function_should_doAlsoDoBlah(void) {
    //更多测试
}

int main(void) {
    UNITY_BEGIN();
    RUN_TEST(test_function_should_doBlahAndBlah);
    RUN_TEST(test_function_should_doAlsoDoBlah);
    return UNITY_END();
}

```

Basic Validity Tests
--------------------

    TEST_ASSERT_TRUE(condition)

Evaluates whatever code is in condition and fails if it evaluates to false

    TEST_ASSERT_FALSE(condition)

Evaluates whatever code is in condition and fails if it evaluates to true

    TEST_ASSERT(condition)

Another way of calling `TEST_ASSERT_TRUE`

    TEST_ASSERT_UNLESS(condition)

Another way of calling `TEST_ASSERT_FALSE`

    TEST_FAIL()
    TEST_FAIL_MESSAGE(message)

This test is automatically marked as a failure. The message is output stating why.

Numerical Assertions: Integers
------------------------------

    TEST_ASSERT_EQUAL_INT(expected, actual)
    TEST_ASSERT_EQUAL_INT8(expected, actual)
    TEST_ASSERT_EQUAL_INT16(expected, actual)
    TEST_ASSERT_EQUAL_INT32(expected, actual)
    TEST_ASSERT_EQUAL_INT64(expected, actual)

Compare two integers for equality and display errors as signed integers. A cast will be performed
to your natural integer size so often this can just be used. When you need to specify the exact size,
like when comparing arrays, you can use a specific version:

    TEST_ASSERT_EQUAL_UINT(expected, actual)
    TEST_ASSERT_EQUAL_UINT8(expected, actual)
    TEST_ASSERT_EQUAL_UINT16(expected, actual)
    TEST_ASSERT_EQUAL_UINT32(expected, actual)
    TEST_ASSERT_EQUAL_UINT64(expected, actual)

Compare two integers for equality and display errors as unsigned integers. Like INT, there are
variants for different sizes also.

    TEST_ASSERT_EQUAL_HEX(expected, actual)
    TEST_ASSERT_EQUAL_HEX8(expected, actual)
    TEST_ASSERT_EQUAL_HEX16(expected, actual)
    TEST_ASSERT_EQUAL_HEX32(expected, actual)
    TEST_ASSERT_EQUAL_HEX64(expected, actual)

Compares two integers for equality and display errors as hexadecimal. Like the other integer comparisons,
you can specify the size... here the size will also effect how many nibbles are shown (for example, `HEX16`
will show 4 nibbles).

    TEST_ASSERT_EQUAL(expected, actual)

Another way of calling TEST_ASSERT_EQUAL_INT

    TEST_ASSERT_INT_WITHIN(delta, expected, actual)

Asserts that the actual value is within plus or minus delta of the expected value. This also comes in
size specific variants.


    TEST_ASSERT_GREATER_THAN(threshold, actual)

Asserts that the actual value is greater than the threshold. This also comes in size specific variants.


    TEST_ASSERT_LESS_THAN(threshold, actual)

Asserts that the actual value is less than the threshold. This also comes in size specific variants.


Arrays
------

    _ARRAY

You can append `_ARRAY` to any of these macros to make an array comparison of that type.  Here you will
need to care a bit more about the actual size of the value being checked.  You will also specify an
additional argument which is the number of elements to compare.  For example:

    TEST_ASSERT_EQUAL_HEX8_ARRAY(expected, actual, elements)

    _EACH_EQUAL

Another array comparison option is to check that EVERY element of an array is equal to a single expected
value. You do this by specifying the EACH_EQUAL macro. For example:

    TEST_ASSERT_EACH_EQUAL_INT32(expected, actual, elements)

Numerical Assertions: Bitwise
-----------------------------

    TEST_ASSERT_BITS(mask, expected, actual)

Use an integer mask to specify which bits should be compared between two other integers.  High bits in the mask are compared, low bits ignored.

    TEST_ASSERT_BITS_HIGH(mask, actual)

Use an integer mask to specify which bits should be inspected to determine if they are all set high.  High bits in the mask are compared, low bits ignored.

    TEST_ASSERT_BITS_LOW(mask, actual)

Use an integer mask to specify which bits should be inspected to determine if they are all set low.  High bits in the mask are compared, low bits ignored.

    TEST_ASSERT_BIT_HIGH(bit, actual)

Test a single bit and verify that it is high.  The bit is specified 0-31 for a 32-bit integer.

    TEST_ASSERT_BIT_LOW(bit, actual)

Test a single bit and verify that it is low.  The bit is specified 0-31 for a 32-bit integer.

Numerical Assertions: Floats
----------------------------

    TEST_ASSERT_FLOAT_WITHIN(delta, expected, actual)

Asserts that the actual value is within plus or minus delta of the expected value.

    TEST_ASSERT_EQUAL_FLOAT(expected, actual)
    TEST_ASSERT_EQUAL_DOUBLE(expected, actual)

Asserts that two floating point values are "equal" within a small % delta of the expected value.

String Assertions
-----------------

    TEST_ASSERT_EQUAL_STRING(expected, actual)

Compare two null-terminate strings.  Fail if any character is different or if the lengths are different.

    TEST_ASSERT_EQUAL_STRING_LEN(expected, actual, len)

Compare two strings.  Fail if any character is different, stop comparing after len characters.

    TEST_ASSERT_EQUAL_STRING_MESSAGE(expected, actual, message)

Compare two null-terminate strings.  Fail if any character is different or if the lengths are different. Output a custom message on failure.

    TEST_ASSERT_EQUAL_STRING_LEN_MESSAGE(expected, actual, len, message)

Compare two strings. Fail if any character is different, stop comparing after len characters. Output a custom message on failure.

Pointer Assertions
------------------

Most pointer operations can be performed by simply using the integer comparisons above. However, a couple of special cases are added for clarity.

    TEST_ASSERT_NULL(pointer)

Fails if the pointer is not equal to NULL

    TEST_ASSERT_NOT_NULL(pointer)

Fails if the pointer is equal to NULL

Memory Assertions
-----------------

    TEST_ASSERT_EQUAL_MEMORY(expected, actual, len)

Compare two blocks of memory.  This is a good generic assertion for types that can't be coerced into acting like
standard types... but since it's a memory compare, you have to be careful that your data types are packed.

\_MESSAGE
---------

you can append \_MESSAGE to any of the macros to make them take an additional argument.  This argument
is a string that will be printed at the end of the failure strings.  This is useful for specifying more
information about the problem.

