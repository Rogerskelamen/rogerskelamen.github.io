---
title: C#基础注意点
date: 2021-03-03 19:24:03
updated: 2021-06-03 12:50:00
index_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/Cdsafasdf.png
banner_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/QQ截图20210313181341.png
tag:
- c
categories:
- 编程语言
---


# C#基础注意点

> C#和基本C的最大区别估计就在于取消了指针，不过这也让我们变轻松了（不用再管那些令人头疼的内存管理了），像Java那样采用了自动清理的方式。

# 数据类型

## 小数类型

除了我们熟悉的`float`和`double`这些浮点数类型（并非是真正的小数类型）外，C#提供了一种更高精度的小数类型——`decimal`，这种类型通常用于财务和金融计算领域。

*ps：小数类型的数据需要使用m或者M作为后缀。*

## 字符类型

我们常用的字符类型原来只能表示ascii码，但是C#将其扩展到了Unicode字符集。

## 布尔类型

在C中，我们通常用0来表示false，非0表示true。但是C#直接将false和true常量化，不能转换成数值（更像是脚本了）。

## Var（js狂喜）

var关键字估计都很熟悉了，联系js就知道：当一个变量的**类型无法确定时**，我们可以用var来先声明一波（所以到底是静态还是动态语言啊╰(￣ω￣ｏ)）

*经常用于foreach的循环语句中*

# 输入输出

我们常用的C系列的输入输出基本上就是scanf，printf，cin/cout了，但是C#作为一款集成项目开发的语言，其输入输出当然基本上就在控制台了。

```c#
Console.WriteLine("Hello World!");
// 于是就输出了Hello World
// 要是用变量的话就这样格式输出
Console.WriteLine("Day={0}, Month={1}", d, m);
// {}中的序号从0开始取后边的变量
Console.WriteLine($"Day={d}, Month={m}");
// 当然加上$之后就可以直接在括号中写上变量（类似插值表达式）
```

*一般来说`.NET`的控制台应用会在执行完项目之后立即结束，所以我们一般都看不到输出。我们可以利用Console的ReadKey方法输入单个字符来间接打断程序，造成程序的滞留。*

```c#
Console.WriteLine($"a = {a}, b = {b}");
Console.ReadKey();
```

# 装箱和拆箱

## 装箱（boxing）

将值类型隐式地转换成object类型或任何该值类型所执行的接口类型。

**实质：数据在内存中存储方式发生了变换。**

## 拆箱（unboxing）

object类型显式地转换成值类型或者把任意接口类型转换成一个执行该接口的值类型。

# 方法的注释

在`Main`（注意和基本C和Java不同，C#是大写的`Main`）之外，我们当然可以写一些其他的方法便于调用。但是对方法的描述也是很重要的，我们很希望在调用函数的时候VS就给我们提供关于这个方法的提示信息。于是诞生了方法前的注释。

通常为了和普通注释相区别，要写上**三个斜杠**(`/`)：

```c#
/// <summary>
/// 对方法的描述
/// </summary>
/// <param name="第一个形参">对这个形参的描述</param>
/// <param name="第二个形参">对这个形参的描述</param>
/// <returns>对返回值的描述</returns>
```

## out和params

> 数值类型数据想要在方法中更改他们的值时，必须要加上`ref`关键字，这是形参就是一个地址（相当于C++的引用）。

而`out`和`params`都是处理多个返回值时的解决方案。

```c#
public static void GetSum(int a, out int b){...}
```

传递b之后，b就相当于是一个返回值了。*而`params`是传入一个数组：*

```c#
public static void GetArr(string name, params int[] score){...}
```

**`out`是不需要初始化的**

# 数组（Array）

> 数组的初始化有4种写法（茴香豆的“茴”字有四种写法，造么？）

```c#
int[] arr1;
int[] arr2 = new int [10];
int[] arr3 = {12, 24, 45};
int[] arr4 = new int[3] {12, 24, 34};
```

不得不承认，很像java语法了。

*然后二维数组也是这么多种的初始化方式：*

```c#
int[,] arr1;
int[,] arr2 = new int[3,4];
int[,] arr3 = new int[,] {{1}, {1}, {1}};
int[,] arr4 = new int[3, 1] {{1}, {1}, {1}};
```

需要注意的是，**我们可以只声明一个二维数组的第一个维度，而空出第二个维度**（和C的数组相适应）。于是每个二维数组里面的数组可以不同，这样不同的特殊数组成为**锯齿数组**。

## 赋值

如果你只是用等号用一个数组对另一个数组进行赋值：

```c#
arr2 = arr1;
```

