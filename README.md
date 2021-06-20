# CPP_learning
Its my notes of  CPP study

## 类的规范

类的定义建议写在头文件，与此同时会把成员函数的声明写在头文件

但是注意把成员函数的定义写在CPP文件而非头文件（虽然这样做在语法上没有问题），但因为函数只能被定义一次。若放在头文件中，当头文件被多个CPP文件iclude时，函数会被多次定义。



## 类const成员函数

如下声明和定义const成员函数

```c++
class Screen {
public:
   char get() const;
};

char Screen::get() const {
   return _screen[_cursor];
}
```

若将成员成员函数声明为const，则该函数不允许修改类的数据成员。

const成员函数可以被具有相同参数列表的非const成员函数重载，例如

```c++
class Screen {
public:
    char get(int x,int y);
    char get(int x,int y) const;
};
```

在这种情况下，类对象的常量性决定调用哪个函数。

```c++
const Screen cs;
Screen cc2;
char ch = cs.get(0, 0);  // 调用const成员函数
ch = cs2.get(0, 0);     // 调用非const成员函数
```

小结：

1）非const对象可以访问所有的成员函数、变量；const对象除了不能访问非const成员函数外，其它都可以访问。（在C++中，只有被声明为const的成员函数才能被一个const类对象调用）；

2）作为一种良好的编程风格，在声明一个成员函数时，若该成员函数并不对数据成员进行修改操作，应尽可能将该成员函数声明为const 成员函数。

3）const成员函数也会将this指针限定为const，这样不能使用this来修改对象的值

参考资料：https://blog.csdn.net/lihao21/article/details/8634876

## 引用传参

意义：提高效率，不用再产生数据的副本或者指向数据的指针的副本。

```c++
Point pt1(10,10);
Point &pt2=pt1;

void show (int* a，int& b)；
//按以上写法是输入参数a的指针而产生新的意义*a，输入参数b而产生新的意义&b的地址
```

引用引入了对象的一个同义词。定义引用的表示方法与定义指针相似，只是用&代替了*。引用必须在定义时马上被初始化，因为它必须是某个东西的同义词。不能先定义一个引用后才初始化它。

需要特别强调的是引用并不产生对象的副本，仅仅是对象的同义词。

## this指针

```c++
class Stock{
    private:
    	int total_val;
    public:
    	int show_total() const {return total_val };
    	//以上函数中范围的total_val其实是this->total_val的缩写
        const Stock & /***/ topval(const Stock & s) /***/ const;
}


const Stock & /***/ Stock::topval(const Stock & s) /***/ const{
	if (s.show_total() > show_total())
		return s;
	else
		return *this;
}
//注意，在函数定义处加入/***/是为了更好地理解以上声明的读法，第一部分为返回类型，可见是返回了一个引用（别名）
```

注意：const成员函数也会将this指针限定为const，这样不能使用this来修改对象的值。

## 类的对象数组

定义并初始化对象数组

```c++
const int number = 10;
//结合列表和构造函数初始化对象数组
//构造函数已进行重载或传入默认参数
Stock stocks[number] = {
    Stock("hello",1,2),
    Stock(),//注意是逗号
    Stock("wolrd")
}
```

## 作用域

```C++
namespace NamespaceA{
    int x;
    class ClassA {
    public:
        int x;
    };
}

int main() {

    // A namespace name used to disambiguate
    NamespaceA::x = 1;

    // A class name used to disambiguate
    NamespaceA::ClassA a1;
    a1.x = 2;
}
```

可以在代码中的一个结构前后加上花括号，界定域，例如

```c++
int main(){
    int a=0;
    {
        int b=1;
    }
    //程序运行到此时a变量依旧存在而b这个自动变量已经消失被程序释放
    cout<<a;
    return 0；
}
```

## ：的用法

```c++
class report
{
	private:
		std::string str;
	public:
		report(const std::string s) : str(s) {//？？？？？
		std::cout << "Object created.\n";
}
~report() {
		std::cout << "Object deleted.\n";
}
```

