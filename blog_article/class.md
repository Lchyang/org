
# Table of Contents

1.  [类与对象深度技术案例进阶训练](#orgc7adbb8)
    1.  [如何派生内置不可变类型并修改其实例化行为？](#org71fe24f)
    2.  [如何为创建大量的实例节省内存？](#org1de2dfa)
    3.  [如何让对象支持上下文管理？](#orgf87a809)
    4.  [如何创建可管理的对象属性？](#orgf4cd45a)
    5.  [如何让类支持比较操作？](#org007f51b)
    6.  [如何使用描述符对实例属性做类型检查？](#org141cbb5)
    7.  [如何在环装数据结构中管理内存？](#org5e6fc4c)
    8.  [如何通过实例方法名字的字符串调用方法](#org644318f)


<a id="orgc7adbb8"></a>

# 类与对象深度技术案例进阶训练


<a id="org71fe24f"></a>

## 如何派生内置不可变类型并修改其实例化行为？

案例：我们想自定义一种新类型的元组，对于传入的可迭代对象，我们只保留其中的int类
型且值大于0的元素，例如：IntTuple([1,-1,'abc',6,['x','y'],3])=>(1,6,3)
要求IntTuple是内置tuple的子类，如何实现

    class IntTuple(tuple):
        def __new__(cls, iterable): #基于__init__中不可改变所以要改变__new__
            g = (x for x in iterable if isinstance(x, int) and x > 0)
            return super(IntTuple, cls).__new__(cls, g)
        def __init__(self, iterable):
            # before 当self实例传入__init__时 tuple已经创建了在这也不能改变
            super(IntTuple, self).__init__(iterable)
            # after self是tuple的实例 tuple是不可变对象 在这改变是不可能的
    
    t = IntTuple([1, -1, 'abc', 6, ['x', 'y'],3])
    print t


<a id="org1de2dfa"></a>

## 如何为创建大量的实例节省内存？

实际案例：
某网络游戏中，定义了玩家类Player（id，name, status,..)每有一个在线的玩家，在服
务器程序内则有一个Player的实例，当在线人数很多时，将产生大量的实例。（如百万级）
如何降低这些大量的内存开销？
解决方案： 定义类的\_<sub>slots</sub>\_<sub>属性</sub>，它是用来声明实例属性名字的列表。

    
    class Player():
        def __init__(self, uid, name, status=0, level=1):
            self.uid = uid
            self.name = name
            self.stat = status
            self.level = level
    
    class Player2():
        __slots__ = ['uid', 'name', 'stat', 'level']
        def __init__(self, uid, name, status=0, level=1):
            self.uid = uid
            self.name = name
            self.stat = status
            self.level = level
    
    p1 = Player('0001', 'Jim')
    p2 = Player2('0001', 'Jim')
    print(set(dir(p1)) - set(dir(p2)))
    # 输出
    # In [2]: {'__weakref__', '__dict__'}
    import sys
    print(sys.getsizeof(p1.__dict__))
    print(sys.getsizeof(p2.__slots__))
    # 112
    # 96

为了节省内存通过定义\_<sub>slots</sub>\_<sub>方法禁止</sub>\_<sub>dict</sub>\_<sub>动态绑定</sub>。


<a id="orgf87a809"></a>

## 如何让对象支持上下文管理？

实现上下文管理协议需要定义实例的\_<sub>enter</sub>\_\_,\_<sub>exit</sub>\_<sub>方法</sub>。他们分别在with开始和结
束时被调用。


<a id="orgf4cd45a"></a>

## 如何创建可管理的对象属性？

    from math import pi
    class Circle:
        def __init__(self, radius):
            self.radius = radius # 直接利用类属性访问的话 不能确保是数字不能进行操作。
    
        def getRadius(self):
            return self.radius
    
        def setRadius(self, value):
            if not isinstance(value, (int, float)):
                raise ValueError('wrong type.')
            self.radius = float(value)
    
        def getArea(self):
            return self.radius **2 * pi
    
        R = property(getRadius, setRadius) # 可以传三个值创建访问和删除
    
    c = Circle(3.2)
    print(c.R)
    c.R = ''
    print(c.R)

利用property 可以实现c.R的访问属性 访问安全设计灵活。


<a id="org007f51b"></a>

## 如何让类支持比较操作？

    from functools import total_ordering
    from abc import abstractmethod
    
    @total_ordering
    class Shape:
        @abstractmethod #实现一个抽象接口子类都需要实现这个接口
        def area(self):
            pass
    
        def __lt__(self, obj): # 通过 __lt__和 __eq__ 和其他的逻辑组合实现全部的大小比较。
            print('in __lt__') # 通过 @total_ordering 装饰器实现
            if not isinstance(obj, Shape):
                raise TypeError('obje is not Shape')
            return self.area() < obj.area()
    
        def __eq__(self, obj):
            print ('in __eq__')
            if not isinstance(obj, Shape):
                raise TypeError('obj is not Shape')
            return self.area() == obj.area()
    
    class Rectangle(Shape):
        def __init__(self, w, h):
            self.w = w
            self.h = h
    
        def area(self):
            return self.w * self.h
    
    class Circle(Shape):
        def __init__(self, r):
            self.r = r
        def area(self):
            return self.r ** 2 * 3.14

两个类的实例是不支持大小比较的 通过复写\_<sub>lt</sub>\_\_ \_<sub>eq</sub>\_<sub>等方法</sub> 可以实现大小比较


<a id="org141cbb5"></a>

## 如何使用描述符对实例属性做类型检查？

实际案例：
在某项目中，我们实现一些类，并希望能像静态语言那样（c, c++, java)对他们的实例属
性做检查。
p = Person()
p.name = 'bob' #必须是str
p.age = 12 # 必须是int
p.height = 1.83 # 必须是float

解决方案：
使用描述符来实现需要类型检查的属性：分别实现\_<sub>get</sub>\_\_,\_<sub>set</sub>\_\_, \_<sub>delete</sub>\_<sub>方法</sub>，
在\_<sub>set</sub>\_<sub>内使用isinstance函数做类型检查</sub>

      class Arrt:
          def __init__(self, name, type_):
              self.name = name
              self.type_ = type_
    
          def __get__(self, instance, cls):
              return instance.__dict__[self.name]
    
          def __set__(self, instance, value):
              if not isinstance(value, self.type_):
                  raise TypeError('expected an %s' % self.type_)
              instance.__dict__[self.name] = value
    
          def __delete__(self, instance):
              del instance.__dict__[self.name]
    
    
      class person:
          name = Arrt('name', str)
          age = Arrt('age', int)
          height = Arrt('height', float)
    
    
      p = person()
      p.name = 'bob'
      print(p.name)
      p.age = '12'
    
    output
    In [17]: bob
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-17-6e8dddb352e3> in <module>()
    ----> 1 import codecs, os;__pyfile = codecs.open('''/var/folders/cb/bmq2h1x5559bztw7_k6q6h3r0000gn/T/py20879h9R''', encoding='''utf-8''');__code = __pyfile.read().encode('''utf-8''');__pyfile.close();os.remove('''/var/folders/cb/bmq2h1x5559bztw7_k6q6h3r0000gn/T/py20879h9R''');exec(compile(__code, '''/Users/lichunyang/org/python_base/python_advance.org[*Org Src python_advance.org[ python ]*]''', 'exec'));
    
    ~/org/python_base/python_advance.org[*Org Src python_advance.org[ python ]*] in <module>()
    
    ~/org/python_base/python_advance.org[*Org Src python_advance.org[ python ]*] in __set__(self, instance, value)
    
    TypeError: expected an <class 'int'>


<a id="org5e6fc4c"></a>

## 如何在环装数据结构中管理内存？


<a id="org644318f"></a>

## 如何通过实例方法名字的字符串调用方法

    from lib1 import Circle #假设三个类中计算面积使用的方法名都不相同
    from lib2 import Triangle #('area','getArea','get_area)
    from lib3 import Rectangle
    
    def getArea(shape):
        for name in ['area', 'getArea', 'get_area']:
            f = getattr(shape, name, None)
            if f:
                return f()
    
    
    shape1 = Circle(2)
    shape2 = Triangle(3, 4, 5)
    shape3 = Rectangle(6, 4)
    
    shapes = [shape1, shape2, shape3]
    print(map(getArea, shapes))

