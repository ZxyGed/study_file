# CLR via C#

## C# 基础

### get 和 set

```C#
public class Student { 
    private string name 
    public string Name { 
        set{ 
            //some operation
            name=value; 
        }
        get{ 
            //some operation
            return name; 
        } 
    } 
} 
```

注意Name和name是两个属性，Name是name的一个封装，set 控制 name 的写属性，get 控制 name 的读属性，在代码中在对应情况下自动调用相应代码。

### out 和 ref

[C#中out和ref之间的区别](https://www.cnblogs.com/sunliyuan/p/5999045.html) 

- <font color=blue>out 和 ref 都是传址</font>，但是 out 作为参数不会将值传入方法，需要在方法内部初始化，类似于C++中的传值参数，ref 则是在外部初始化，并将值传进去，类似C++中的传址。

- 在调用时需要明确用 out 和 ref 修饰参数与方法匹配
- out 和 ref 不能作为重载的判定标准

### Tips

以下语句具有C++中 `system("pause")` 同样的作用

```C#
Console.Write("\r\nPress any key to continue...."); 
Console.Read();
```



## Chapter 1 CLR的执行模型

CLR（common language runtime）【公共语言运行时】是一个可用多种编程语言使用的“运行时”。

核心功能（内存管理、程序集加载、安全性、异常处理和线程同步）可由面向CLR的所有语言使用，【混合编程】。

在编程时，可以选择任何语言，只需要编译器是面向CLR的就可以，不同语言的源码经过编译器的处理的结果都是一个托管模块（32位的PE32文件或64位的PE32+文件）。

对于非托管代码指的是它针对一种 具体的CPU平台进行编译，一旦调用，代码就能执行。

IL (Intermediate Language) ，面向对象的语言

IDL（Interface Definition Language)

metadata 元数据 是一组数据表，描述模块定义的内容（类型、成员）；引用的内容（导入的类型及其成员）

编译器同时生成元数据与代码，将二者绑定并嵌入托管模块（manage module），元数据与IL代码永远不会失去同步

C++编译器最为特殊，允许开发人员同时写托管和非托管的代码



程序集（Assembly）是一个或多个模块资源的逻辑性分组，是重用、安全性以及版本控制的最小单元。可以生成单文件的程序集也可以生成多文件的程序集，取决于编译器或工具的选择，这使得可以将一组文件看作一个实体。

默认情况下，编译器实际上会把生成的托管模块转换成程序集（包括资源代码）

关于/platform开关选项，VS默认为x86（生成->默认平台）

| /platform开关 | 生成的托管模块  | x86 Windows          | x64 Windows          | IA64 Windows         |
| ------------- | --------------- | -------------------- | -------------------- | -------------------- |
| anycpu(默认)  | PE32/不明确指定 | 作为32位应用程序运行 | 作为64位应用程序运行 | 作为64位应用程序运行 |
| x86           | PE32/x86        | 作为32位应用程序运行 | 作WoW64应用程序运行  | 作WoW64应用程序运行  |
| x64           | PE32+/x64       | 不运行               | 作为64位应用程序运行 | 不运行               |
| Itanium       | PE32+/Itanium   | 不运行               | 不运行               | 作为64位应用程序运行 |

其中WoW64是能模拟x86指令集的技术

使用/platform:x86开关编译的一个托管程序集无法加载到64位进程，但同样开关编译的可执行文件能在64位计算机中作为WoW64程序运行

高级语言只公开了CLR功能的一个子集，IL则允许访问CLR所有功能，所以如果选择的一种编程语言隐藏了你迫切需要的一个CLR功能，可以换用IL汇编语言或提供了所需功能的另一种编程语言来写那部分代码。

Jit (just in time) 即时

JITCompiler == JITter

在首次调用某方法时，该方法记录为JITCompiler，需要通过JITCompiler从元数据中找到相应的IL代码并将其编译成本地CPU指令，这些指令被保存到动态分配的内存块中，然后JITCompiler返回该方法的记录处，修改最初对JITComplier的引用，让它指向刚刚保存CPU指令的内存块地址。从而第二次调用时可以全速运行。

影响代码优化的两个C#编译器开关：/optimize和/debug

| 编译器开关设置                       | C# IL代码质量 | JIT 本地代码质量 |
| ------------------------------------ | ------------- | ---------------- |
| /optimize- /debug-（默认）           | 未优化        | 已优化           |
| /optimize- /debug (+/full/pdbonly)   | 未优化        | 未优化           |
| /optimize+ /debug (-/+/full/pdbonly) | 已优化        | 已优化           |

