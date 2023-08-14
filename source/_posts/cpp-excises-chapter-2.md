---
hidden: true
title: C++ Primer Plus 第2章课后习题
date: 2015-06-14 03:57:00
tags:
  - C++
categories:
  - C++
---
## 补缺补漏

嗯！！！

## 复习题
1. 函数
2. 引入 iostream 头文件
3. 引入名为 std 的名称空间
4. `std::cout << "Hello, World" << std::endl;`
5. `int cheeses;`
6. `cheeses = 32;`
7. `std::cin >> cheeses;`
8. `std::cout << "We have " << cheeses << " variables of cheeses";`
9. 定义了一个形参为 double、返回值为 int 的函数 froop；定义了一个形参为 int、返回值为空的函数 rattle；定义了一个形参为空(无形参)、返回值为 int 的函数 prune
10. 返回值为空

<!--more-->

## 编程练习
1.
```cpp
#include <iostream>
using namespace std;
int main()
{
	cout << "My name is is YumeMichi" << endl;
	return 0;
}
```

2.
```cpp
#include <iostream>
using namespace std;
int main()
{
	int var;
	cout << "Enter a number: ";
	cin >> var;
	cout << "Result: " << var << endl;
	return 0;
}
```

3.
```cpp
#include <iostream>
using namespace std;

void func1(void);
void func2(void);

int main()
{
	func1();
	func1();
	func2();
	func2();
	return 0;
}

void func1(void)
{
	cout << "Three blind mice." << endl;
}

void func2(void)
{
	cout << "See how they run" << endl;
}
```

4.
```cpp
#include <iostream>
using namespace std;

float tem_convert(int c_tem);

int main()
{
	int c_tem;
	float f_tem;
	cout << "Please enter a Celsius value: ";
	cin >> c_tem;
	cout << c_tem << " degrees Celsius is " << (int)tem_convert(c_tem) << " degrees Fahrenheit." << endl;
	return 0;
}

float tem_convert(int c_tem)
{
	return (1.8 * c_tem + 32.0);
}
```

5.
```cpp
#include <iostream>
using namespace std;

double time_convert(double light_year);

int main()
{
	double light_year, ast_units;
	cout << "Enter the number of light years: ";
	cin >> light_year;
	cout << light_year << " light years = " << time_convert << " astronomical units." << endl;
	return 0;
}

double time_convert(double light_year)
{
	return (63240 * light_year);
}
```

6.
```cpp
#include <iostream>
using namespace std;

void show_time(int hour, int minute);

int main()
{
	int hour, minute;
	cout << "Enter the number of hour and minute: ";
	cin >> hour >> minute;
	show_time(hour, minute);
	return 0;
}

void show_time(int hour, int minute)
{
	cout << "Enter the number of hour: " << hour << endl;
	cout << "Enter the number of minute: " << minute << endl;
	cout << "Time: " << hour << ":" << minute << endl;
}
```
