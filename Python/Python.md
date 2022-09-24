# Python

## 1 Python基础

### 1.1 注释

Python3注释可以确保对模块, 函数, 方法和行内注释使用正确的风格，有专门的符号和格式，有单行与多行的区别。

Python 中的注释有单行注释和多行注释：

Python 中单行注释以 # 开头，例如：

```python
# 这是一个注释
print("Hello, World") 
```

多行注释用三个单引号（'''）或者三个双引号（"""）将需要注释的内容囊括起来，例如:

1、单引号（'''）

```python
'''
这是多行注释，用三个单引号
这是多行注释，用三个单引号 
这是多行注释，用三个单引号
'''
print("Hello, World!") 
```

2、双引号（"""）

```python
"""
这是多行注释，用三个双引号
这是多行注释，用三个双引号 
这是多行注释，用三个双引号
"""
print("Hello, World!") 
```

>   三引号实际上是多行长字符串的声明方式，也作为python代码文档使用（类似javadoc）。

### 1.2 数据类型

在 Python 中，变量就是变量，它没有类型，我们所说的"类型"是变量所指的内存中对象的类型。 

Python 3 中有六个标准的数据类型：

-   Numbers（数字）
-   String（字符串）
-   List（列表）
-   Tuple（元组）
-   Sets（集合）
-   Dictionaries（字典）

#### 1.2.1 Numbers

Python 3 支持 int（整型）、float（浮点型）、bool（布尔型）、complex（复数）。

数值类型的赋值和计算都是很直观的，就像大多数语言一样。内置的 type() 函数可以用来查询变量所指的对象类型。

```shell
>>> a, b, c, d = 20, 5.5, True, 4+3j
>>> print(type(a), type(b), type(c), type(d))
<class 'int'><class 'float'><class 'bool'><class 'complex'>
```

此外还可以用isinstance来判断：

```shell
>>>a=111
>>>isinstance(a,int)
True
>>>
```

isinstance和type的区别在于：

-   type（）不会认为子类是一种父类类型。
-   isinstance（）会认为子类是一种父类类型。

```shell
>>> class A:
...     pass
... 
>>> class B(A):
...     pass
... 
>>> isinstance(A(), A)
True
>>> type(A()) == A 
True
>>> isinstance(B(), A)
True
>>> type(B()) == A
False
```

注意：在 Python2 中是没有布尔型的，它用数字 0 表示 False，用 1 表示 True。到 Python3 中，把 True 和 False 定义成关键字了，但它们的值还是 1 和 0，它们可以和数字相加。

当指定一个值时，Number 对象就会被创建：

```
var1 = 1
var2 = 10
```

也可以使用 del 语句删除一些对象引用。

del 语句的语法是：

```
del var1[,var2[,var3[....,varN]]]
```

可以通过使用 del 语句删除单个或多个对象。例如：

```shell
del var
del var_a, var_b
```

数值运算：

```shell
>>> 5 + 4  # 加法
9
>>> 4.3 - 2 # 减法
2.3
>>> 3 * 7  # 乘法
21
>>> 2 / 4  # 除法，得到一个浮点数
0.5
>>> 2 // 4 # 除法，得到一个整数
0
>>> 17 % 3 # 取余 
2
>>> 2 ** 5 # 乘方
32
```

注意：

-   1、Python 可以同时为多个变量赋值，如 a, b = 1, 2。
-   2、一个变量可以通过赋值指向不同类型的对象。
-   3、数值的除法（/）总是返回一个浮点数，要获取整数使用`//`操作符。
-   4、在混合计算时，Python 会把整型转换成为浮点数。

#### 1.2.2 String

Python 中的字符串 str 用单引号(' ')或双引号 (" ") 括起来，同时使用反斜杠 (\) 转义特殊字符。

```shell
>>> s = 'Yes,he doesn\'t'
>>> print(s, type(s), len(s))
Yes,he doesn't  <class 'str'> 14
```

如果你不想让反斜杠发生转义，可以在字符串前面添加一个 r，表示原始字符串：

```shell
>>> print('C:\some\name')
C:\some
ame
>>> print(r'C:\some\name')
C:\some\name
```

另外，反斜杠可以作为续行符，表示下一行是上一行的延续。还可以使用"""..."""或者'''...'''跨越多行。

字符串可以使用 + 运算符串连接在一起，或者用 * 运算符重复：

```shell
>>> print('str'+'ing', 'my'*3)
string mymymy
```

Python 中的字符串有两种索引方式，第一种是从左往右，从 0 开始依次增加；第二种是从右往左，从 -1 开始依次减少。

注意，没有单独的字符类型，一个字符就是长度为 1 的字符串。

```shell
>>> word = 'Python'
>>> print(word[0], word[5])
P n
>>> print(word[-1], word[-6])
n P
```

还可以对字符串进行切片，获取一段子串。用冒号分隔两个索引，形式为变量[头下标:尾下标]。

截取的范围是前闭后开的（头下标取，尾下标不取），并且两个索引都可以省略：

```shell
>>> word = 'ilovepython'
>>> word[1:5]
'love'
>>> word[:]
'ilovepython'
>>> word[5:]
'python'
>>> word[-10:-6]
'love'
```

与 C 字符串不同的是，Python 字符串不能被改变。**向一个索引位置赋值，比如 word[0] = 'm' 会导致错误**。

注意：

-   1、反斜杠可以用来转义，使用 r 可以让反斜杠不发生转义。
-   2、字符串可以用 + 运算符连接在一起，用 * 运算符重复。
-   3、Python 中的字符串有两种索引方式，从左往右以 0 开始，从右往左以 -1 开始。
-   4、Python 中的字符串不能改变。

#### 1.2.3 List

List（列表） 是 Python 中使用最频繁的数据类型。

列表是写在方括号之间、用逗号分隔开的元素列表。列表中元素的类型可以不相同：

```shell
>>> a = ['him', 25, 100, 'her']
>>> print(a)
['him', 25, 100, 'her']
```

和字符串一样，列表同样可以被索引和切片，列表被切片后返回一个包含所需元素的新列表。详细的在这里就不赘述了。

列表还支持串联操作，使用 + 操作符：

```shell
>>> a = [1, 2, 3, 4, 5]
>>> a + [6, 7, 8]
[1, 2, 3, 4, 5, 6, 7, 8]
```

与 Python 字符串不一样的是，列表中的元素是可以改变的：

```shell
>>> a = [1, 2, 3, 4, 5, 6]
>>> a[0] = 9
>>> a[2:5] = [13, 14, 15]
>>> a
[9, 2, 13, 14, 15, 6]
>>> a[2:5] = []   # 删除
>>> a
[9, 2, 6]
```

List 内置了有很多方法，例如 append()、pop() 等等，这在后面会讲到。

注意：

-   1、List 写在方括号之间，元素用逗号隔开。
-   2、和字符串一样，List 可以被索引和切片。
-   3、List 可以使用 + 操作符进行拼接。
-   4、List 中的元素是可以改变的。

#### 1.2.4 Tuple

元组（tuple）与列表类似，不同之处在于元组的元素不能修改。元组写在小括号里，元素之间用逗号隔开。

元组中的元素类型也可以不相同：

```shell
>>> a = (1991, 2014, 'physics', 'math')
>>> print(a, type(a), len(a))
(1991, 2014, 'physics', 'math') <class 'tuple'> 4
```

元组与字符串类似，可以被索引且下标索引从 0 开始，也可以进行截取/切片（看上面，这里不再赘述）。

其实，可以把字符串看作一种特殊的元组。

```
>>> tup = (1, 2, 3, 4, 5, 6)
>>> print(tup[0], tup[1:5])
1 (2, 3, 4, 5)
>>> tup[0] = 11  # 修改元组元素的操作是非法的
```

虽然 tuple 的元素不可改变，但它可以包含可变的对象，比如 list 列表。

构造包含 0 个或 1 个元素的 tuple 是个特殊的问题，所以有一些额外的语法规则：

```
tup1 = () # 空元组
tup2 = (20,) # 一个元素，需要在元素后添加逗号
```

另外，元组也支持用 + 操作符：

```shell
>>> tup1, tup2 = (1, 2, 3), (4, 5, 6)
>>> print(tup1+tup2)
(1, 2, 3, 4, 5, 6)
```

string、list 和 tuple 都属于 sequence（序列）。

注意：

-   1、与字符串一样，元组的元素不能修改。
-   2、元组也可以被索引和切片，方法都是一样的。
-   3、注意构造包含 0 或 1 个元素的元组的特殊语法规则。
-   4、元组也可以使用 + 操作符进行拼接。

#### 1.2.5 Sets

集合（set）是一个无序不重复元素的集。

基本功能是进行成员关系测试和消除重复元素。

可以使用大括号 或者 set() 函数创建 set 集合，注意：创建一个空集合必须用 set() 而不是 { }，因为{ }是用来创建一个空字典。

```shell
>>> student = {'Tom', 'Jim', 'Mary', 'Tom', 'Jack', 'Rose'}
>>> print(student)   # 重复的元素被自动去掉
{'Jim', 'Jack', 'Mary', 'Tom', 'Rose'}
>>> 'Rose' in student  # membership testing（成员测试）
True
>>> # set可以进行集合运算
... 
>>> a = set('abracadabra')
>>> b = set('alacazam')
>>> a
{'a', 'b', 'c', 'd', 'r'}
>>> a - b     # a和b的差集
{'b', 'd', 'r'}
>>> a | b     # a和b的并集
{'l', 'm', 'a', 'b', 'c', 'd', 'z', 'r'}
>>> a & b     # a和b的交集
{'a', 'c'}
>>> a ^ b     # a和b中不同时存在的元素
{'l', 'm', 'b', 'd', 'z', 'r'}
```

#### 1.2.6 Dictionaries

字典（dictionary）是 Python 中另一个非常有用的内置数据类型。

字典是一种映射类型（mapping type），它是一个无序的键值对（key-value）集合。

关键字（key）必须使用不可变类型，也就是说list和包含可变类型的 tuple 不能做关键字。

在同一个字典中，关键字（key）必须互不相同。

```shell
>>> dic = {}  # 创建空字典
>>> tel = {'Jack':1557, 'Tom':1320, 'Rose':1886}
>>> tel
{'Tom': 1320, 'Jack': 1557, 'Rose': 1886}
>>> tel['Jack']   # 主要的操作：通过key查询
1557
>>> del tel['Rose']  # 删除一个键值对
>>> tel['Mary'] = 4127  # 添加一个键值对
>>> tel
{'Tom': 1320, 'Jack': 1557, 'Mary': 4127}
>>> list(tel.keys())  # 返回所有key组成的list
['Tom', 'Jack', 'Mary']
>>> sorted(tel.keys()) # 按key排序
['Jack', 'Mary', 'Tom']
>>> 'Tom' in tel       # 成员测试
True
>>> 'Mary' not in tel  # 成员测试
False
```

构造函数 dict() 直接从键值对 sequence 中构建字典，当然也可以进行推导，如下：

```shell
>>> dict([('sape', 4139), ('guido', 4127), ('jack', 4098)])
{'jack': 4098, 'sape': 4139, 'guido': 4127}

>>> {x: x**2 for x in (2, 4, 6)}
{2: 4, 4: 16, 6: 36}

>>> dict(sape=4139, guido=4127, jack=4098)
{'jack': 4098, 'sape': 4139, 'guido': 4127}
```

另外，字典类型也有一些内置的函数，例如 clear()、keys()、values() 等。

注意：

-   1、字典是一种映射类型，它的元素是键值对。
-   2、字典的关键字必须为不可变类型，且不能重复。
-   3、创建空字典使用 { }。

### 1.3 标识符和关键字

#### 1.3.1 标识符

-   第一个字符必须是字母表中字母或下划线'_'。
-   标识符的其他的部分有字母、数字和下划线组成。
-   标识符对大小写敏感。

在 Python 3中，非 ASCII 编码的标识符也是允许的了。

#### 1.3.2 Python 保留字

保留字即关键字，我们不能把它们用作任何标识符名称。Python 的标准库提供了一个关键词模块，我们可以使用它来查看当前版本的所有保留字：

```
>>> import keyword
>>> keyword.kwlist
['False', 'None', 'True', '__peg_parser__', 'and', 'as', 'assert', 'async', 'await', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
```

### 1.4 类型转换

1.5 运算符

1.6 输入输出

1.7 流程控制语句

1.8 数据类型高级

1.9 函数

1.10 文件

1.11 异常