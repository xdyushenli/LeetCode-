# 简介
给定一个整数, 输出该数二进制表示法中 1 的个数。

# js 中的数制转换 API
首先我们来介绍两个在 js 中能实现数制转换的 API: `toString()` 和 `parseInt()`。

## toString([radix])
**数字类型的 `toString` 方法**接收一个范围在 [2, 36] 内的整数作为参数, 用于确定转化后的数制, 返回值为转化后的字符串。默认为 10。
应当注意的是, **这种数制转换仅在数字上调用 `toString` 方法时才会有效, 对于在字符串上调用该方法不会进行数制转换。**

## parseInt(str[, radix])
这个方法接收两个参数, 第一个参数表示要转化的字符串, 第二个参数为一个范围在 [2, 36] 内的整数, **用于确认要将字符串视为多少进制来进行转换。**
如果没有提供 radix 参数, 则 `parseInt()` 会根据字符串前缀来判断数制, `0x` 开头的为 16 进制, 其他的默认为 10 进制。
**parseFloat() 不提供类似功能!**

# 实现
最先想到的应该就是把数字转换为二进制, 之后再计算其中 1 的个数。上面介绍的 `toString()` 恰好就可以满足我们的需要。
现在让我们来写第一种实现方法:
```js
function numberOf1InBinary(num) {
    // 从数制转换到计算 1 的个数一步到位!
    return num.toString(2).match(/1/g) !== null ? num.toString(2).match(/1/g).length : 0;
}
```
第二种方法是先将目标数字与 1 做位与运算, 判断目标数字最低位是否为 1。接着把 1 左移一位得到 2, 再和目标数字做位与运算, 判断目标数字从左数第二位是否为 1。这样反复左移, 每次都能判断目标数字的某一位是否为 1。
在这种方法中, **循环次数等于二进制的位数。**在 JS 内部, 数值都是以 64 位浮点数的形式储存的, 但是做位运算的时候, 是以32位带符号的整数进行运算的, 并且返回值也是一个32位带符号的整数, 所以共需要循环 32 次。
```js
function numberOf1InBinary(num) {
    let flag = 1;
    let count = 0;

    while(flag > 0) {
        if(num & flag) {
            count++;
        }
        flag << 1;
    }

    return count;
}
```
第三种方法更为巧妙。利用了如下原理: **把一个整数减去 1, 再和原来的整数做位与运算, 就会把整数最左边的 1 变为 0。**
```js
function numberOf1InBinary(num) {
    let count = 0;

    while(num > 0) {
        num = (num - 1) & num;
        count++;
    }

    return count;
}
```

# 相关题目
* 判断某个数是不是 2 的 n 次方: 判断该数二进制表示中 1 的个数即可, 如果是 2 的 n 次方, 则其二进制表示中有且只有一位为 1, 其他位均为 0。
* 输入两个整数 m 和 n, 判断 m 的二进制需要多少次转换才能变为 n: 求 m 与 n 的`异或(^)`, 再统计异或的二进制表示中 1 的个数即可。