如果使用/optimize-，IL未优化，这将包含许多NOP指令以及分支指令，方便调试。

只有使用/debug (+/full/pdbonly) 情况下，编译器才会生成program database (pdb) 文件，其帮助调试器查找局部变量并将IL映射到源代码

/debug:full 开关告诉JIT编译器你打算调试程序集，JIT会帮你记录每一条IL指令所生成的本地代码。

在VS中新建一个C#项目时，Debug配置是/optimize-和/debug:full开关，而Release配置的是/optimize+和/debug:pdbonly开关

## Chapter 2 

| 简写                                | 含义                                  |
| ----------------------------------- | ------------------------------------- |
| CLS (Common Language Specification) | 公共语言规范，CLR体系中一定支持的部分 |
| FCL (Framework Class Library)       | Framework 类库                        |
|                                     |                                       |
|                                     |                                       |
|                                     |                                       |
|                                     |                                       |



## Chapter 3

## Chapter 4 类型基础

| 简写 | 含义       |
| ---- | ---------- |
| CTE  | 编译时错误 |
| RTE  | 运行时错误 |
|      |            |

| System.Object 的公用方法 |                                                              |
| ------------------------ | ------------------------------------------------------------ |
| Equals                   | 如果两个对象具有相同的值，则返回true                         |
| GetHashCode              | 返回对象值的一个哈希码                                       |
| ToString                 | 默认返回类型的完整名称，往往会重写该方法                     |
| GetType                  | 返回从Type派生的一个对象的实例，同时指出该对象的类型。非虚函数，不可重写 |

| System.Object 的受保护方法 |                                              |
| -------------------------- | -------------------------------------------- |
| MemberwiseClone            | 创建一个所有属性值相同的实力对象             |
| Finalize                   | 在对象的内存被实际回收之前调用该方法。可重写 |



**C# 的 is 和 as 操作符**

- ```c#
  if(o is Employee)
  ```

  return true/false. 首先需要判断是否兼容于Employee，而后遍历继承树核实是否引用一个Employee类型对象

- ```c#
  Employee e = o as Employee
  if(e != null){}
  ```

  return 同一个对象的引用或者 null，只校验一次对象的类型并且永远不会报错

  

**C# 中的命名空间 ** 

- 使用命名空间来减小代码量

  `using` 同 `import` 类似，也就是方便调用该空间中的类（不用写相应的前缀）

```C#
System.IO.FileStream fs = new System.IO.FileStream(...);
// => 等价于
using System.IO;
FileStream fs = new System.IO.FileStream(...);
```

- 使用命名空间来消除歧义

  `using` 还可以用来消除命名空间的歧义

  如果存在两个同名命名空间且均具有同名类，消除歧义的方法可以采用外部别名（Extern Alias），其还允许从同一个程序集的两个或更多不同版本中访问一个类型

```c#
// Microsoft.Widget 和 Wintellect.Widget 存在歧义（不方便使用命名空间），可以using取别名来消除歧义
using WintellectWidget = Wintellect.Widget
```

- 定义命名空间

```C#
namespace CompanyName{
    public sealed class A{...}     //TypeDef: CompanyName.A
    namespace X{
        public sealed class B{...} //TypeDef: CompanyName.X.B (多重命名空间)
    }
}
```



**CLR工作过程中堆栈的处理过程**

关于创建一个对象时的CLR过程，参见 P92 4.4 运行时的相互关系

关于调用静态方法/实例方法的CLR过程，参见 P97 图 4-9 下开始的部分

简短解说

**注意局部变量在栈（对于实例作为局部变量，其地址在堆），对象、类型对象在堆**

方法调用会有序幕（prologue）代码和尾声（epilogue）代码，前者负责在方法开始前为其中的局部变量分配内存，后者负责在方法完成后清理现场。

一个方法的调用首先会在线程栈为局部变量分配内存，而后局部变量（地址）入栈，需要的参数入栈，需要的参数则为用来标注参数在栈中的位置，同时函数的返回地址入栈，函数完成后的return将返回栈中的返回地址

---

