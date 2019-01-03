
# Table of Contents

1.  [python 高级编程和异步IO并发编程](#org85df6c6)
    1.  [python 一切皆对象](#orgd712c81)
        1.  [python 一切皆对象](#org8cbc4d1)
        2.  [class object type 的关系](#org2f0afb7)
        3.  [python 常见的内置类型](#org96f4467)
    2.  [python的魔法函数](#org606acfa)
        1.  [什么是魔法函数](#org8d55d62)
        2.  [python数据模型对python的影响](#org4b0bc3a)
        3.  [魔法函数一览](#orge6c286d)
    3.  [深入类和对象](#org09c4777)
        1.  [鸭子类型和多态](#org4b2c804)
        2.  [抽象基类（abc模块）](#org079007b)
        3.  [isinstance和type的区别](#org984a9d0)
        4.  [类变量和实例变量的区别](#org46dc3b7)
        5.  [静态方法类方法以及实例方法以及参数](#orgebdc87e)
        6.  [python类的私有属性](#orgdf3733b)
        7.  [python的自省机制](#org83b68a0)
        8.  [super真的是调用父类吗？](#org6ee4d1c)
        9.  [python中的mixin](#org6bb9275)
        11. [使用contextlib简化上下文管理器](#orgf0f7b16)
        10. [python中的with语句](#org01c7301)
    4.  [自定义序列类](#org6189f6f)
        1.  [序列类型分类](#org61d6735)
        2.  [list 中的 + += extend append 的区别](#orgfacf8eb)
        3.  [实现可切片的对象](#orgbf61833)
        4.  [bisect维护已排序序列](#org738ea99)
        5.  [什么时候应该使用列表，什么时候不使用列表](#org2ed912d)
        6.  [列表推导式，生成器，字典推导式](#orgfb3a880)
    5.  [深入python的set和dict](#orgfed5987)
        1.  [dict常用方法](#org3affb20)
        2.  [dict子类](#org294c5b1)
        3.  [set和frozenset](#orgdea16ba)
        4.  [dict和set实现原理](#orgc71e349)
    6.  [对象引用、可变性和垃圾回收](#orga415873)
        1.  [python中的变量是什么？](#orgcf2f30a)
        2.  [python的垃圾回收和del](#org66c8993)
        3.  [一个典型的错误](#orgd260607)
    7.  [元类编程](#org42573ef)
        1.  [property动态属性](#org3e4a694)
        2.  [\_<sub>getattr</sub>\_\_, <span class="underline"><span class="underline">getattribute</span></span> 区别](#orgf7ffc83)
        3.  [属性描述符和属性查找过程](#org331944f)
        4.  [\_<sub>new</sub>\_\_,\_<sub>init</sub>\_<sub>的区别</sub>](#orga07fe2e)
        5.  [自定义元类](#org06b2b4b)
    8.  [python Socket编程](#org2f45ca6)
        1.  [socket client 实现通信](#org9193a46)


<a id="org85df6c6"></a>

# python 高级编程和异步IO并发编程


<a id="orgd712c81"></a>

## python 一切皆对象


<a id="org8cbc4d1"></a>

### python 一切皆对象

1.  函数和类也是对象，属于python的一等公民

    1、赋值给一个变量
    2、可以添加到集合对象
    3、可以作为参数传递
    4、可以当做函数返回


<a id="org2f0afb7"></a>

### class object type 的关系


<a id="org96f4467"></a>

### python 常见的内置类型

1.  None （全局只有一个）

2.  数值类型

    -   int
    -   float
    -   complex(复数）
    -   bool

3.  迭代类型

4.  序列类型

    -   list
    -   bytes, bytearray, memoryview(二进制序列)
    -   range
    -   tuple
    -   str
    -   array

5.  映射类型（dict)

6.  集合

    -   set
    -   frozenset

7.  上下文管理类型（with)

8.  其他

    -   模块类型
    -   class和实例
    -   函数类型
    -   方法类型
    -   代码类型
    -   object对象
    -   type类型
    -   ellipsis类型（省略号）
    -   notimplemented类型


<a id="org606acfa"></a>

## python的魔法函数


<a id="org8d55d62"></a>

### 什么是魔法函数

在python中 双下划线开头和双下划线结尾的函数是魔法函数

    class Company:
        def __init__(self, employee_list):
            self.employee = employee_list
    
        def __getitem__(self, item):
            return self.employee[item]
    
    
    company = Company(['tom', 'bob', 'jame'])        
    
    for em in company.employee: #没有__getitem__只能这样用
        print(em)
    
    for em in company: #现在可以这样用
        print(em)

通过\_<sub>getitem</sub>\_<sub>魔法函数可以Company变成可序列类型</sub>


<a id="org4b0bc3a"></a>

### python数据模型对python的影响

在python中定义魔法函数会对python的操作有影响，比如上面定义了\_<sub>getitem</sub>\_<sub>后</sub>，实例
化对象可以支持切片，迭代、len()等操作，而没有定义时则会报错。


<a id="orge6c286d"></a>

### 魔法函数一览

1.  非数学运算

    1.  字符串表示
    
        \_<sub>repr</sub>\_\_
        \_<sub>str</sub>\_\_
    
    2.  集合序列相关
    
        \_<sub>len</sub>\_\_
        \_<sub>getitem</sub>\_\_
        \_<sub>setitem</sub>\_\_
        \_<sub>delitem</sub>\_\_
        \_<sub>contains</sub>\_\_
    
    3.  迭代相关
    
        \_<sub>iter</sub>\_\_
        \_<sub>next</sub>\_\_
    
    4.  可调用
    
        \_<sub>call</sub>\_\_
    
    5.  with上下文管理器
    
        \_<sub>enter</sub>\_\_
        \_<sub>exit</sub>\_\_
    
    6.  数值替换
    
        \_<sub>abs</sub>\_\_
        \_<sub>bool</sub>\_\_
        \_<sub>init</sub>\_\_
        \_<sub>float</sub>\_\_
        \_<sub>hash</sub>\_\_
        \_<sub>index</sub>\_\_
    
    7.  元类相关
    
        \_<sub>new</sub>\_\_
        \_<sub>init</sub>\_\_
    
    8.  属性相关
    
        \_<sub>getattr</sub>\_\_
        \_<sub>setattr</sub>\_\_
        \_<sub>getattribute</sub>\_\_
        \_<sub>setattribute</sub>\_\_
        \_<sub>dir</sub>\_\_
    
    9.  属性描述符
    
        \_<sub>get</sub>\_\_
        \_<sub>set</sub>\_\_
        \_<sub>delete</sub>\_\_
    
    10. 协程
    
        \_<sub>awaite</sub>\_\_
        \_<sub>aiter</sub>\_\_
        \_<sub>anext</sub>\_\_
        \_<sub>aenter</sub>\_\_
        \_<sub>aexit</sub>\_\_


<a id="org09c4777"></a>

## 深入类和对象


<a id="org4b2c804"></a>

### 鸭子类型和多态

    class Cat(object):
        def say(self):
            print("i am a cat")
    
    class Dog(object):
        def say(self):
            print("i am a fish")
    
    class Company(object):
        def __init__(self, employee_list):
            self.employee = employee_list
    
        def __getitem__(self, item):
            return self.employee[item]
    
        def __len__(self):
            return len(self.employee)
    
    company = Company(["tom", "bob", "jane"])
    
    class Duck(object):
        def say(self):
            print("i am a duck")
    
    animal_list = [Cat, Dog, Duck]
    for animal in animal_list:
        animal().say()
    
    
    dog = Dog()
    a = ["bobby1", "bobby2"]
    
    b = ["bobby2", "bobby"]
    name_tuple = ["bobby3", "bobby4"]
    name_set = set()
    name_set.add("bobby5")
    name_set.add("bobby6")
    a.extend(dog)
    print(a)

extend方法传递一个可迭代对象，可以是list、tuple、set、只因为他们内部有共同的方
法实现了可迭代类型，他们就是可以被extend的，就是鸭子类型。


<a id="org079007b"></a>

### 抽象基类（abc模块）

    #我们去检查某个类是否有某种方法
    class Company(object):
        def __init__(self, employee_list):
            self.employee = employee_list
    
        def __len__(self):
            return len(self.employee)
    
    
    com = Company(["bobby1","bobby2"])
    print(hasattr(com, "__len__"))
    
    
    class A:
        pass
    
    class B:
        pass
    
    #我们在某些情况之下希望判定某个对象的类型
    from collections.abc import Sized
    isinstance(com, Sized)
    
    b = B()
    print(isinstance(b, A))
    # print(len(com))
    
    #我们需要强制某个子类必须实现某些方法
    #实现了一个web框架，集成cache(redis, cache, memorychache)
    #需要设计一个抽象基类， 指定子类必须实现某些方法
    
    #如何去模拟一个抽象基类
    
    import abc
    from collections.abc import *
    
    
    class CacheBase(metaclass=abc.ABCMeta):
        @abc.abstractmethod
        def get(self, key):
            pass
    
        @abc.abstractmethod
        def set(self, key, value):
            pass
    # class CacheBase():
    #     def get(self, key):
    #         raise NotImplementedError
    #     def set(self, key, value):
    #         raise NotImplementedError
    #
    class RedisCache(CacheBase):
        def set(self, key, value):
            pass
    
    # redis_cache = RedisCache()
    # redis_cache.set("key", "value")

平时并不推荐使用抽象基类，应该更好的利用python的鸭子类型，和mixin继承方式


<a id="org984a9d0"></a>

### isinstance和type的区别

    class A:
        pass
    
    class B(A):
        pass
    
    b = B()
    
    print(isinstance(b, B)) #True
    print(isinstance(b, A)) #True
    
    print(type(b) is B)  #True
    print(type(b) is A)  #False
    
    # isinstance检查继承关系， type检查数据类型


<a id="org46dc3b7"></a>

### 类变量和实例变量的区别

    class A:
        aa = 1
        def __init__(self, x, y):
            self.x = x
            self.y = y
    
    a = A(2,3)
    
    A.aa = 11
    a.aa = 100 # 此时是实例变量增加了一个aa变量并不是改变了类变量aa中的值。
    print(a.x, a.y, a.aa) #2,3,100 #如果实例的aa变量没有赋值的话，会向上查找类变量aa的值此时返回11
    print(A.aa) # 11
    
    b = A(3,5)
    print(b.aa) # 11


<a id="orgebdc87e"></a>

### 静态方法类方法以及实例方法以及参数

      class Date:
          #构造函数
          def __init__(self, year, month, day):
              self.year = year
              self.month = month
              self.day = day
    
          def tomorrow(self):
              self.day += 1
    
          @staticmethod #当我们需要处理一些和实例方法和实例参数无关的逻辑时（就是可以在类外面处理的）
    # 为了方便组织代码，把他移到类的内部，使用静态方法
          def parse_from_string(date_str):
              year, month, day = tuple(date_str.split("-"))
              return Date(int(year), int(month), int(day))
    
          @staticmethod
          def valid_str(date_str):
              year, month, day = tuple(date_str.split("-"))
              if int(year)>0 and (int(month) >0 and int(month)<=12) and (int(day) >0 and int(day)<=31):
                  return True
              else:
                  return False
    
          @classmethod #避免实例方法中的类的硬解码传递一个类的参数
          def from_string(cls, date_str):
              year, month, day = tuple(date_str.split("-"))
              return cls(int(year), int(month), int(day))
    
          def __str__(self):
              return "{year}/{month}/{day}".format(year=self.year, month=self.month, day=self.day)
    
      if __name__ == "__main__":
          new_day = Date(2018, 12, 31)
          new_day.tomorrow()
          print(new_day)
    
          #2018-12-31
          date_str = "2018-12-31"
          year, month, day = tuple(date_str.split("-"))
          new_day = Date(int(year), int(month), int(day))
          print (new_day)
    
          #用staticmethod完成初始化
          new_day = Date.parse_from_string(date_str)
          print (new_day)
    
          #用classmethod完成初始化
          new_day = Date.from_string(date_str)
          print(new_day)
    
          print(Date.valid_str("2018-12-32"))


<a id="orgdf3733b"></a>

### python类的私有属性

python 用双下划线隐藏私有变量，其实内部转换为<sub>classname</sub>\_<sub>attr</sub>.


<a id="org83b68a0"></a>

### python的自省机制

    #自省是通过一定的机制查询到对象的内部结构
    from chapter04.class_method import Date
    class Person:
        """
        人
        """
        name = "user"
    
    class Student(Person):
        def __init__(self, scool_name):
            self.scool_name = scool_name
    
    if __name__ == "__main__":
        user = Student("慕课网")
    
        通过__dict__查询属性
        print(user.__dict__)
        user.__dict__["school_addr"] = "北京市"
        print(user.school_addr)
        print(Person.__dict__) #类的属性比实例属性多 而使用dir更加强大
        print(user.name)
        a = [1,2]
        print(dir(a))


<a id="org6ee4d1c"></a>

### super真的是调用父类吗？

      # from threading import Thread
      # class MyThread(Thread):
      #     def __init__(self, name, user):
      #         self.user = user
      #         super().__init__(name=name)
    
      #既然我们重写B的构造函数， 为什么还要去调用super？
      #super到底执行顺序是什么样的？
    
      class A:
          def __init__(self):
              print ("A")
    
      class B(A):
          def __init__(self):
              print ("B")
              super().__init__()
    
      class C(A):
          def __init__(self):
              print ("C")
              super().__init__() # 如果注释掉这行结果是 D,B,A吗？结果是D,B,C
    
      class D(B, C):
          def __init__(self):
              print ("D")
              super(D, self).__init__()
    
      # if __name__ == "__main__":
      print(D.__mro__) #查看mro顺序
      d = D()
    
      In [3]: (<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, 
    <class '__main__.A'>, <class 'object'>)
      D
      B
      C
      A
    # super调用的其实不是严格意义上的父类 而是以mro顺序去查找


<a id="org6bb9275"></a>

### python中的mixin


<a id="org01c7301"></a>

### python中的with语句

    #try except finally
    def exe_try():    #关于try except, finally 的return问题 按执行顺序一次把return结果
        try:          # 压入栈中最后取栈顶的结果
            print ("code started")
            raise KeyError
            return 1
        except KeyError as e:
            print ("key error")
            return 2
        else:  # 当异常不触发是 执行else语句
            print ("other error")
            return 3
        finally:
            print ("finally")
            # return 4
    
    #上下文管理器协议
    class Sample:
        def __enter__(self):
            print ("enter")
            #获取资源
            return self
        def __exit__(self, exc_type, exc_val, exc_tb):
            #释放资源
            print ("exit")
        def do_something(self):
            print ("doing something")
    
    with Sample() as sample:
        sample.do_something()
    
    # if __name__ == "__main__":
    #     result = exe_try()
    #     print (result)


<a id="orgf0f7b16"></a>

### 使用contextlib简化上下文管理器

    import contextlib
    
    @contextlib.contextmanager
    def file_open(file_name):
        print ("file open") 类似__enter__
        yield {}
        print ("file end")  类似__exit__
    
    with file_open("bobby.txt") as f_opened:
        print ("file processing")


<a id="org6189f6f"></a>

## 自定义序列类


<a id="org61d6735"></a>

### 序列类型分类

1.  容器序列 (list, tuple, deque)

2.  扁平序列 (str, bytes, bytearray, array.array)

3.  可变序列 (list, deque, bytearray, arry)

4.  不可变序列 (str, tuple, bytes)


<a id="orgfacf8eb"></a>

### list 中的 + += extend append 的区别

    my_list = []
    my_list.append(1)
    my_list.append("a")
    
    from collections import abc
    
    a = [1,2]
    c = a + [3,4]   #只能是同一个类型的对象
    
    #就地加
    # a += (3,4) #实现的是 extend方法 传递一个可迭代对象
    # 
    # a.extend(range(3))
    
    a.append((1,2)) # append的是什么就相当于一个元素加入list
    print(a)


<a id="orgbf61833"></a>

### 实现可切片的对象

    import numbers
    class Group:    # 在collection abc 中找到不可变序列的抽象基类 重写函数
        #支持切片操作
        def __init__(self, group_name, company_name, staffs):
            self.group_name = group_name
            self.company_name = company_name
            self.staffs = staffs
    
        def __reversed__(self):
            self.staffs.reverse()
    
        def __getitem__(self, item):
            cls = type(self)
            if isinstance(item, slice):
                return cls(group_name=self.group_name, company_name=self.company_name, staffs=self.staffs[item])
            elif isinstance(item, numbers.Integral):
                return cls(group_name=self.group_name, company_name=self.company_name, staffs=[self.staffs[item]])
    
        def __len__(self):
            return len(self.staffs)
    
        def __iter__(self):
            return iter(self.staffs)
    
        def __contains__(self, item):
            if item in self.staffs:
                return True
            else:
                return False
    
    staffs = ["bobby1", "imooc", "bobby2", "bobby3"]
    group = Group(company_name="imooc", group_name="user", staffs=staffs)
    reversed(group)
    for user in group:
        print(user)


<a id="org738ea99"></a>

### bisect维护已排序序列

    import bisect
    from collections import deque
    
    #用来处理已排序的序列，用来维持已排序的序列， 升序
    #二分查找
    inter_list = deque()
    bisect.insort(inter_list, 3)
    bisect.insort(inter_list, 2)
    bisect.insort(inter_list, 5)
    bisect.insort(inter_list, 1)
    bisect.insort(inter_list, 6)
    
    print(bisect.bisect_left(inter_list, 3))
    #学习成绩
    print(inter_list)


<a id="org2ed912d"></a>

### 什么时候应该使用列表，什么时候不使用列表

    # array, deque
    # 数组
    import array
    #array和list的一个重要区别， array只能存放指定的数据类型
    my_array = array.array("i")
    my_array.append(1)
    my_array.append("abc")


<a id="orgfb3a880"></a>

### 列表推导式，生成器，字典推导式


<a id="orgfed5987"></a>

## 深入python的set和dict


<a id="org3affb20"></a>

### dict常用方法

    a = {"bobby1":{"company":"imooc"},
         "bobby2": {"company": "imooc2"}
         }
    #clear
    # a.clear()
    # pass
    
    #copy, 返回浅拷贝
    new_dict = a.copy()
    new_dict["bobby1"]["company"] = "imooc3"
    
    #formkeys
    new_list = ["bobby1", "bobby2"]
    
    new_dict = dict.fromkeys(new_list, {"company":"imooc"})
    
    new_dict.update((("bobby","imooc"),)) #创建字典神器， 传入可迭代对象 可以传键值


<a id="org294c5b1"></a>

### dict子类

    #不建议继承list和dict
    class Mydict(dict):
        def __setitem__(self, key, value):
            super().__setitem__(key, value*2)
    
    my_dict = Mydict(one=1)
    my_dict["one"] = 1
    print (my_dict)
    
    from collections import UserDict
    
    class Mydict(UserDict):
        def __setitem__(self, key, value):
            super().__setitem__(key, value*2)
    
    my_dict = Mydict(one=1)
    # my_dict["one"] = 1
    print (my_dict)
    
    from collections import defaultdict
    
    my_dict = defaultdict(dict)
    my_value = my_dict["bobby"]
    pass


<a id="orgdea16ba"></a>

### set和frozenset

    #set 集合 fronzenset (不可变集合) 无序， 不重复
    # s = set('abcdee')
    # s = set(['a','b','c','d','e'])
    s = {'a','b', 'c'}
    # s = frozenset("abcde") #frozenset 可以作为dict的key
    # print(s)
    
    #向set添加数据
    another_set = set("cef")
    re_set = s.difference(another_set)
    re_set = s - another_set
    re_set = s & another_set
    re_set = s | another_set
    
    #set性能很高
    # | & -  #集合运算
    print(re_set)
    
    print (s.issubset(re_set))
    # if "c" in re_set:
    #     print ("i am in set")


<a id="orgc71e349"></a>

### dict和set实现原理

    
    from random import randint
    
    
    def load_list_data(total_nums, target_nums):
        """
        从文件中读取数据，以list的方式返回
        :param total_nums: 读取的数量
        :param target_nums: 需要查询的数据的数量
        """
        all_data = []
        target_data = []
        file_name = "G:/慕课网课程/AdvancePython/fbobject_idnew.txt"
        with open(file_name, encoding="utf8", mode="r") as f_open:
            for count, line in enumerate(f_open):
                if count < total_nums:
                    all_data.append(line)
                else:
                    break
    
        for x in range(target_nums):
            random_index = randint(0, total_nums)
            if all_data[random_index] not in target_data:
                target_data.append(all_data[random_index])
                if len(target_data) == target_nums:
                    break
    
        return all_data, target_data
    
    def load_dict_data(total_nums, target_nums):
        """
        从文件中读取数据，以dict的方式返回
        :param total_nums: 读取的数量
        :param target_nums: 需要查询的数据的数量
        """
        all_data = {}
        target_data = []
        file_name = "G:/慕课网课程/AdvancePython/fbobject_idnew.txt"
        with open(file_name, encoding="utf8", mode="r") as f_open:
            for count, line in enumerate(f_open):
                if count < total_nums:
                    all_data[line] = 0
                else:
                    break
        all_data_list = list(all_data)
        for x in range(target_nums):
            random_index = randint(0, total_nums-1)
            if all_data_list[random_index] not in target_data:
                target_data.append(all_data_list[random_index])
                if len(target_data) == target_nums:
                    break
    
        return all_data, target_data
    
    
    def find_test(all_data, target_data):
        #测试运行时间
        test_times = 100
        total_times = 0
        import time
        for i in range(test_times):
            find = 0
            start_time = time.time()
            for data in target_data:
                if data in all_data:
                    find += 1
            last_time = time.time() - start_time
            total_times += last_time
        return total_times/test_times
    
    
    if __name__ == "__main__":
        # all_data, target_data = load_list_data(10000, 1000)
        # all_data, target_data = load_list_data(100000, 1000)
        # all_data, target_data = load_list_data(1000000, 1000)
    
    
        # all_data, target_data = load_dict_data(10000, 1000)
        # all_data, target_data = load_dict_data(100000, 1000)
        # all_data, target_data = load_dict_data(1000000, 1000)
        all_data, target_data = load_dict_data(2000000, 1000)
        last_time = find_test(all_data, target_data)
    
        #dict查找的性能远远大于list
        #在list中随着list数据的增大 查找时间会增大
        #在dict中查找元素不会随着dict的增大而增大
        print(last_time)
    
    #1.  dict的key或者set的值 都必须是可以hash的
    #不可变对象 都是可hash的， str， fronzenset， tuple，自己实现的类 __hash__
    #2. dict的内存花销大，但是查询速度快， 自定义的对象 或者python内部的对象都是用dict包装的
    # 3. dict的存储顺序和元素添加顺序有关
    # 4. 添加数据有可能改变已有数据的顺序


<a id="orga415873"></a>

## 对象引用、可变性和垃圾回收


<a id="orgcf2f30a"></a>

### python中的变量是什么？


<a id="org66c8993"></a>

### python的垃圾回收和del


<a id="orgd260607"></a>

### 一个典型的错误

    def add(a, b):
        a += b
        return a
    
    class Company:
        def __init__(self, name, staffs=[]):
            self.name = name
            self.staffs = staffs
        def add(self, staff_name):
            self.staffs.append(staff_name)
        def remove(self, staff_name):
            self.staffs.remove(staff_name)
    
    if __name__ == "__main__":
        com1 = Company("com1", ["bobby1", "bobby2"])
        com1.add("bobby3")
        com1.remove("bobby1")
        print (com1.staffs)
    
        com2 = Company("com2") #当com2,com3都不传递list参数是会默认使用defaults参数
        com2.add("bobby")      #会使com2，com3中的数据互相影响
        print(com2.staffs)
    
        print (Company.__init__.__defaults__)
    
        com3 = Company("com3")
        com3.add("bobby5")
        print (com2.staffs)
        print (com3.staffs)
        print (com2.staffs is com3.staffs)
    
        # a = 1
        # b = 2
        #
        # a = [1,2]
        # b = [3,4]
        #
        # a = (1, 2)
        # b = (3, 4)
        #
        # c = add(a, b)
        #
        # print(c)
        # print(a, b)


<a id="org42573ef"></a>

## 元类编程


<a id="org3e4a694"></a>

### property动态属性


<a id="orgf7ffc83"></a>

### \_<sub>getattr</sub>\_\_, <span class="underline"><span class="underline">getattribute</span></span> 区别

    #__getattr__, __getattribute__
    #__getattr__ 就是在查找不到属性的时候调用
    from datetime import date
    class User:
        def __init__(self,info={}):
            self.info = info
    
        def __getattr__(self, item):
            return self.info[item]
    
        # def __getattribute__(self, item):
        #     return "bobby"
    
    if __name__ == "__main__":
        user = User(info={"company_name":"imooc", "name":"bobby"})
        print(user.test)


<a id="org331944f"></a>

### 属性描述符和属性查找过程

    from datetime import date, datetime
    import numbers
    
    class IntField:
        #数据描述符
        def __get__(self, instance, owner):
            return self.value
        def __set__(self, instance, value):
            if not isinstance(value, numbers.Integral):
                raise ValueError("int value need")
            if value < 0:
                raise ValueError("positive value need")
            self.value = value
        def __delete__(self, instance):
            pass
    
    
    class NonDataIntField:
        #非数据属性描述符
        def __get__(self, instance, owner):
            return self.value
    
    class User:
        age = IntField()
        # age = NonDataIntField()
    
    '''
    如果user是某个类的实例，那么user.age（以及等价的getattr(user,’age’)）
    首先调用__getattribute__。如果类定义了__getattr__方法，
    那么在__getattribute__抛出 AttributeError 的时候就会调用到__getattr__，
    而对于描述符(__get__）的调用，则是发生在__getattribute__内部的。
    user = User(), 那么user.age 顺序如下：
    
    （1）如果“age”是出现在User或其基类的__dict__中， 且age是data descriptor， 那么调用其__get__方法, 否则
    
    （2）如果“age”出现在user的__dict__中， 那么直接返回 obj.__dict__[‘age’]， 否则
    
    （3）如果“age”出现在User或其基类的__dict__中
    
    （3.1）如果age是non-data descriptor，那么调用其__get__方法， 否则
    
    （3.2）返回 __dict__[‘age’]
    
    （4）如果User有__getattr__方法，调用__getattr__方法，否则
    
    （5）抛出AttributeError
    
    '''
    
    # class User:
    #
    #     def __init__(self, name, email, birthday):
    #         self.name = name
    #         self.email = email
    #         self.birthday = birthday
    #         self._age = 0
    #
    #     # def get_age(self):
    #     #     return datetime.now().year - self.birthday.year
    #
    #     @property
    #     def age(self):
    #         return datetime.now().year - self.birthday.year
    #
    #     @age.setter
    #     def age(self, value):
    #         #检查是否是字符串类型
    #         self._age = value
    
    if __name__ == "__main__":
        user = User()
        user.__dict__["age"] = "abc"
        print (user.__dict__)
        print (user.age)
        # print (getattr(user, 'age'))
        # user = User("bobby", date(year=1987, month=1, day=1))
        # user.age = 30
        # print (user._age)
        # print(user.age)


<a id="orga07fe2e"></a>

### \_<sub>new</sub>\_\_,\_<sub>init</sub>\_<sub>的区别</sub>

    class User:
        def __new__(cls, *args, **kwargs):
            print (" in new ")
            return super().__new__(cls)
        def __init__(self, name):
            print (" in init")
            pass
    a = int()
    #new 是用来控制对象的生成过程， 在对象生成之前
    #init是用来完善对象的
    #如果new方法不返回对象， 则不会调用init函数
    if __name__ == "__main__":
        user = User(name="bobby")


<a id="org06b2b4b"></a>

### 自定义元类


<a id="org2f45ca6"></a>

## python Socket编程


<a id="org9193a46"></a>

### socket client 实现通信