这样只会让arr2的地址指向arr1（即引用赋值），其中一个变化另一个跟着变。**而这一般不是我们所想要的。**（究其本质是数组是一种引用类型）

另一种方法：

```c#
arr1.CopyTo(arr2, 0);
// 第二个参数是从哪个索引号开始拷贝
```

这样我们就可以得到一个独立的拷贝数组。

# 关于字符串

> 一定要搞清楚，char数组和字符串不是一种类型的数据！
> 一个是`char[]`，一个是`string`！

将字符串变成字符数组：

```c#
char[] arr = str.ToCharArray();
```

但是将字符数组转化成字符串：

```c#
string str = new string(arr);
```

*查找字符的索引使用`IndexOf()`方法。*

*在索引位置插入字符使用`Insert()`方法。*

*替换指定字符用`Replace()`方法。*

*删除指定片段使用`Remove()`方法。*

*截取指定片段使用`Substring()`方法。*

**字符串拼接最好还是用StringBuilder类的方法`Append()`**，而不是用`+=`。

## @赋值

通常C#中的一个字符串中如果有转义字符的话，C#是会默认转义的，但是用了`@`之后就不需要转义路径中的特殊字符了。

eg:

```c#
string str = @"\n";
```

# 类和对象

## set和get

> 记得python和js里面的set和get吧，看来C#偏向脚本化实锤了。

如果我们在类中声明一个私有（`private`）的属性，就只可以用get和set方法和数据进行联系了。

```c#
private string name;

public string Name{
    get { return name; } // 读操作
    set { name = value; } // 写操作
}
```

*于是产生了自动属性这种东西：*

```c#
public char Gender { get; set; }  // 自动属性
```

这样声明之后，C#会自动帮我们创建并关联一个Gender相关的属性。（*通过反编译可以查看到*）

*ps：按下<kbd>Ctrl</kbd>+<kbd>r</kbd>和<kbd>Ctrl</kbd>+<kbd>e</kbd>可以快速生成set和get方法*

## 继承

>  因为C#的继承和其他语言不太一样（但是还是C++形式），在此进行区分

| 语言   | 继承形式 |
| ------ | -------- |
| C#     | ：       |
| Java   | extends  |
| python | ()       |

### 构造函数部分

如果是想要在子类构造函数传递参数到父类中，则使用`base`：

```c#
public Student(string name, int age): base(name, age){...}
```

### sealed & abstract

很显然被`sealed`修饰的类是不能被继承的，但是abstract类作为抽象类又是必须被继承，所以两个可以一起记忆。

*当然这对方法也同样适用。（被sealed修饰的方法一定是和override一起修饰）*

## 接口（interface）

> 因为java还没学到接口那里，所以暂时写详细一些(类只能单继承，但是接口可以多继承)

```c#
interface ISpeakabe{
    void 
}
```

很多时候，一个接口很像一个抽象类，比如我们还是得去重写接口中的抽象方法。

### 接口方法重名

如果有两个接口，每一个都有一个交func的方法，那Student类继承这两个接口怎么分别去重写这两个方法呢？

C#采用的方法是像实例对象一样建立一个对应关系，然后这么写：

```c#
// 在类中显式实现接口方法
public void interface1.func(){}
public void interface2.func(){}
// 这两其实在重写方法
```

## 委托(delegate)

> 个人感觉委托就像是python中的装饰器（其实不是，其实是类似于C中的函数指针）

但是很怪的是，这种方式又是一种数据类型（类似于一种方法的引用），我们在建立一个委托时必须new一个（其实也可以不new），然后就是将必要的方法委托给委托。

委托的最大特点是可以使用`+=`运算符来委托多个方法，然后来决定每个方法的调用的顺序。

```c#
MyDelegate01 myDelegate01 = M1;
myDelegate01 += M4;
myDelegate01 += M5;
myDelegate01 += M6;
myDelegate01();
// 则M1,M4,M5,M6会依次执行
```

*当然我们还可以移除委托的方法，使用`-=`*

### Lambda表达式

> 其实委托最强大的功能不是调用方法，而是配合匿名函数进行委托

有一点ES6基础的同学都知道JS存在一种叫做箭头表达式的东西（好像是这么称呼的吧😶），这个Lambda表达式其实就是箭头表达式。

匿名函数优势：不用声明函数并为函数取名，直接再委托里面定义函数体和形参。

```c#
MyDelegate myDelegate = new MyDelegate((msg) => {
    ...
})
```

### 内置委托

Action：无参数无返回值的委托
Action\<T\>:有参数无返回值的委托
Func\<T,T>:有参数有返回值的委托

