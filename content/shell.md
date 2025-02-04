## V shell script

V语言还可以用来写系统shell脚本，比如开发脚本，构建脚本等。

用V语言来写shell脚本还是比较舒服的，不仅语法简洁，而且是跨平台的。

V脚本的文件名后缀为 .vsh，跟.v源文件相比，在.vsh中：

- 不用定义主模块
- 不用定义主函数
- 不用导入os模块，调用os模块函数时，可以省略os前缀，就像使用内置函数那样
- 跟shell脚本一样，可以在首行中使用#!来设置执行脚本的工具

script.vsh

```v
#!/usr/local/bin/v run

for _ in 0 .. 5 {
	println('V script')
}
println('deploying...')
println('Files')
foo := ls('.') or { panic(err) }
println(foo)
println('')
rm('a.out')
println('Making dir name and creating foo.txt')
mkdir('name') ?
// TODO mkdir()
create('foo.txt') ?
foo_ls := ls('.') or { panic(err) }
println(foo_ls)
println('')
println('Entering into name')
chdir('name')
foo_ls2 := ls('.') or { panic(err) }
println(foo_ls2)
println('')
println('Removing name and foo.txt')
println('')
chdir('../')
rmdir('name')
rm('foo.txt')
again := ls('.') or { panic(err) }
println(again)

```

直接运行:

```shell
v script.vsh	// v后面跟着.vsh文件，默认就是要执行脚本，可以省略run子命令
v run script.vsh
```

以下是[vls](https://github.com/vlang/vls)的构建脚本：

```v
#!/usr/local/bin/v run  //跟shell脚本一样，可以使用#!来设置执行此脚本的工具，也是要提前设置为可执行：chmod +x

import os

// use system default C compiler if found
mut cc := 'cc'
if os.args.len >= 2 {
	if os.args[1] in ['cc', 'gcc', 'clang', 'msvc'] {
		cc = os.args[1]
	} else {
		println('> Usage error: parameter must be in cc/gcc/clang/msvc')
		return
	}
}
println('> Building VLS...')

ret := system('v -gc boehm -cg -cc $cc cmd/vls -o vls')	//可以直接使用os模块内部的函数，就像内置函数那样，不用模块前缀
if ret != 0 {
	println('Failed building VLS')
	return
}

println('> VLS built successfully!')

```

os模块常用的函数可以参考[标准库os模块](std_os.md)章节的介绍。
