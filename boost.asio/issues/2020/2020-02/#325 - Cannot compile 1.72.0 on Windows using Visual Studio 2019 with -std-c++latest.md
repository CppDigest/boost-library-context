# #325 - Cannot compile 1.72.0 on Windows using Visual Studio 2019 with /std:c++latest [Closed]

> Username: felixhao28  
> Created at: 2020-02-06 07:25:27 UTC  
> Updated at: 2020-12-30 01:13:07 UTC  
> Closed at: 2020-12-30 01:13:06 UTC  
> Url: https://github.com/boostorg/asio/issues/325  

The source of boost library is acquired from https://www.boost.org/users/history/version_1_72_0.html .  
  
As the title indicates, compilation fails on boost 1.72.0 while 1.71.0 works fine with /std:c++latest on Visual Studio 2019. And /std:c++17 works fine too.  
  
Not sure this is a bug with Visual Studio or boost library. [I opened an issue for Visual Studio team](https://developercommunity.visualstudio.com/content/problem/909011/failed-to-compile-boost-1720-with-stdclatest.html), too.

---

## Comment 1

> Username: kartikeytewari  
> Created at: 2020-02-06 08:54:07 UTC  
> Url: https://github.com/boostorg/asio/issues/325#issuecomment-582799616  

Would you mind sharing the error shown when you tried compiling 1.72.0? It  
would be more useful since I am unable to reproduce the same error.  
  
On Thu, Feb 6, 2020 at 12:55 PM Felix Hao <notifications@github.com> wrote:  
  
> The source of boost library is acquired from  
> https://www.boost.org/users/history/version_1_72_0.html .  
>  
> As the title indicates, compilation fails on boost 1.72.0 while 1.71.0  
> works fine with /std:c++latest on Visual Studio 2019.  
>  
> Not sure this is a bug with Visual Studio or boost library. I opened an  
> issue for Visual Studio team, too.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/boost/issues/369?email_source=notifications&email_token=AJWKDRR554WZXZW5TF3M2OTRBO3PXA5CNFSM4KQX4FO2YY3PNVWWK3TUL52HS4DFUVEXG43VMWVGG33NNVSW45C7NFSM4ILNRUBQ>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AJWKDRST3S3SLGMGDYHOHZ3RBO3PXANCNFSM4KQX4FOQ>  
> .  
>

---

## Comment 2

> Username: felixhao28  
> Created at: 2020-02-06 09:48:18 UTC  
> Url: https://github.com/boostorg/asio/issues/325#issuecomment-582821024  

It prints a lot of errors. The first ones are:  
  
```  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(44,1): error C2062: type 'bool' unexpected (compiling source file JavaPredictionTests.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(44,1): error C2062: type 'bool' unexpected (compiling source file LangUtilsTest.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(45,1): error C2143: syntax error: missing ';' before '{' (compiling source file JavaPredictionTests.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(44,1): error C2062: type 'bool' unexpected (compiling source file ExecutorTest.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(45,1): error C2143: syntax error: missing ';' before '{' (compiling source file LangUtilsTest.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(45,1): error C2447: '{': missing function header (old-style formal list?) (compiling source file JavaPredictionTests.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(44,1): error C2062: type 'bool' unexpected (compiling source file PythonPredictionTests.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(45,1): error C2143: syntax error: missing ';' before '{' (compiling source file ExecutorTest.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(45,1): error C2447: '{': missing function header (old-style formal list?) (compiling source file LangUtilsTest.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(56,30): error C7568: argument list missing after assumed function template 'callable_with' (compiling source file JavaPredictionTests.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(58): message : see reference to class template instantiation 'boost::asio::detail::is_completion_handler_for<T,R(Args...)>' being compiled (compiling source file JavaPredictionTests.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(45,1): error C2143: syntax error: missing ';' before '{' (compiling source file PythonPredictionTests.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(45,1): error C2447: '{': missing function header (old-style formal list?) (compiling source file ExecutorTest.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(56,30): error C7568: argument list missing after assumed function template 'callable_with' (compiling source file LangUtilsTest.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(58): message : see reference to class template instantiation 'boost::asio::detail::is_completion_handler_for<T,R(Args...)>' being compiled (compiling source file LangUtilsTest.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(44,1): error C2062: type 'bool' unexpected (compiling source file PredictionBase.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(56,2): error C2059: syntax error: '>' (compiling source file JavaPredictionTests.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(45,1): error C2447: '{': missing function header (old-style formal list?) (compiling source file PythonPredictionTests.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(56,30): error C7568: argument list missing after assumed function template 'callable_with' (compiling source file ExecutorTest.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(58): message : see reference to class template instantiation 'boost::asio::detail::is_completion_handler_for<T,R(Args...)>' being compiled (compiling source file ExecutorTest.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(44,1): error C2062: type 'bool' unexpected (compiling source file UtilsTest.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(56,2): error C2059: syntax error: '>' (compiling source file LangUtilsTest.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(45,1): error C2143: syntax error: missing ';' before '{' (compiling source file PredictionBase.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(57,1): error C3770: 'unknown-type': is not a valid base class (compiling source file JavaPredictionTests.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(56,30): error C7568: argument list missing after assumed function template 'callable_with' (compiling source file PythonPredictionTests.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(58): message : see reference to class template instantiation 'boost::asio::detail::is_completion_handler_for<T,R(Args...)>' being compiled (compiling source file PythonPredictionTests.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(56,2): error C2059: syntax error: '>' (compiling source file ExecutorTest.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(45,1): error C2143: syntax error: missing ';' before '{' (compiling source file UtilsTest.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(57,1): error C3770: 'unknown-type': is not a valid base class (compiling source file LangUtilsTest.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(45,1): error C2447: '{': missing function header (old-style formal list?) (compiling source file PredictionBase.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(63,1): error C2062: type 'bool' unexpected (compiling source file JavaPredictionTests.cpp)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(56,2): error C2059: syntax error: '>' (compiling source file PythonPredictionTests.cpp)  
```  
  
![image](https://user-images.githubusercontent.com/2417391/73925013-530abc80-4908-11ea-9b90-c43ccbdf1da5.png)  
  
  
The error is definitely reproducible. Another coworker of mine also faces the same issue.

---

## Comment 3

> Username: felixhao28  
> Created at: 2020-02-06 09:50:22 UTC  
> Url: https://github.com/boostorg/asio/issues/325#issuecomment-582821880  

The full error log for reference:  
[boost1.72.0vscppstdlatest.txt](https://github.com/boostorg/boost/files/4164355/boost1.72.0vscppstdlatest.txt)

---

## Comment 4

> Username: felixhao28  
> Created at: 2020-02-06 09:53:46 UTC  
> Updated at: 2020-02-06 09:55:09 UTC  
> Url: https://github.com/boostorg/asio/issues/325#issuecomment-582823213  

I found a minimal reproducible case:  
  
```c++  
#include <boost/asio.hpp>  
  
int main() {  
	return 0;  
}  
```  
  
Compiling this with `/std:c++latest` gives:  
  
![image](https://user-images.githubusercontent.com/2417391/73925936-c7922b00-4909-11ea-81e9-cab7017bb8d2.png)  
  
  
```  
1>------ Build started: Project: Project1, Configuration: Debug x64 ------  
1>Source.cpp  
1>Please define _WIN32_WINNT or _WIN32_WINDOWS appropriately. For example:  
1>- add -D_WIN32_WINNT=0x0601 to the compiler command line; or  
1>- add _WIN32_WINNT=0x0601 to your project's Preprocessor Definitions.  
1>Assuming _WIN32_WINNT=0x0601 (i.e. Windows 7 target).  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(44,1): error C2062: type 'bool' unexpected  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(45,1): error C2143: syntax error: missing ';' before '{'  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(45,1): error C2447: '{': missing function header (old-style formal list?)  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(56,30): error C7568: argument list missing after assumed function template 'callable_with'  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(58): message : see reference to class template instantiation 'boost::asio::detail::is_completion_handler_for<T,R(Args...)>' being compiled  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.24.28314\include\chrono(632): message : see reference to class template instantiation 'std::chrono::duration<double,std::ratio<1,1>>' being compiled  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.24.28314\include\chrono(178): message : see reference to class template instantiation 'std::chrono::duration<__int64,std::nano>' being compiled  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.24.28314\include\chrono(610): message : see reference to class template instantiation 'std::chrono::time_point<std::chrono::steady_clock,std::chrono::nanoseconds>' being compiled  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(56,2): error C2059: syntax error: '>'  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(57,1): error C3770: 'unknown-type': is not a valid base class  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(63,1): error C2062: type 'bool' unexpected  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(64,35): error C2065: 'T': undeclared identifier  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(64,11): error C2923: 'boost::asio::detail::is_completion_signature': 'T' is not a valid template type argument for parameter 'T'  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(64,39): error C2955: 'boost::asio::detail::is_completion_signature': use of class template requires template argument list  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(34): message : see declaration of 'boost::asio::detail::is_completion_signature'  
1>D:\dev\boost_1_72_0\boost\asio\async_result.hpp(69,44): error C2061: syntax error: identifier 'completion_signature'  
...  
```

---

## Comment 5

> Username: felixhao28  
> Created at: 2020-02-10 01:59:56 UTC  
> Url: https://github.com/boostorg/asio/issues/325#issuecomment-583925752  

This might be a duplicate of #312

---

## Comment 6

> Username: velia-ai  
> Created at: 2020-03-25 10:29:14 UTC  
> Url: https://github.com/boostorg/asio/issues/325#issuecomment-603763036  

I experienced the exact same problem and confirm that boost 1.72.0 w/ VS-2019 compiled fine when the language flag is set to /std:c++17 but failed when set to  /std:c++latest

---

## Comment 7

> Username: ghost  
> Created at: 2020-12-30 01:13:05 UTC  
> Url: https://github.com/boostorg/asio/issues/325#issuecomment-752293449  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#747](https://github.com/chriskohlhoff/asio/issues/747).