对于一个方法中调用的类，CLR会从相应程序集的元数据中提取信息，在堆上创建一些数据结构来表示类型本身，即**类型对象**，堆上所有对象都包含两个额外的成员：**类型对象指针**（type object pointer）和**同步块索引**（sync block index），首先分配内存后会初始化为 null 或 0 （但如果未经显示初始化直接调用会报错），而后调用构造函数（本质是可能修改某些实例数据字段的方法）。new 操作符返回对象的内存地址，该地址即作为局部变量（地址）入栈。**创建的实例对象的类型对象指针，指向相应的类型对象。**

对于局部变量中类的方法的调用，首先通过类型对象指针找到对应的类型对象，而后在其方法表中搜寻对应方法对应的记录项，对方法进行JIT编译然后调用相应代码。如果该方法继承自父类，则会通过类型对象中基类型的字段，一直回溯到Object直到找到对应方法。

<font color=red>类型对象本身也是对象，也具有类型对象指针，他们也会指向上一层的类型对象，顶层的类型对象指向自身。</font>

<font color=blue>GetType返回的是指向对象的类型对象的一个指针。</font>

## Chapter 5 基元类型、引用类型和值类型

编译器直接支持的数据类型成为**基元类型**（primitive type）

```C#
System.Int32 a = new System.Int32();
int a = 0;       //二者等价，int直接反映到System.Int32
```

<font color=red>基元类型与FCL类型对应表见P102，使用FCL类型名称更具灵活性同时也更加便于理解（不存在类型位数的歧义）</font>

```C#
Byte b = 100;        //第一步要求将所有操作数扩大为32位/64位
b = (Byte)(b + 200); //第二步需要将32位/64位转化为Byte，b=44(2C),即256溢出
```

不进行溢出检查可以保证代码更快运行，控制溢出检查的方法

- 全局性： C# 编译器开关 /checked+

  VS中的/checked开关在项目属性->生成->高级->勾选“检查运算上溢/下溢”

- 局部性： 使用 checked 和 unchecked 来在代码特定区域控制溢出检查，记得捕捉OverFlowException

```C#
//使用checked对单行代码进行检查
b = checked((Byte)(b + 200)); //抛出OverFlowException异常
b = (Byte)checked(b+200);     //b = 44(12C)不会抛出异常

//使用checked语句块
//如果在语句块中调用了某个方法，checked语句块不会确认方法内部的溢出
checked{
    Byte b = 100;
    b += 200;                //可直接采用+=运算符
}
```

**Tips：无符号的整数类型不相容于 CLS，同时为了更好的检查上溢下溢，尽量使用有符号数值类型**

 文档将所有称为“类”的类型都是引用类型（用class声明），所有值类型都称为结构或枚举（用struct声明的）

所有值类型都是sealed，防止将其作为基类

```C#
class SomeRef { public Int32 x; }   //引用类型，在堆上创建
struct SomeVal { public Int32 x; }  //值类型，未装箱的话则在栈上创建
```

对栈上的值类型的属性的修改都是直接修改，使用诸如 `SomeVal v2 = v1` 会创建另一个成员与 `v1` 完全相同的对象，而`SomeRef r2 = r1` 则是复制一个引用，该指针与r1指向同一个堆栈上的对象。

```C#
//在线程栈上分配的代码字段均会初始化为0，但要看C#编译器是否承认
SomeVal v1 = new SomeVal(); //C#编译器认为实例v1已初始化，可以直接调用其中的属性
SomeVal v1;                 //C#编译器认为实例v1未初始化，不能直接调用其中的属性
```

**声明为值类型必须满足的条件**

- 类型具有基元类型的行为，即实例字段不可变，其全部字段最好标记为readonly
- 类型不需要从其他类型继承
- 类型不会派生出其他任何类型

- 由于值类型在传参时会整个复制，需要考虑大小，以下条件必须满足任一

  - 类型的实例比较小（约为16字节或更小）

  - 类型的实例较大（大于16字节）但不作为方法的实参传递，也不从方法返回

**不需要为值类型定义Finalize方法**

值类型不在堆上分配，因此值类型的实例在内存被回收时不会通过Finalize方法收到一个通知。同时值类型的已装箱实例被垃圾回收时，CLR也不会调用Finalize方法。

**CLR控制类型的字段布局**

CLR通过 `System.Runtime.InteropServices.StructLayout` 来控制字段布局。

- `LayoutKind.Sequential` CLR保持你的字段布局
- `LayoutKind.Auto` CLR自动排列字段（优化效果最好）
- `LayoutKind.Explicit` 利用偏移量在内存中显式排列字段

对于引用类型，默认是 `LayoutKind.Auto` . 对于值类型，默认是 `LayoutKind.Sequential` (因为认为其经常用于与非托管代码进行互操作)