直接看例子：

```c#
Func<int, int> func = new Func<int, int>((n) => n);
```

### 事件

个人感觉委托定义到了类中就变成了事件

## 集合

- 集合是用来管理多个数据的
- 常用集合类：ArrayList，Hashtable，Queue，Stack

首先是ArrayList：

添加元素：`Add(data)`方法，可以添加任意数据类型的数据。然后**只认单个元素**（即如果插入数组，这个数组中的每个元素算一个数据。

插入元素：`Insert(location，data)`，

删除元素：`Remove(data)`，`RemoveAt(location)`，`RemoveRange(start, stop)`

清楚所有元素：`Clear()`

再说List：（泛型）

声明一个List：

```c#
List<int> list = new List<int>() {1, 2, 3, 4};
```

常用方法：`Max()`,`Min()`,`Average()`,`Sum()`

使用`Contains()`来判断元素是否在List中（返回一个布尔值）

使用`Where(Lambda())`来选择出元素。

还有Dictionary：（泛型）

声明一个Dictionary：

```c#
Dictionary<string, int> dic = new Dictionary<string, int>();
```

添加数据：`Add(key, value)`

判断数据是否在Dic内：`ContainKey(key)`

遍历键：`foreach(string item in dic.Keys)`，当然还有`foreach(int item in dic.Values)`

于是就有遍历键值对：

```c#
foreach(KeyValuePair<sting, int> item in dic){...}
```

# 窗体应用

首先要认知的一点是我们创建的所有控件都是在主窗口中（通常取名为`Form1`）的，然后我们可以用Program.cs文件来修改当前会运行的窗口程序。

## Form类

我们创建的窗口都是继承了这个类，其中定义了每个控件的功能，我们可以使用`this`来指向这个类，然后`this.Control`就是**当前这个窗口中的所有控件**（也就是说我们可以对`this.Control`来循环遍历，对每一个控件进行操控。

## 列表框（List Box）

常用属性：

- Items：存储列表中的内容。
- SelectedItem：表示所选列表条目的内容
- SelectedIndex：表示所选列表条目的序号
- SelectedIndexChanged：当选择条目发生变化时触发

## 组合框（ComboBox）

常用属性：

- DropDownStyle：确定组合框的显示类型。有三个值可选
  - Simple：无下拉框，可编辑
  - DropDown：有下拉框，可编辑
  - DropDownList：有下拉框，不可编辑
- Items：存储下拉列表中的内容
- SelectedItem：所选下拉列表条目的内容
- SelectedIndex：所选下拉列表条目的序号



产生一个新窗口（在有多个窗口的程序中）

``` c#
Form form2 = new Form2();
form2.Show();

// 关闭一个窗口
form2.Close();		// 但是一般不这么关
```

# 连接数据库(MS SQL Server)

## 数据库连接字符串

一般格式：

```c#
string conStr = "Data Source=DESKTOP-CC4F3VS;Initial Catalog=MyDB01;Integrated Security=True";
```

## 使用using：

可以使用`using`来进行暂用资源的创建和销毁（比如数据库的连接）:

```c#
using (SqlConnection conn=new SqlConnection(conStr)){
    
}
```

使用sql语句的套话：

```c#
string sql = "select * from table1";
using (SqlCommand cmd = new SqlCommand(sql, conn)){
    conn.Open();
    int n = cmd.ExecuteNonQuery();
    if (n > 0){
        Console.WriteLine("成功查询了{0}条数据", n);
    }else{
        Console.WriteLine("查询失败。");
    }
}
```

## 文件操作

C#最常见的文件操作就是使用文件流类了`FileStream`（类似于python）

eg.

```c#
private void btn_write_Click(object sender, EventArgs e)
{
    string path = tbx_path.Text;
    string content = tbx_content.Text;
    FileStream fsWriter = new FileStream(path, FileMode.Create, FileAccess.Write);
    byte[] array = Encoding.UTF8.GetBytes(content);
    fsWrite.Write(array, 0, array.Length);
    MessageBox.Show("文件写入成功!");
    fsWriter.Close();
    fsWriter.Dispose();
}
// 这里的项目类型是窗体应用

private void btn_read_Click(object sender, EventArgs e)
{
    string path = tbx_path.Text;
    using (FileStream fsReader = new FileSteam(path, FileMode.Open, FileAccess.Read))
    {
        byte[] array = new byte[1024];
        fsReader.Read(array, 0, (int)fsReader.Length);
        tbx_content.Text = Encoding.UTF8.GetString(array);
    }
}
```

