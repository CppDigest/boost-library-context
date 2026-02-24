# #1578 - build error with #include <windows.h> [Closed]

> Username: jackiehe  
> Created at: 2019-04-19 16:39:56 UTC  
> Updated at: 2019-04-20 19:05:08 UTC  
> Closed at: 2019-04-20 19:05:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1578  

when i bulid any sample with boost 1.70,  beast 248 in vistual studio 2017,  
if i include ,  #include <windows.h> , there are many many build errors;  
  
d:\bak2\boost\boost_1_70_0\boost\beast\core\rate_policy.hpp(155): warning C4003: 类函数宏的调用“max”参数不足  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\rate_policy.hpp(155): error C2589: “(”:“::”右边的非法标记  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\rate_policy.hpp(155): error C2062: 意外的类型“unknown-type”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\rate_policy.hpp(155): error C2737: “private: static unsigned int const boost::beast::simple_rate_policy::all”: 必须初始化“constexpr”对象  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\rate_policy.hpp(155): error C2059: 语法错误:“)”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(30): warning C4003: 类函数宏的调用“min”参数不足  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(30): error C2146: 语法错误: 缺少“)”(在标识符“t”的前面)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(30): error C2365: “T”: 重定义；以前的定义是“模板参数”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(29): note: 参见“T”的声明  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(30): error C2061: 语法错误: 标识符“t”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(30): error C2059: 语法错误:“)”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(30): error C2988: 不可识别的模板声明/定义  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(31): error C2143: 语法错误: 缺少“;”(在“{”的前面)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(31): error C2447: “{”: 缺少函数标题(是否是老式的形式表?)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(36): warning C4002: 类函数宏的调用“min”参数过多  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(36): error C2146: 语法错误: 缺少“)”(在标识符“t0”的前面)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(36): error C2365: “T”: 重定义；以前的定义是“模板参数”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(35): note: 参见“T”的声明  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(36): error C2061: 语法错误: 标识符“t0”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(36): error C2059: 语法错误:“)”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(36): error C2146: 语法错误: 缺少“)”(在标识符“t1”的前面)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(36): error C2146: 语法错误: 缺少“;”(在标识符“t1”的前面)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(37): error C2143: 语法错误: 缺少“;”(在“{”的前面)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(37): error C2447: “{”: 缺少函数标题(是否是老式的形式表?)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-19 17:03:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1578#issuecomment-484958035  

Please provide:  
  
### Steps necessary to reproduce the problem  
  
A small compiling program is the best. If your code is public, you can provide a link to the repository.

---

## Comment 2

> Username: jackiehe  
> Created at: 2019-04-19 17:09:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1578#issuecomment-484959301  

please compile this very very very  sample code in vistual studio 2017, you can see the error.  
  
#include <windows.h>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/websocket.hpp>  
int main(int argc, char** argv)  
{  
return 0;  
}

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-04-19 17:23:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1578#issuecomment-484963007  

What happens when you compile this program?  
```  
#include <windows.h>  
#include <boost/asio.hpp>  
int main(int argc, char** argv)  
{  
    return 0;  
}  
```

---

## Comment 4

> Username: jackiehe  
> Created at: 2019-04-19 17:29:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1578#issuecomment-484964550  

#define WIN32_LEAN_AND_MEAN             //  从 Windows 头文件中排除极少使用的信息  
#include <windows.h>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/websocket.hpp>  
int main(int argc, char** argv)  
{  
	return 0;  
}  
[ConsoleApplication1.zip](https://github.com/boostorg/beast/files/3098852/ConsoleApplication1.zip)

---

## Comment 5

> Username: jackiehe  
> Created at: 2019-04-19 17:30:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1578#issuecomment-484964743  

1>------ 已启动生成: 项目: ConsoleApplication1, 配置: Debug Win32 ------  
1>pch.cpp  
1>ConsoleApplication1.cpp  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\rate_policy.hpp(155): warning C4003: 类函数宏的调用“max”参数不足  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\rate_policy.hpp(155): error C2589: “(”:“::”右边的非法标记  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\rate_policy.hpp(155): error C2062: 意外的类型“unknown-type”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\rate_policy.hpp(155): error C2737: “private: static unsigned int const boost::beast::simple_rate_policy::all”: 必须初始化“constexpr”对象  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\rate_policy.hpp(155): error C2059: 语法错误:“)”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(30): warning C4003: 类函数宏的调用“min”参数不足  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(30): error C2146: 语法错误: 缺少“)”(在标识符“t”的前面)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(30): error C2365: “T”: 重定义；以前的定义是“模板参数”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(29): note: 参见“T”的声明  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(30): error C2061: 语法错误: 标识符“t”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(30): error C2059: 语法错误:“)”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(30): error C2988: 不可识别的模板声明/定义  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(31): error C2143: 语法错误: 缺少“;”(在“{”的前面)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(31): error C2447: “{”: 缺少函数标题(是否是老式的形式表?)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(36): warning C4002: 类函数宏的调用“min”参数过多  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(36): error C2146: 语法错误: 缺少“)”(在标识符“t0”的前面)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(36): error C2365: “T”: 重定义；以前的定义是“模板参数”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(35): note: 参见“T”的声明  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(36): error C2061: 语法错误: 标识符“t0”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(36): error C2059: 语法错误:“)”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(36): error C2146: 语法错误: 缺少“)”(在标识符“t1”的前面)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(36): error C2146: 语法错误: 缺少“;”(在标识符“t1”的前面)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(37): error C2143: 语法错误: 缺少“;”(在“{”的前面)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\type_traits.hpp(37): error C2447: “{”: 缺少函数标题(是否是老式的形式表?)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\websocket\detail\prng.hpp(68): warning C4003: 类函数宏的调用“min”参数不足  
1>d:\bak2\boost\boost_1_70_0\boost\beast\websocket\detail\prng.hpp(68): error C2059: 语法错误:“)”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\websocket\detail\prng.hpp(68): error C2334: “:”的前面有意外标记；跳过明显的函数体  
1>d:\bak2\boost\boost_1_70_0\boost\beast\websocket\detail\prng.hpp(76): warning C4003: 类函数宏的调用“max”参数不足  
1>d:\bak2\boost\boost_1_70_0\boost\beast\websocket\detail\prng.hpp(81): error C2143: 语法错误: 缺少“)”(在“;”的前面)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\websocket\detail\prng.hpp(81): error C2059: 语法错误:“)”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\websocket\detail\prng.hpp(81): error C2238: 意外的标记位于“;”之前  
1>d:\bak2\boost\boost_1_70_0\boost\beast\websocket\detail\prng.hpp(115): error C2059: 语法错误:“}”  
1>d:\bak2\boost\boost_1_70_0\boost\beast\websocket\detail\prng.hpp(115): error C2143: 语法错误: 缺少“;”(在“}”的前面)  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\pcg.hpp(37): error C2039: “ignore_unused”: 不是“boost::boost”的成员  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\pcg.hpp(17): note: 参见“boost::boost”的声明  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\pcg.hpp(39): error C2039: “ignore_unused”: 不是“boost::boost”的成员  
1>d:\bak2\boost\boost_1_70_0\boost\beast\core\detail\pcg.hpp(17): note: 参见“boost::boost”的声明  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thr\xthread(236): error C2061: 语法错误: 标识符“index_sequence”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thr\xthread(251): note: 参见对正在编译的 类 模板 实例化 "boost::std::_LaunchPad<_Target>" 的引用  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thr\xthread(246): error C3861: “make_index_sequence”: 找不到标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thr\xthread(246): error C3861: “tuple_size_v”: 找不到标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(48): error C3861: “tuple”: 找不到标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(48): note: 此诊断出现在编译器生成的函数“boost::std::thread::thread(_Fn &&,_Args &&...)”中  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(133): error C2653: “chrono”: 不是类或命名空间名称  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(133): error C4430: 缺少类型说明符 - 假定为 int。注意: C++ 不支持默认 int  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(133): error C2988: 不可识别的模板声明/定义  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(133): error C2143: 语法错误: 缺少“,”(在“<”的前面)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(135): error C3861: “_Rel_time”: 找不到标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(142): error C2653: “chrono”: 不是类或命名空间名称  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(142): error C4430: 缺少类型说明符 - 假定为 int。注意: C++ 不支持默认 int  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(142): error C2988: 不可识别的模板声明/定义  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(142): error C2143: 语法错误: 缺少“,”(在“<”的前面)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(144): error C3861: “_Abs_time”: 找不到标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(158): error C2988: 不可识别的模板声明/定义  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(158): error C2143: 语法错误: 缺少“;”(在“<”的前面)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(158): error C2238: 意外的标记位于“;”之前  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(158): error C2059: 语法错误:“<”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(160): error C2334: “{”的前面有意外标记；跳过明显的函数体  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(165): error C2988: 不可识别的模板声明/定义  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(166): error C2059: 语法错误:“:”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(152): error C2614: “boost::std::thread::id”: 非法的成员初始化:“_Id”不是基或成员  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(186): error C3083: “this_thread”:“::”左侧的符号必须是一种类型  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(186): error C2039: “get_id”: 不是“std”的成员  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\random(21): note: 参见“std”的声明  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(186): error C2678: 二进制“==”: 没有找到接受“boost::std::thread::id”类型的左操作数的运算符(或没有可接受的转换)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(174): note: 可能是“bool boost::std::operator ==(boost::std::thread::id,boost::std::thread::id) noexcept”[通过使用参数相关的查找找到]  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(186): note: 尝试匹配参数列表“(boost::std::thread::id, boost::std::thread::id)”时  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(195): error C2440: “return”: 无法从“const _Thrd_id_t”转换为“boost::std::thread::id”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(195): note: 无构造函数可以接受源类型，或构造函数重载决策不明确  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(200): error C2440: “return”: 无法从“_Thrd_id_t”转换为“boost::std::thread::id”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(200): note: 无构造函数可以接受源类型，或构造函数重载决策不明确  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(210): error C2039: “_Id”: 不是“boost::std::thread::id”的成员  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(32): note: 参见“boost::std::thread::id”的声明  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(215): error C2678: 二进制“==”: 没有找到接受“boost::std::thread::id”类型的左操作数的运算符(或没有可接受的转换)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(208): note: 可能是“bool boost::std::operator ==(boost::std::thread::id,boost::std::thread::id) noexcept”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(215): note: 尝试匹配参数列表“(boost::std::thread::id, boost::std::thread::id)”时  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(220): error C2039: “_Id”: 不是“boost::std::thread::id”的成员  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(32): note: 参见“boost::std::thread::id”的声明  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(225): error C2678: 二进制“<”: 没有找到接受“boost::std::thread::id”类型的左操作数的运算符(或没有可接受的转换)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(218): note: 可能是“bool boost::std::operator <(boost::std::thread::id,boost::std::thread::id) noexcept”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(225): note: 尝试匹配参数列表“(boost::std::thread::id, boost::std::thread::id)”时  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(230): error C2678: 二进制“<”: 没有找到接受“boost::std::thread::id”类型的左操作数的运算符(或没有可接受的转换)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(218): note: 可能是“bool boost::std::operator <(boost::std::thread::id,boost::std::thread::id) noexcept”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(230): note: 尝试匹配参数列表“(boost::std::thread::id, boost::std::thread::id)”时  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(235): error C2678: 二进制“<”: 没有找到接受“boost::std::thread::id”类型的左操作数的运算符(或没有可接受的转换)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(218): note: 可能是“bool boost::std::operator <(boost::std::thread::id,boost::std::thread::id) noexcept”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(235): note: 尝试匹配参数列表“(boost::std::thread::id, boost::std::thread::id)”时  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(240): error C2988: 不可识别的模板声明/定义  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(240): error C2143: 语法错误: 缺少“;”(在“<”的前面)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(240): error C2059: 语法错误:“<”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(243): error C2143: 语法错误: 缺少“;”(在“{”的前面)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(243): error C2447: “{”: 缺少函数标题(是否是老式的形式表?)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(258): error C2888: “boost::hash<boost::std::thread::id>”: 不能在命名空间“std”内定义符号  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(256): error C2059: 语法错误:“return”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(256): error C2238: 意外的标记位于“;”之前  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(258): error C2059: 语法错误:“}”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\thread(258): error C2143: 语法错误: 缺少“;”(在“}”的前面)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(36): error C2061: 语法错误: 标识符“_Mymtx”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(36): error C4430: 缺少类型说明符 - 假定为 int。注意: C++ 不支持默认 int  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(36): warning C4183: “_Mtx_init_in_situ”: 缺少返回类型；假定为返回“int”的成员函数  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(39): error C2588: “::~_Mutex_base”: 非法的全局 析构函数  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(41): error C3861: “_Mymtx”: 找不到标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(41): error C3861: “_Mtx_destroy_in_situ”: 找不到标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(44): error C4430: 缺少类型说明符 - 假定为 int。注意: C++ 不支持默认 int  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(44): error C2143: 语法错误: 缺少“,”(在“&”的前面)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(45): error C2143: 语法错误: 缺少“;”(在“&”的前面)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(45): error C4430: 缺少类型说明符 - 假定为 int。注意: C++ 不支持默认 int  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(45): error C2143: 语法错误: 缺少“,”(在“&”的前面)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(45): error C2059: 语法错误:“=”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(49): error C3861: “_Mymtx”: 找不到标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(49): error C3861: “_Mtx_lockX”: 找不到标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(54): error C3861: “_Mymtx”: 找不到标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(54): error C3861: “_Mtx_trylockX”: 找不到标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(54): error C2065: “_Thrd_success”: 未声明的标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(59): error C3861: “_Mymtx”: 找不到标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(59): error C3861: “_Mtx_unlockX”: 找不到标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(64): error C2146: 语法错误: 缺少“;”(在标识符“native_handle”的前面)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(65): error C2143: 语法错误: 缺少“;”(在“{”的前面)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(65): error C2447: “{”: 缺少函数标题(是否是老式的形式表?)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(69): error C2059: 语法错误:“private”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(70): error C2255: “friend”: 不允许位于类定义之外  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(70): warning C4091: “”: 没有声明变量时忽略“std::condition_variable”的左侧  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(71): error C2255: “friend”: 不允许位于类定义之外  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(71): warning C4091: “”: 没有声明变量时忽略“std::condition_variable_any”的左侧  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(76): error C2146: 语法错误: 缺少“;”(在标识符“_Mymtx”的前面)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(77): error C2143: 语法错误: 缺少“;”(在“{”的前面)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(77): error C2447: “{”: 缺少函数标题(是否是老式的形式表?)  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(83): error C3861: “_Mutex_base”: 找不到标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(84): error C2504: “_Mutex_base”: 未定义基类  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(87): error C2059: 语法错误:“:”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(96): error C3861: “_Mutex_base”: 找不到标识符  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(97): error C2504: “_Mutex_base”: 未定义基类  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(100): error C2059: 语法错误:“:”  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(171): error C2653: “chrono”: 不是类或命名空间名称  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(313): note: 参见对正在编译的 类 模板 实例化 "unique_lock<_Mutex>" 的引用  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(171): error C4430: 缺少类型说明符 - 假定为 int。注意: C++ 不支持默认 int  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(171): error C2988: 不可识别的模板声明/定义  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\mutex(171): fatal error C1003: 错误计数超过 100；正在停止编译  
1>已完成生成项目“ConsoleApplication1.vcxproj”的操作 - 失败。  
========== 生成: 成功 0 个，失败 1 个，最新 0 个，跳过 0 个 ==========

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-04-19 17:43:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1578#issuecomment-484968183  

Since this does not compile for you, it looks like it is an Asio problem and not a Beast problem:  
```  
#include <windows.h>  
#include <boost/asio.hpp>  
int main(int argc, char** argv)  
{  
    return 0;  
}  
```  
  
These links might help:  
  
https://stackoverflow.com/questions/38201102/including-boost-network-libraries-makes-windows-h-functions-undefined  
  
http://boost.2283326.n4.nabble.com/asio-header-order-dependence-with-lt-windows-h-gt-td2618424.html  
  
https://github.com/Microsoft/vcpkg/issues/591  
  
https://www.linuxquestions.org/questions/programming-9/preprocessor-directives-boost-and-winsock-h-already-included-910697/  
  
https://sourceforge.net/p/asio/mailman/message/3802479/

---

## Comment 7

> Username: jackiehe  
> Created at: 2019-04-19 17:48:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1578#issuecomment-484969396  

thank you , i test your code, it is no problem. this problem is cased by beast. i had upload my code.   
build tools is vistual studio 2017, 15.9.11.  windows sdk is windows 10.0.17763.132  
[ConsoleApplication1.zip](https://github.com/boostorg/beast/files/3098895/ConsoleApplication1.zip)

---

## Comment 8

> Username: jackiehe  
> Created at: 2019-04-19 17:52:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1578#issuecomment-484970391  

#define WIN32_LEAN_AND_MEAN // 从 Windows 头文件中排除极少使用的信息  
#include <windows.h>  
#include <boost/asio.hpp>  
int main(int argc, char** argv)  
{  
return 0;  
}  
  
  
this code is not problem.

---

## Comment 9

> Username: jackiehe  
> Created at: 2019-04-19 17:53:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1578#issuecomment-484970441  

// #define WIN32_LEAN_AND_MEAN // 从 Windows 头文件中排除极少使用的信息  
#include <windows.h>  
#include <boost/asio.hpp>  
int main(int argc, char** argv)  
{  
return 0;  
}  
1>------ 已启动全部重新生成: 项目: ConsoleApplication1, 配置: Debug Win32 ------  
1>pch.cpp  
1>ConsoleApplication1.cpp  
1>d:\bak2\boost\boost_1_70_0\boost\asio\detail\socket_types.hpp(24): fatal error C1189: #error: WinSock.h has already been included  
1>已完成生成项目“ConsoleApplication1.vcxproj”的操作 - 失败。  
========== 全部重新生成: 成功 0 个，失败 1 个，跳过 0 个 ==========  
  
  
this code have one error.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-04-19 18:18:28 UTC  
> Updated at: 2019-04-19 18:18:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1578#issuecomment-484976626  

As I said, this is not a Beast problem. You must define `NOMINMAX` before including Windows.h. Try this program:  
```  
#define NOMINMAX  
#define WIN32_LEAN_AND_MEAN  
#include <windows.h>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/websocket.hpp>  
int main(int argc, char** argv)  
{  
    return 0;  
}  
```

---

## Comment 11

> Username: jackiehe  
> Created at: 2019-04-19 18:20:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1578#issuecomment-484977283  

ok,  thank you very much.  i test it no problem.  i hope you fix later.

---

## Comment 12

> Username: jackiehe  
> Created at: 2019-04-19 18:48:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1578#issuecomment-484984297  

all my code is use this style :        (std::min)(x, y);    
you do not need define NOMINMAX,  when you have time, you can fix it.  
it is my recommand.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2019-04-19 18:48:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1578#issuecomment-484984420  

Yes I agree, the fix will go into v253.

---

## Comment 14

> Username: jackiehe  
> Created at: 2019-04-19 18:50:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1578#issuecomment-484984737  

the build error is very hard find, because many many and error code is not show.