```C#
using System.Runtime.InteropServiecs;
[StructLayout(LayoutKind.Auto)]
internal struct SomeVlType {...} //当创建的类型不与非托管代码进行互操作，覆盖CLR默认设定

//显式指定偏移量模拟union
//m_b m_x 在这个类型中的实例中相互重叠
[StructLayout(LayoutKind.Explicit)]
internal struct SomeValType {
	[Field0ffset(0)]
	private readonly Byte m_b;
	[FieldOffset(0)]
	private readonly Int16 m_x;
}

//需要注意的是一个引用类型和一个值类型相互重叠是不合法的（一个在栈一个在堆），即重叠字段需要存在公共字段
```

**装箱**即当某个方法的参数为一个引用类型，但传入为值类型时，会在堆分配内存空间（类型对象指针+同步块索引+实例空间）而后将值对象复制到对应的空间并返回堆上该对象的地址。

泛型的出现使得开发人员无需对集合中的项进行装箱/拆箱操作->堆中创建的对象少了->**减少了垃圾回收的次数**，从而使得性能得到大幅提升。（在创建一个引用实例的同时会强制执行垃圾回收操作）

**拆箱**即获取已装箱对象的各个字段的地址

在拆箱之后将字段包含的值复制到基于栈的值类型实例中（复制过程不算拆箱）

```	c#
public static void Main(){
    Int32 x = 5;
    Object o = x;
    Int16 y = (Int16)o;        //InvalidCastException，拆箱只允许转换回原来的类型
    Int16 y = (Int16)(Int32)o; //正确
}

public static void Main(){
    Point p;
    p.x = p.y = 1;
    Object o = p;   
    //将p的字段变成2要先拆箱再装箱
    //C++/CLI 允许直接对已装箱对象的未装箱部分进行操作，会方便得多
    p = (Point)o;
    p.2 = 2;
    o = p;
}
```

```C#
public static void Main(){
    Int32 v = 5;
    Object o = v;
    v = 123;
    Console.WriteLine(v + ", " + (Int32)o); 
    //2次装箱，因为WriteLine接受String参数，String调用concat方法，该方法接受三个对象作为参数
    //需要将v进行装箱，将（Int32）转型后的对象再次装箱
    Console.WriteLine(v.toString() + ", " + o); //无需装箱
    Console.WriteLine("{0},{1},{2}", v, v, v);  
    //装箱三次，每次装箱都会在堆生成一个对象， 建议手动装箱成o然后传o
}
```

判断一个值类型实例是否会被装箱，往往看他调用的方法是自身重写/实现的还是调用了基类（Object）的方法，同时如果作为参数，需要判断方法的参数是值类型还是引用类型，而后进行相应转换。 

P124 使用接口来更改已装箱值类型的字段（以及为什么不应该这么做）

**相等性和同一性**

Object提供的 `Equals` 方法检测的是同一性，往往需要重写，（记得重载==和 != ）思路如下：

1. 如果obj为null，就返回false
2. 如果this和obj实参引用同一对象就返回true，免去比较具有大量字段的对象
3. 如果this和obj的对象类型不同，直接返回false
4. 比较各个字段的值，不一样则返回false
5. 调用**基类的** `Equals` 方法，检测基类的属性相等性，如果返回false就返回false

```C#
public sealed class Program{
    public override Boolean Equals(Object obj){...}
}
```

如果需要检查同一性，应该调用 `ReferenceEquals` 而不应该使用 `==`

在重写 `Equals` 方法的同时，往往需要重写 `GetHashCode` 方法，因为在 `System.Collections.Hashtable`，`System.Collections.Generic.Dictionary` 以及其他类型的集合要求两个相同的对象具有相同的哈希码。

在重写 `GetHashCode` 之后，如果需要获取对象的唯一ID可以使用 `System.Runtime.CompilerServices` 命名空间中 `RuntimeHelpers` 提供的公共静态方法 `GetHashCode` 方法（重写的是继承自Object/ValueType的方法）

```C#
internal sealed class Point{
    private Int32 m_x, m_y;
    public override Int32 GetHashCode(){
        return m_x ^ m_y;
    }
}
```

C# 的 dynamic 类型需要加载额外的 dll ，同时dynamic需要作额外的工作，所以除非程序中大量使用dynamic语句，否则尽量使用显式转换。

## Chapter 6 类型和成员基础

### 类型可见性

