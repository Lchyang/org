
# Table of Contents

1.  [字符串处理案例进阶训练](#org9688769)
    1.  [如何拆分含多种分隔符的字符串](#orgc264026)
    2.  [如何判断字符串a是否以字符串b结尾](#org99d99a7)
    3.  [如何调整字符串中文本的格式](#orgdf73f94)
    4.  [如何将多个小字符串拼接成大的字符串](#org05ead4a)
    5.  [如何对字符串进行左右居中对齐](#org522f581)
    6.  [如何去掉字符串中不需要的字符](#orgc447714)


<a id="org9688769"></a>

# 字符串处理案例进阶训练


<a id="orgc264026"></a>

## 1.如何拆分含多种分隔符的字符串

案例：要把某个字符串依据分隔符拆分成不同的字段.  
s = 'ab;cd|efg|hi,jkl|mn\topq;rst,uvw\txyz'.  
其中，；\\ /t都是分隔符，如何处理.  

    # split()方法
    def mySplit(s, ds):
        res = [s]
        for d in ds:
            t = []
            list(map(lambda x: t.extend(x.split(d)), res))
            res = t
        return [x for x in res if x] #当两个分隔符连续时会产生空字符串 过滤掉
    
    s = 'ab;cd|efg|hi,,jkl|mn\topq;rst,uvw\txyz'
    print(mySplit(s, ';,|\t'))
    
    # In [7]: ['ab', 'cd', 'efg', 'hi', 'jkl', 'mn', 'opq', 'rst', 'uvw', 'xyz']
    
    # 方法二： re 模块 re.split
    import re
    print(re.split(r'[,;|\t]+', s))


<a id="org99d99a7"></a>

## 2.如何判断字符串a是否以字符串b结尾

案例：某文件系统目录下有一系列文件：  
quicksort.c  
graph.py  
heap.java  
install.sh  
stack.cpp   
编写程序给其中的所有.sh文件和.py文件加上用户权限

解决方案：使用字符串的   str.startswith()  和  str.endswith()  方法

      In [18]: ls
      HelloWorld.class  c_test/           test.py           venv2/
      HelloWorld.java   test              underscores.txt
    
      In [19]: import os, stat
    
      In [20]: os.listdir('.')
      Out[20]:
      ['.DS_Store',
      'c_test',
      'HelloWorld.class',
      'HelloWorld.java',
      'test',
      'test.py',
      'underscores.txt',
      'venv2']
    # 注意endswith()参数不能是列表。
      In [29]: [name for name in os.listdir('.') if name.endswith(('.sh','.py'))]
      Out[29]: ['test.py']
    
      In [30]: os.stat('test.py')
      Out[30]: posix.stat_result(st_mode=33188, st_ino=10893185, st_dev=16777220,
                                st_nlink=1, st_uid=501, st_gid=20, st_size=0,
                                st_atime=1542949483, st_mtime=1542949488, st_ctime=1542949488)
    
      In [31]: os.stat('test.py').st_mode
      Out[31]: 33188
    
      In [32]: oct(os.stat('test.py').st_mode)
      Out[32]: '0100644'
    
      In [33]: os.chmod('test.py', os.stat('test.py').st_mode | stat.S_IXUSR)
    
      In [34]: ls -l
      total 32
      -rw-r--r--  1 lichunyang  staff  425  9 20 13:56 HelloWorld.class
      -rw-r--r--  1 lichunyang  staff  147  9 20 13:56 HelloWorld.java
      drwxr-xr-x  5 lichunyang  staff  170  9 24 22:41 c_test/
      -rw-r--r--  1 lichunyang  staff   62  3 14  2018 test
      -rwxr--r--  1 lichunyang  staff    0 11 23 13:04 test.py*
      -rw-r--r--  1 lichunyang  staff  141  9 29 10:11 underscores.txt
      drwxr-xr-x  6 lichunyang  staff  204  7 17 16:38 venv2/

test.py 权限已经改变


<a id="orgdf73f94"></a>

## 3.如何调整字符串中文本的格式

将一个log 文件中的 2018-05-23 的日期格式 转换成 05/23/2018的格式

    import re
    log  = open('var/log/dpkg.log').read()
    
    '''
    re.sub?
    Signature: re.sub(pattern, repl, string, count=0, flags=0)
    Docstring:
    Return the string obtained by replacing the leftmost
    non-overlapping occurrences of the pattern in string by the
    replacement repl.  repl can be either a string or a callable;
    if a string, backslash escapes in it are processed.  If it is
    a callable, it's passed the match object and must return
    '''
    re.sub('(\d{4})-(\d{2})-(\d{2}))', r'\2/\3/\1', log) #注意正则表达式中使用原始字符串
    re.sub('(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2}))',
          r'\g<month>/\g<day>/\g<year>', log)
    # 第一种使用引用计数方法 第二种属于引用名字方法。


<a id="org05ead4a"></a>

## 4.如何将多个小字符串拼接成大的字符串

有字符串['a','b', 1, 2, 'c', 'd']如何拼接成大的字符串

使用+号 每次迭代存在浪费

    In [38]: l = ['a', 'b','c']
    
    In [39]: s = ''
    
    In [40]: for x in l:
        ...:     s += x
        ...:
    
    In [41]: s
    Out[41]: 'abc'
    
    In [42]: for x in l:
        ...:     s += x
        ...:     print(s)
        ...:
    abca
    abcab
    abcabc

使用str.join()

    In [44]: l
    Out[44]: ['a', 'b', 'c']
    
    In [45]: s = ''
    
    In [46]: ''.join(l)
    Out[46]: 'abc'
    
    In [47]: l = ['a', 1, 'b'] #当有整数时记得转换成str
    
    In [48]: ''.join(l)
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-48-9e05f63092b3> in <module>()
    ----> 1 ''.join(l)
    
    TypeError: sequence item 1: expected str instance, int found
    
    In [49]: ''.join([str(x) for x in s])
    Out[49]: ''
    
    In [50]: ''.join([str(x) for x in l]) #使用列表解析存在空间浪费
    Out[50]: 'a1b'
    
    In [51]: ''.join(str(x) for x in l) #建议使用生成器
    Out[51]: 'a1b'


<a id="org522f581"></a>

## 5.如何对字符串进行左右居中对齐

某个字典中存储了一系列属性值，
{'lodDist': 100.0,
'SmallCull': 0.05,
'DistCull': 500,
'trulinear': 40,
'farclip': 477}
如何将其工整的输出.  
str.ljust, str.rjust, str.center

    In [92]: s = 'abc'
    
    In [96]: s.ljust?
    Docstring:
    S.ljust(width[, fillchar]) -> str
    
    Return S left-justified in a Unicode string of length width. Padding is
    done using the specified fill character (default is a space).
    Type:      builtin_function_or_method
    
    
    In [97]: s.ljust(20)
    Out[106]: 'abc                 '
    
    In [130]: s.rjust(20, '-')
    Out[146]: '-----------------abc'
    
    In [147]: s.center(20, '0')
    Out[165]: '00000000abc000000000'

使用format

    In [166]: format?
    Signature: format(value, format_spec='', /)
    Docstring:
    Return value.__format__(format_spec)
    
    format_spec defaults to the empty string.
    See the Format Specification Mini-Language section of help('FORMATTING') for
    details.
    Type:      builtin_function_or_method
    
    
    In [172]: s
    Out[172]: 'abc'
    
    In [173]: format(s, '<20')
    Out[243]: 'abc                 '
    
    In [244]: format(s, '>20')
    Out[260]: '                 abc'
    
    In [261]: format(s, '^20')
    Out[283]: '        abc         '

解决问题：

    In [284]: d = {'lodDist': 100.0,
    'SmallCull': 0.05,
    'DistCull': 500,
    'trulinear': 40,
    'farclip': 477}
    
    In [291]: d.keys()
    Out[297]: dict_keys(['lodDist', 'SmallCull', 'DistCull', 'trulinear', 'farclip'])
    
    In [317]: w = max(map(len, d.keys()))
    
    In [332]: for k in d:
         ...:     print (k.ljust(w), ':', d[k])
    
    SmallCull : 0.05
    DistCull  : 500
    trulinear : 40
    farclip   : 477


<a id="orgc447714"></a>

## 6.如何去掉字符串中不需要的字符

第一种 使用 strip

    In [65]: s = '   abc 123'
    
    In [66]: s.strip()
    Out[66]: 'abc 123'
    
    In [67]: s.strip('3')
    Out[67]: '   abc 12'
    
    In [68]: s.strip?
    Docstring:
    S.strip([chars]) -> str
    
    Return a copy of the string S with leading and trailing
    whitespace removed.
    If chars is given and not None, remove characters in chars instead.
    Type:      builtin_function_or_method
    
    In [69]: s.lstrip()
    Out[69]: 'abc 123'
    
    In [70]: s.rstrip()
    Out[70]: '   abc 123'
    
    In [71]: s = '---abc+++'
    
    In [72]: s.strip('-+')
    Out[72]: 'abc'

第二种 使用切片 拼接的方式

    In [82]: s = '123?4556'
    
    In [84]: s[:3] + s[4:]
    Out[84]: '1234556'

第三种：字符串replace方法， 或者使用正则表达式re.sub()删除任意字符

    In [74]: s
    Out[74]: '\tabc\t123\txyz'
    
    In [75]: s.replace('\t', '') # replace 只能替换一种字符
    Out[75]: 'abc123xyz'
    
    In [78]: s = '\tabc\t123\txyz\ropt\r'
    
    In [79]: import re
    
    In [80]: re.sub('[\t\r]', '', s)  # re.sub 可以替换多种字符
    Out[80]: 'abc123xyzopt'

