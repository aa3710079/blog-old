---
layout: post
title: Call By Value, Call By Reference and Call By Address
description: ""
modified: 2018-10-06
tags: []
categories: [note]
image:
    feature:
    credit:
    creditlink:
---
Call By Value, Call By Reference跟Call By Address 這三個傳遞參數的方法，每次使用時，都要google一下，然後查完用完，覺得自己又成長了好棒棒這樣，等到下一次要用又忘記，我都已經碩一了耶，再這樣下去也不是辦法，只好花個時間記錄一下
## Call By Value
{% highlight C++ %}
#include <iostream>
using namespace std;

int sum(int, int);

int main()
{
    int a = 5;
    int b = 10;

    cout << sum(a, b); // 輸出15

    return 0;
}

int sum(int x, int y)
{
    return x + y;
}
{% endhighlight %}
記憶體位址與內容長得像下面這樣:

|            |     a    |     b    |..|  x   | y    |
| ---------- | -------- | -------- |--| ---- | ---- |
| **value**  | 5        | 10       |..| 5    | 10   |
| **address**| 0x04     | 0x08     |..| 0x16 | 0x20 |

基本上也就是把a的值複製給x、b的值複製給y，沒什麼特別的

## Call By Reference
```C++=
#include <iostream>
using namespace std;

void Swap(int&, int&);

int main()
{
    int a = 5;
    int b = 10;

    Swap(a, b);

    cout << "a = " << a << ", " << "b = " << b; // 輸出 a = 10, b = 5

    return 0;
}

void Swap(int &x, int &y)
{
    int temp;

    temp = x;
    x = y;
    y = temp;
}
```
在呼叫Swap以前，記憶體位址與內容長得像這樣:

|            |     a    |     b    |
| ---------- | -------- | -------- |
| **value**  | 5        | 10       |
| **address**| 0x04     | 0x08     |

當程式跑到第21行:

|            |     a    |     b    |..|  x   | y    |
| ---------- | -------- | -------- |--| ---- | ---- |
| **value**  | 5        | 10       |..| 5    | 10   |
| **address**| 0x04     | 0x08     |..| 0x04 | 0x08 |

注意到了嗎? x不管是address還是value都跟a一樣，也就是說，x完完全全就是a本人，x只是a變數的別名
Swap完後，記憶體長這樣:
|            |     a    |     b    |..|  x   | y    |
| ---------- | -------- | -------- |--| ---- | ---- |
| **value**  | 10       | 5        |..| 10   | 5    |
| **address**| 0x04     | 0x08     |..| 0x04 | 0x08 |

對x跟y做任何的運算，結果會完全套用在a跟b身上，而且整個程式在run的過程中，不會有新的變數產生，重頭到尾就只有a跟b而已

## Call By Address
```C++=
#include <iostream>
using namespace std;

void Swap(int*, int*);

int main()
{
    int a = 5;
    int b = 10;

    Swap(&a, &b);

    cout << "a = " << a << ", " << "b = " << b; // 輸出 a = 10, b = 5

    return 0;
}

void Swap(int *x, int *y)
{
    int temp;

    temp = *x;
    *x = *y;
    *y = temp;
}
```
當程式跑到20行:

|            |     a    |     b    |..| x    | y    |
| ---------- | -------- | -------- |--| ---- | ---- |
| **value**  | 5        | 10       |..| 0x04 | 0x08 |
| **address**| 0x04     | 0x08     |..| 0x16 | 0x20 |

&為取址運算子，加在變數前面，可以提取該變數的address
當呼叫Swap(&a, &b)時，傳遞的是a及b的位址，分別為0x04以及0x08
int \*x代表宣告一個指標叫做x，而指標是用來存放記憶體位址的，所以x可以用來存放a的位址，指標本身也是一個變數，會佔用記憶體空間
**注意:** "temp = \*x" 中的星號，跟"void Swap(int \*x, int \*y)"中的星號，意義並不相同，前者是dereference，代表提取x指標所指向的位址的值；後者代表宣告一個指標變數
目前為止:
**x  == 0x04
\*x == 5
&x == 0x16**

Swap完之後:

|            |     a    |     b    |..| x    | y    |
| ---------- | -------- | -------- |--| ---- | ---- |
| **value**  | 10       | 5        |..| 0x04 | 0x08 |
| **address**| 0x04     | 0x08     |..| 0x16 | 0x20 |

此時:
**x == 0x04
\*x == 10
&x == 0x16**
其實，Call By Address根本就是Call By Value的一種，只是傳遞的是**址**不是**值**

參考資料: http://wp.mlab.tw/?p=176
