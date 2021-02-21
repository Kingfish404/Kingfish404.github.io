---
layout: post        # page:单页面,不在归档索引;post:有前后页面，索引
title: cpp笔记与思考-string与实现    # 标题
date: 2021-02-14    # 日期
categories:         # 集合,暂未使用
Author:  jinyu      # 作者
tags: [cpp,笔记]              # 标签，博客分类页面显示
comments: true          # bool,是否可以评论
toc:                # bool,是否有目录
pinned:             # bool,是否置顶
description: 深入理解并部分实现一个cpp的string类 
hide: 
---

追根溯源，剖析STL是个享受的过程。

<!-- more -->

## 类构造

```c++
// 类构造
class String
{
public:
    String(const char *str = NULL);	// 构造函数
    String(const String &str);		// 拷贝构造函数
    ~String();						// 析构函数

    String operator+(const String &str) const;	// 重载+
    String& operator=(const String &str);		// 重载=
    String& operator+=(const String &str);		// 重载+=
    bool operator==(const String &str) const;	// 重载==
    char& operator[](int n) const;				// 重载[]

    int getLength() const;		//获取长度

    friend istream& operator>>(istream &is, String &str); // 输入
    friend ostream& operator<<(ostream &os, String &str); // 输出

private:
    char *data;		//字符串
};
```

## 部分实现

string类实际上还拥有vector类的大部分方法（好像是全部？），在表现上

理解string类为vector\<char\>也可以，但是string有对+等运算符的重载，同时内寸管理方式也不同。

```c++
#include <iostream>
#include <cstring>

using namespace std;

// 实现 String 类
class String
{
public:
    // 普通构造函数
    String(const char *str = NULL) {
	if (str == NULL) {
	    m_data = new char[1];
	    m_data = '\0';
	} else {
	    int length = strlen(str);

	    m_data = new char[length + 1];

	    strcpy(m_data, str);
	}			
    }

    // 拷贝构造函数,深复制
    String(const String &other) {
	if (!other.m_data) {
	    m_data = NULL;
	}

	m_data = new char[strlen(other.m_data) + 1];

	strcpy(m_data, other.m_data);
    }

    // 析构函数
    ~String() {
	if (m_data) {
	    delete[] m_data;
	    m_data = NULL;
	}
    }

    // 重载 + 字符串连接, 不能返回引用
    String operator+(const String &other) const {
	String newString;

	delete[] newString.m_data;

	if (!other.m_data) {
	    newString = *this;	
	} else if (!m_data) {
	    newString = other;
	} else {
	    newString.m_data = new char[strlen(m_data) + strlen(other.m_data) + 1];
	    strcpy(newString.m_data, m_data);
	    strcat(newString.m_data, other.m_data);
	}

	return newString;
    }

    // 重载 = 赋值
    String& operator=(const String &other) {
	if (this != &other) {
	    delete[] m_data;

	    if (!other.m_data) {
		m_data = NULL;
	    } else {
		m_data = new char[strlen(other.m_data) + 1];
		strcpy(m_data, other.m_data);
	    }
	}

	return *this;
    }

    // 重载 ==
    bool operator==(const String &other) const {
	if (strlen(m_data) != strlen(other.m_data)) {
	    return false;
	} else {
            return strcmp(m_data, other.m_data) ? false : true;
	}
    }

    // 重载 +=
    String& operator+=(const String &other) {
        char *newData = new char[strlen(m_data) + strlen(other.m_data) + 1];

	strcpy(newData, m_data);
	strcat(newData, other.m_data);

	delete[] m_data;

	m_data = newData;

	return *this;
    }

    // 重载 []
    char& operator[](int n) const {
	if (n >= strlen(m_data)) {
	    return m_data[strlen(m_data) - 1];
	}

	return m_data[n];
    }

    // 获取长度
    int getLength() const {
	return strlen(m_data);
    }

    // 输入, 重载输入操作，需要先申请一块内存，用于存放输入字符串
    friend istream& operator>>(istream &is, String &str) {
	char strTemp[100];

	memset(strTemp, 0, sizeof(strTemp));

	is >> strTemp;

	str.m_data = new char[strlen(strTemp) + 1];

	strcpy(str.m_data, strTemp);

	return is;
    }

    // 输出
    friend ostream& operator<<(ostream &os, String &str) {
	os << str.m_data;

	return os;
    }

private:
    char *m_data;
};

// 测试
void test(){
    String s;

    cin >> s;

    cout << s << " : " << s.getLength() << endl;

	
    String s1(s);
	
    cout << s1 << " : " << s1.getLength() << endl;

    const char *str = "Hello";

    String s2(str);

    cout << s2 << " : " << s2.getLength() << endl;


    char str1[] = "world!";

    String s3(str1);

    cout << s3 << " : " << s3.getLength() << endl;

    String s4 = s3;

    if (s4 == s3) {
	cout << "s3 == s4" << endl;
    } else {
	cout << "s3 != s4" << endl;
    }

    String s5 = s3 + s4;

    cout << s5 << " : " << s5.getLength() << endl;
	
    s3 += s4;

    if (s5 == s3) {
	cout << "s5 == s3" << endl;
    } else {
	cout << "s5 != s3" << endl;
    }

    cout << s5[5] << endl;
}

int main()
{
	test();
    return 0;
}
```

## Reference

* [https://zh.cppreference.com/ - 参考文档](https://zh.cppreference.com/)
* [http://cpp.sh/ - 在线cpp](http://cpp.sh/)
* [SGI-STL - STL源代码解析](https://github.com/steveLauwh/SGI-STL/)