`internal` ：只有同一个程序集内代码可见（默认设置）

`public` ：所有程序集内代码可见

通过 `System.Runtime.CompilerServices` 中的 `InternalsVisibleTo` 来标明友元程序集。 在一个程序集确认了自己的友元程序集后，友元程序集能访问该程序集的internal类型及这些类型的internal成员。

```C#
using System.Runtime.CompilerServices
[assembly:InternalsVisibleTo("Wintellect,PublicKey=123...90abc")]
```

C#编译器在编译友元程序集时，要求使用编译器开关 `/out:<file>`

C#编译器的 `/t:module` 开关来编译模块（而不是程序集）使用 `/moduleassemblyname:<string>` 告诉编译器该模块将成为哪个友元程序集的一部分。

### 成员可访问性

| CLR 术语            | C# 术语            | 描述                                                     |
| ------------------- | ------------------ | -------------------------------------------------------- |
| Private             | private            | 定义类型本身及嵌套类可见                                 |
| Family              | protected          | 自身、嵌套类型或在任意程序集中的派生类的方法可见         |
| Family and Assembly | 不支持             | 自身、嵌套类型或在同一程序集中的派生类可见               |
| Assembly            | internal           | 同一程序集中的类别可见                                   |
| Family or Assembly  | protected internal | 自身、嵌套类型、任意程序中的派生类、同一程序集中的类可见 |
| Public              | public             | 任意程序集任意方法可见                                   |

<font color=blue>一个派生类重写在它的基类中定义的一个成员时，C# 编译器要求原始成员和重写成员具备相同的可访问性，然而CLR则允许放宽成员的可访问性限制，但不允许收紧，因为CLR承诺派生类总是可以转换为基，并获取对基类方法的访问权.</font>

### 静态类

静态类的特点**

- 静态类必须直接从 `System.Object` 派生
- 静态类不能实现任何接口，因为只有实例才能调用接口的方法
- 静态类只能定义静态成员
- 静态类不能作为字段、方法参数或局部变量，因为他们都代表引用一个实例的变量

<font color=blue>使用关键字 `static` 定义一个类，将导致 C# 编译器将该类同时标记为 `abstract` 和 `sealed`</font>

### 分布类、结构和接口

**C# 提供分布类、结构和接口的功能，CLR 对此则一无所知**

`partial` 关键字告诉 C# 编译器，一个类、结构或接口的定义源码可能要分散到一个或多个源代码文件中，为了

1. **源代码控制。**`partial` 可以将类型的代码分散到多个源代码文件中，每个文件可以单独签出 (check out) 使多个程序员能够同时编辑类型。
2. **在同一个文件中，将一个类或结构分解成不同的逻辑单元。**即在一个源代码文件中反复声明同一个分部类型。然后再分部类型的每一个部分都实现一个功能并配以它全套字段、方法、属性、事件等。
3. **代码拆分。**比如应用VS窗体设计功能时，VS自动创建两个代码文件，一个用于用户的代码，一个用于设计器生成的代码。防止用户不小心编辑了设计器的代码造成设计器行为异常。

### 组件、多态和版本控制

**组件软件编程 (Component Software Programming, CSP)** : 组件（程序集）有自己的标识（名称、版本、语言文化和公钥）、文档化、具有安全权限、清楚指明以来的组件等。通过组件进行编程而不是几个人完成应用程序的全部代码。

**版本号**：主版本号 (major version).次版本号 (minor version).内部版本号 (build number).修订号 (revision)

major/minor 部分通常代表程序集的一个连续、稳定的功能集，而 build/revision 部分通常代表对这个功能集的一次维护

如对于 2.7.0.0 的一次bug修复，其版本应只改动 build/revision，如2.7.1.34

对于一个全新的组件，其 major/minor 往往不同，如3.0.0.0

### CLR调用虚方法、属性和事件

CLR允许一个类型定义多个同名方法，只要每个方法都有一组不同的参数**或不同的返回类型**。（与C++不同）

**IL代码来调用方法**

- <font size=4>call</font>

  - 这个IL指令可调用静态方法、实例方法和虚方法。
  - 其默认引用的对象不为 null。
  - 同时其也经常用于以非虚方式调用一个虚方法。

  ```c#
  internal class SomeClass{
      public override String ToString(){
          ...
          //使用base来调用基类方法
      	return base.ToString()
      }
  }
  //按常理，C#编译器会采用callvirt来调用ToString
  //但是如果callvirt，则采用虚方式调用，即base.ToString会实行多态，递归调用了自身的ToString
  //自身继续return base.ToString()递归执行直到线程栈溢出
  ```

