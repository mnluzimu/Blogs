# argparser Python包使用

导入

```
import argparse
```

定义parser对象（参数为介绍）

```
parser = argparse.ArgumentParser('test parser')
```

定义argument

传入字符串

首先我们看最简单的传入string的情况

```
parser.add_argument('-o', metavar='STRING', help='input string')
```

第一个参数是用于表示其后面的那个字符串是所要输入的字符串的命令形式，metavar是在提示或报错里面用于指代传入值的，help是你传入`-h`时的提示

比如

```
python ./test.py -o hello
```

传入的就是hello，同时，“-o”中o也用于索引，我们用

```
args = parser.parse_arg()
```

获得参数之后，就用args.o来取出这个传入的string

```
print(args.o)
```

可以输出

```
hello
```

然后我们来测试一下

```
import argparse

parser = argparse.ArgumentParser('test parser')


parser.add_argument('-o', metavar='STRING', help='input string')
parser.add_argument('-oo', metavar='STRING', help='input string')
parser.add_argument('string2', metavar='STRING2', help='input string')
parser.add_argument('string', metavar='STRING', help='input string')
parser.add_argument('--a', metavar='STRING', help='input string')
parser.add_argument('---aa', metavar='STRING', help='input string')


def main():
    args = parser.parse_args()
    print(args.string)
    print(args.string2)
    print(args.o)
    print(args.oo)
    print(args.a)
    print(args.aa)

if __name__ == '__main__':
    main()
```

以上程序，输入

```
python ./test.py hello world --a hi ---aa you --aaa what -o 11 -oo 22
```

输出的是

```
world
hello
11
22
hi
you
what
```

可以看出`string2`和`string`两个参数前面没有`-`, 它们输入时就不用加前面的标识（其实也不能加，加了报错），遇到这种参数程序就把传入参数中没有前面标识的依次填入，如上例中就是string中是world，string2中是hello，如果把add_argument中string和string2调换顺序就会是相反的。

而只要参数前面有`-`, 不管是一个还是两个或三个`-`, 最后都是去掉`-`做为索引，索引是一个还是多个字符也不影响

而根据进一步测试，如果把`---`省为`--`, 结果会是arg.aa变成None

```
python ./test.py hello world --a hi --aa you --aaa what -o 11 -oo 22
```

输出

```
world
hello
11
22
hi
None
what
```

其他情况擅自在命令中删增`-`都直接报类似下面的错

```
usage: test parser [-h] [-o STRING] [-oo STRING] [--a STRING] [---aa STRING]
                   [--aaa STRING]
                   STRING2 STRING
test parser: error: unrecognized arguments: --oo 22
```

所以使用的时候还是尽量不要瞎搞，下面还是保险都用`--name`吧（两个`-`符合习惯）

而且如果string 和string2不到值，就会出现以下报错

```
usage: test parser [-h] [-o STRING] [-oo STRING] [--a STRING] [---aa STRING]
                   [--aaa STRING] [--bb]
                   STRING2 STRING
test parser: error: the following arguments are required: STRING2, STRING
```

所以这种参数一定要传

而其他参数如果不传，里面的值是None，不会报错

### 确定布尔值

```
parser.add_argument('--bb', dest='bbb', action='store_false',
                    help='store bool')
```

这样会在`args.bbb`里面存上false

```
import argparse

parser = argparse.ArgumentParser('test parser')

parser.add_argument('--bb', dest='bbb', action='store_false',
                    help='store bool')

def main():
    args = parser.parse_args()
    print(args.bbb)

if __name__ == '__main__':
    main()
```

运行

```
python ./test.py --bb
```

输出

```
False
```

如果是

```
parser.add_argument('--bb', dest='bbb', action='store_true',
                    help='store bool')
```

就会输出

```
True
```

注意是用dest里面的字符串（这里是args.bbb）索引

如果用args.bb，会报错

```
AttributeError: 'Namespace' object has no attribute 'bb'
```

### 传入整数，浮点数

```
import argparse

parser = argparse.ArgumentParser('test parser')

parser.add_argument('--test-freq', default=1, type=int, metavar='N', 
                    help='run test evaluation every N epochs (default: 1)')

def main():
    print(args.test_freq)

if __name__ == '__main__':
    main()
```

运行

```
python ./test.py --test-freq 5
```

结果

```
5
```

如果运行

```
python ./test.py
```

结果

```
1
```

因为有default值（其实之前string也可以用default）

同时注意到命令行里test-freq中间的`-`在程序里变成`_`

浮点数就把type改为float

```
import argparse

parser = argparse.ArgumentParser('test parser')

parser.add_argument('--lr', default=0.001, type=float, metavar='LR', 
                    help='learning rate(default=0.001)')

def main():
    print(args.lr)

if __name__ == '__main__':
    main()
```

输入

```
python ./test.py --lr 0.0001
```

输出

```
0.0001
```



以上的参数设置在一般程序里面都已经够用了，可以看出argparse还是很方便的。