图中不解的那一行代码意义如下，从：的作用出发

（1）表示机构内位域的定义（即该变量占几个bit空间）

```c++
typedef struct _XXX{//命名风格之一

unsigned char a:4;//指该变量占四个字节

unsigned char c;

} XXX;
```

（2）构造函数后面的冒号起分割作用，是类给成员变量赋值的方法，初始化列表，更适用于成员变量的常量const型。

```c++
class _XXX{
	private:
    	std::string int str;
    public:
		_XXX(const std::string s) : str(s) {}
};
```

## 构造函数和析构函数理解

1.定义的类对象必须要初始化，未初始化的对象，会使编译器报错

```c++
//如果类中只定义了普通构造函数，而没有定义默认构造函数，一下做法是非法的
test myclassone；
//而当你以如下方式定义带参数的构造函数
test::test(int first=0,int second=0,int third=0){
	this->a=first;
	this->b=second;
	this->c=third;
}
/*或者像下面这么做，进行构造函数重载*/
/*
test::test(){
	this->a=0;
	this->b=0;
	this->c=0;
}
test::test(int first,int second,int third){
	this->a=first;
	this->b=second;
	this->c=third;
}
*/

//编译器会认为同时存在了默认构造函数和普通构造函数，所以以下做法是合法的
test myclasstwo;//隐式调用默认构造函数
test myclassthree=test();//显式调用默认构造函数，且与下面代码调用两次构造函数析构函数不同，该行代码只调用了一次构造函数和一次析构函数


{
test myclassfour;//此处会先调用一次默认构造函数
myclassfour = test();//此时再次对test()这个临时对象调用了浅拷贝构造函数给myclassfour再次赋值，然后立刻调用析构函数销毁test()
}//此处运行出myclassfour的作用域，myclassfour对象调用是析构函数
```

2.构造函数和析构函数的调用一定是一一对应的，换言之，整个程序中，构造函数和析构函数的调用的次数是相等的。

3.一个对象的默认构造函数或普通构造函数只能被调用一次，但是拷贝构造函数可以被多次调用。



## 操作符（运算符）重载

1.重载的运算符必须是有效的C++运算符。

2.运算符表示法中运算符左侧为调用对象，运算符右侧为参数被传递对象。

3.重载运算符的格式如下

```c++
test test::operator+(const test & t) const{
    test sum;
    sum.a=a+y.a;
    sum.b=b+y.b;
    sum.b=b+y.c;
    return sum;
}
```

4.使用重载运算符的两种方式

```
test classone;
test classtwo;
test classsum;
classsum = classone.operator+(classtwo);
classsum = classone + classtwo;
```

5.重载限制

1）重载运算符的操作数至少一个是用户定义类型。

2）运算符的优先级和原来一样，不能违反原来的语法规则。

3）不能创建新运算符，且有一部分运算符不能重载。

## 友元函数和友元重载

对于友元函数，要在类声明中声明，类外部定义。并且声明前加friend，定义时不要加friend和类作用域。如下

```c++
friend test operator+(int outa,const test & s);
//上面一行代码写在类public部分的声明中

test operator+(int outa,const test & s){
    test outsum;
    outsum.a=outa+s.a;
    outsum.b=outa+s.b;
    outsum.c=outa+s.c;
}
```

此时可以如此使用

```
test getsum_a;
test calssfive=test(1,1,1);
getsum_a= 1 + classfive;
//编译器会把它解读为：getsum_a= operator+(1,classfive);
```

若未定义友元函数，以上做法是非法的，要注意成员函数重载运算符的左边是调用对象，可是上边左边为参数常量。

若要用到对<<的巧妙友元重载，随时查阅C++PrimerPlus----P321。

若对重载运算符同时作为成员函数和友元函数，可能会引发二义性错误，导致便宜错误，详情看P322。