- <font size=4>callvirt</font>

  - 这个IL指令可调用实例方法和虚方法，但**不能调用静态方法**。
  - 编译这个调用时，JIT 编译器会生成代码来验证变量的值不是 null，因此性能较 call 相对较弱。

<font color=blue>C# 编译器默认使用 callvirt 调用实例方法和虚方法，特例如上</font>

设计一个类型时候应减少虚方法的数量

- 虚方法的速度比调用非虚方法慢（要检验调用对象是否为null）
- JIT 编译器不能内嵌（inline）虚方法，进一步影响性能
- 虚方法使组件的版本控制更加脆弱
- 定义一个基类时，常需要提供一组重载的简便方法，如果希望这些方法时多态的，最好的方法是使最复杂的方法成为虚方法，使所有的重载版本成为非虚方法

作者提出了closed关键字的设想，在该关键字的修饰下，子类只能访问 public 成员【目前并未实现】

<font color=blue>Tips：当一个算法是实现变得复杂，定义一些辅助类来封装独立的功能，如果辅助类只有一个超类型使用，则在超类型中嵌套辅助类，不仅能限制辅助类的调用范围，还能使辅助类调用超类型的参数</font>

### 版本控制·虚方法的处理

对于派生类与基类同名的方法，

- 如果派生类的方法是一个与基类毫不相干的方法，则使用 `new` 关键字修饰
- 如果二者相关，派生类是基类虚方法的重写，则使用 `override` 关键字

## Chapter 7 常量和字段

### 常量

常量可以是基元类型，也可以是非基元类型（其值只能为 null ）

但不能获取常量的地址，也不能以传引用的方式传递常量，这意味着常量没有很好的跨程序集版本控制特性。

常量的值是直接嵌入代码的，因此无需再运行时分配内存。

```C#
using System;
public sealed class SomeLibraryType{
    // C#不允许为常量指定static关键字，常量总是隐式为static
    public const Int32 MaxEntriesInList = 50;
}
```

```C#
using System;
public sealed class Program{
    public static void Main(){
        Console.WriteLine(SomeLibraryType.MaxEntriesInList);
    }
}
```

注意到 Program 中调用的 `MaxEntriesInList` 是一个常量，则当 program 编译为应用程序集时，50便嵌入了应用程序及代码，运行时根本不需要加载 DLL 程序集。

在此情况下，应用程序要获得新值，需要重新编译。如果希望运行时从一个程序集提取另一个程序集中的值，则不应该使用常量，而应该使用 `readonly` 字段。

### 字段

| CLR 术语 | C# 术语  | 说明                                                         |
| -------- | -------- | ------------------------------------------------------------ |
| Static   | static   |                                                              |
| Instance | 默认     |                                                              |
| InitOnly | readonly | <font color=blue>只能由一个构造器的方法写入</font>           |
| Volatile | volatile | 看到访问这种字段的代码，编译器、CLR或硬件就不会执行一些“线程不安全”的优化措施 |

**定义常量的正确姿势**：`public static readonly Int32 MaxEntriesInList = 50;`

使用 `readonly` 修饰后，代码在运行时将会提取DLL程序集中的值而不是嵌入到自身应用程序集中，这在构造器方法中实现，因此在构造器方法中修改自身的 `static readonly` 属性值也是正确的。

<font color=blue>当某个字段是引用类型，且该字段标为 readonly 时，那么不可改变的是引用，而不是字段引用的对象。</font>

## Chapter 8 方法

### 实例构造器和类（引用类型）

<font color=blue>如果没有显式定义任何构造器，C# 编译器将定义一个默认的（无参）构造器。但是在调用时需要显示调用无参构造器（new 一个），否则会报错。</font>

不要在构造器中调用会影响构造对象的任何虚方法，因为如果基类构造方法中调用的虚方法在派生类中进行了重写，但调用时派生类并未初始化，将出现不可预期的行为。

```C#
internal sealed class SomeType{
    private Int32 m_x = 5;
    
    public SomeType(){...}
    public SomeType(Int32 x){...}
}
```

对于上面的代码，`private Int32 m_x = 5` 会在<font color=blue>每个</font>构造器的代码开始位置插入，同时即便没有代码显式初始化变量，变量也会被初始化为0/null。

