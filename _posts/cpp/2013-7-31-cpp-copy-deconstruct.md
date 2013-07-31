---
layout: post
title: "复制控制"
description: ""
category: learning
tags: [cpp, 学习]
---
{% include JB/setup %}

# cpp中的复制控制

{% highlight c %}
#include<iostream>
using namespace std;

class Exmpl
{
public:
    static int count;
    Exmpl():ID(count++){std::cout << ID << " Exmpl()" << std::endl;}
    Exmpl(const Exmpl&):ID(count++){std::cout << ID << " Exmpl(const Exmpl&)" << std::endl;}
    Exmpl&  operator=(const Exmpl& other)
    {
        std::cout << ID << " operator=(const Exmpl& other)" << endl;
        return *this;
    }
    ~Exmpl()
    {
        std::cout << ID <<  "~Exmpl()" << endl;
    }
private:
    int ID;
};
int Exmpl::count = 0;


Exmpl f(Exmpl &e)
{
    std::cout << "f(Exmpl e)" << endl;
    return e;
}
int main()
{
    Exmpl e1;
    Exmpl e2 = f(e1);
}
{% endhighlight %}

运行结果

	0 Exmpl()
	f(Exmpl e)
	1 Exmpl(const Exmpl&)
	1~Exmpl()
	0~Exmpl()

一共两个Exmpl 对象 e1和e2 然而

{% highlight c %}
int main()
{
    Exmpl e1;
    Exmpl e2;
    e2 = f(e1);
}
{% endhighlight %}

运行结果为:

	0 Exmpl()
	1 Exmpl()
	f(Exmpl e)
	2 Exmpl(const Exmpl&)
	1 operator=(const Exmpl& other)
	2~Exmpl()
	1~Exmpl()
	0~Exmpl()

生成了3个, 也就是在f函数返回的时候生成了一个临时对象, 这样看来

1. 当返回的匿名对象用作初始化时, 并没有生成一个新的临时对象, 而是使用了函数中的局部对象(这里其实是e1,通过引用传进去)
2. 而当返回的匿名对象用作赋值时, 会产生一个新的临时对象, 再通过赋值函数赋值.

当然有可能可编译器有关.


#### vector 和 array

{% highlight c %}
int main()
{   
	vector<Exmpl> ev(2);
	Exmpl es[2];
}
{% endhighlight %}

结果为:
	
	//vector
	0 Exmpl()	//先初始化一个临时对象
	1 Exmpl(const Exmpl&) //复制构造vector[0]
	2 Exmpl(const Exmpl&) //复制构造vector[1]
	0~Exmpl()	//临时对象析构
	3 Exmpl()	//初始化es[0]
	4 Exmpl()	//初始化es[1]
	4~Exmpl()	//析构es[1]
	3~Exmpl()	//析构es[2]
	1~Exmpl()	//析构ev[0]
	2~Exmpl()	//析构ev[1]

vector 的析构顺序从小到大

array 的析构顺序是逆序的
