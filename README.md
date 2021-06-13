# CPP_learning
Its my notes of  CPP study

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
    cout<<a;
    //程序运行到此时a变量依旧存在而b这个自动变量已经消失被程序释放
    return 0；
}
```