因此如果有几个已初始化字段和大量重载的构造器方法，往往创建单个构造器来执行这些代码的公共初始化，然后让其他构造器显式调用这个公共初始化构造器。

```C#
internal sealed class SomeType{
    //注意没有在此初始化
    private Int32 m_x;
    private String m_s;
    
    public SomeType(){
        m_x = 5;
        m_s = "Hello World";
    }
    //用this调用公共初始化构造器
    public SomeType(Int32 x):this(){
        m_x = x;
    }
}
```

### 实例构造器和结构（值类型）

<font color=blue>C# 不会为值类型提供默认无参构造器，字段都默认初始化为0/null，此时可以直接调用，无需显式初始化，不会报错。</font>

事实上，值类型的构造器只有在显式调用时才会执行，即 new 一个实例的时候。

<font color=red>需要注意的是，为了防止程序员想当然，认为会调用无参构造器，C# 不允许为值类型定义无参构造器 ( CLR 允许)，因此 C# 也就不能使用前述 `private Int32 m_x = 5;` 这样的初始化语句。</font>

```C#
internal struct SomeValType{
    private Int32 m_x,m_y;
    //会报错，因为没有对m_y赋值
    public SomeValType(Int32 x){
        m_x = x;
    }
}
```

```C#
internal struct SomeValType{
    private Int32 m_x,m_y;
    public SomeValType(Int32 x){
        //this表示值类型本身的一个实例，用于将所有字段初始化为0/null
        this = new SomeValType();
        m_x = x;  //用x来覆盖原来的m_x
    }
}
```

如上代码，在值类型中，this 表示值类型本身的一个实例，而引用类型中，this 被认为是只读的，所以无法对它进行赋值。

### 类型构造器

类型构造器 (type constructor) 也被称为静态构造器 (static constructor)、类构造器 (class constructor) 或者类型初始化器 (type initializer)。其可应用于接口 (C#不允许)、引用类型和值类型。

在 IL 代码中标记为 .cctor (class constructor)

```C#
internal sealed class SomeRefType{
    static SomeRefType(){
        //SomeRefType被首次访问时，执行此处代码
    }
}
```

- 类型默认没有类型构造器，即使有也只能定义一个。

- 类型构造器用 `static` 修饰且没有参数。其默认为 private 但不能显式用 private 标记，防止除CLR外的人员调用它。
- 类型构造器只能访问 `static` 字段，其常规用途便是初始化这些字段。
- 由于类型构造器在每个AppDomain中只执行一次且是线程安全的，所以非常适合用于在类型构造器中初始化类型需要的任何单实例 (Singleton) 对象。

<font color=blue>Notice：不要为值类型创建类型构造器，因为值类型的类型构造器有时不会被调用。</font>

虽然无法为实例字段使用内联字段初始化语法，如之前提到的 `private Int32 m_x = 5;`，但可以为静态字段使用，也就是如下代码是合法的。

```C#
internal sealed class SomeType{
    private static Int32 s_x = 5;  //静态字段
}
//上述代码下实际上编译器自动生成一个类型构造器
internal sealed class SomeType{
    private static Int32 s_x;
    static SomeType(){ s_x = 5; }
}
```

---

如果需要在卸载一个类型时执行一段代码。首先需要清楚，只有在 AppDomain 卸载时，类型才会卸载，而 AppDomain 卸载时，类型对象将不可达，垃圾回收器会回收对象的内存。达成目标的方法不是添加一个静态 `Finalize` 方法，而应该向 `System.AppDomain` 类型的 `DomainUnload` 登记一个回调方法。

---

对于何使调用类型构造器，分为 `Precise` (刚好访问时调用) 和 `BeforeFieldInit` (在访问之前的任意时间调用)

Precise 往往是类型自身显式定义了类型构造器时采用 (因为显式构造器可能包含具有副作用的代码，需要精确拿捏运行时间)，而 BeforeFieldInit 则是未显示定义类型构造器时采用。<font color=blue>BeforeFieldInit 会比 Precise 的性能好很多。</font>

### 操作符重载方法

C# 编译器要求操作符重载方法至少有一个参数的类型与当前定义这个方法的类型相同

```C#
public sealed class Complex{
    public static Complex operator+(Complex c1, Complex c2){...}
}
```

对于常用的操作符，往往会有一个特殊方法名，以及推荐的相容于 CLS 的方法名。（P177)

如对于 + 而言，特殊方法名为 `op_Addition`。上述代码重载了 +，则编译器为 `op_Addition` 生成元数据方法定义项，并为其设置了 `specialname` 标志。当编译器看到代码中出现 + 时，会检查是否有名为 `op_Addition` 的 `specialname` 方法，且该方法的参数兼容于操作数的类型，满足则调用，否则报错。

### 转换操作符方法

用于不同类型之间的转换

```C#
public sealed calss Rational{
    //构造器，通过Int32构造Rational
    public Rational(Int32 num){...}
    //实例方法
    public Int32 ToInt32(){...}
    //由Int32隐式构造并返回Rational
    public static implicit operator Rational(Int32 num){
        return new Ration(num);
    }
    //有Rational显式构造并返回Int32
    public static explicit operator Int32(Rational r){
        return new r.ToInt32();
    }
}
```

只有在转换不损失精度或数量级的情况下才能定义隐式转换操作符。

### 扩展方法

```C#
public static class StringBuilderExtensions{
    public static Int32 IndexOf(StringBuilder sb, Char value){...}
}

StringBuilder sb = new StringBuilder("Hello World!");
Int32 index = StringBuilderExtensions.IndexOf(sb.Replace('!','.'),'.')
```

对于上述代码，

1. 开发人员不一定知道 `StringBuilderExtensions` 类的存在
2. 代码没有反映在 `StringBuilder` 对象上的操作顺序，应该先看到 replace 再看到 indexof
3. 影响了对于代码的理解，降低了代码的可维护性

扩展方法 (extension method)：它允许你定义一个静态方法，并用实例方法的语法调用它，即可以将 `StringBuilderExtensions` 的 `IndexOf` 看作是 `StringBuilder` 自身的方法来调用

```c#
//这个类是静态类，IndexOf是静态方法
public static class StringBuilderExtensions{
    //注意在StringBuilder参数前加上了this，
    public static Int32 IndexOf(this StringBuilder sb, Char value){...}
}

StringBuilder sb = new StringBuilder("Hello World!");
Int32 index = sb.Replace('!','.').IndexOf('.');
```

编译器看到 `IndexOf` 方法时会先检查是否有匹配的实例方法，没有就会检查是否有静态类定义了相应的静态方法且第一个参数和调用该方法的类型相匹配，且这个参数用 `this` 关键字标识。

<font color=blue>VS 提供智能感知窗口，扩展方法的图标有一个向下的小箭头</font>

---

- 扩展方法不会进行 null 值检查。

- 可以为接口类型定义扩展方法。

```c#
public static void ShowItems<T>(this IEnumerable<T> collection){...}
```

如上代码，只要该类型实现了 `IEnumerable<T>` 接口，就都能调用这个扩展方法

### 分部方法

```c#
internal class Base{
    private String m_name;
    protected virtual void OnNameChanging(String value){...}
    public String Name{
        get { return m_name; }
        set {
            OnNameChanging(value.ToUpper());
            m_name = value;
        }
    }
}

//开发人员生成的代码，存储来另一个源代码文件中
internal class Drived : Base{
    protected override void OnNameChanging(String value){...}
}
```

上述代码存在的问题

- 类型必须是一个非密封的类，同时不能是静态方法，否则无法继承或重写
- 定义了一个 Derived 类型却只为重写一个方法，浪费系统资源

采用分部方法可以解决上述问题

```c#
//注意该类密封，同时注意partial关键字
internal sealed partial class Base{
    private String m_name;
    
    //分部方法的声明
    partial void OnNameChanging(String value);
    
    public String Name{
        get { return m_name; }
        set {
            OnNameChanging(value.ToUpper());
            m_name = value;
        }
    }
}

//开发人员生成的代码，存储来另一个源代码文件中
//注意类和方法都用了partial关键字
internal sealed partial class Base{
    partial void OnNameChanging(String value){...}
}
```

```c#
//如果分部方法没有实现，那么编译器便生成如下代码
//即抹除OnNameChanging的痕迹
internal sealed partial class Base{
    private String m_name;   
    public String Name{
        get { return m_name; }
        set {
            m_name = value;
        }
    }
}
```

**规范和原则**（具体见P190）

- 分布方法的返回类型只能是 void，任何参数都不能用 out 修饰，是因为方法在运行时可能不存在，所以不能将变量初始化为可能要返回的东西。因此可以用 ref 、泛型方法、实例或静态方法，因为无需初始化。
- 方法总被视为 private 但是不能显式标记为 private，就像类型构造器一样。