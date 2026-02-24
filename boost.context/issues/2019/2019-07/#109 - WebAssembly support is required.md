# #109 - WebAssembly support is required [Open]

> Username: Klayflash  
> Created at: 2019-07-16 12:59:42 UTC  
> Updated at: 2021-07-15 23:20:11 UTC  
> Url: https://github.com/boostorg/context/issues/109  

Is WebAssembly (WASM) (https://webassembly.org/) supported?  
  
C++ to WASM can be used via https://emscripten.org/.

---

## Comment 1

> Username: olk  
> Created at: 2019-07-16 13:33:02 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-511817540  

no - it isn't

---

## Comment 2

> Username: Klayflash  
> Created at: 2019-07-16 13:48:48 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-511823734  

Thanks for the fast answer!  
  
Is it possible? If possible then I think it's good idea to add support.  
  
I've found  
https://github.com/WebAssembly/design/blob/master/FutureFeatures.md  
  
> Coroutines will eventually be part of C++ and is already popular in other programming languages that WebAssembly will support.

---

## Comment 3

> Username: Klayflash  
> Created at: 2019-07-21 14:56:59 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-513561520  

I think it can be possible via emscripten_coroutine_create, emscripten_coroutine_next and emscripten_yield.  
I've implemented a small class Context with two tests.  
Compile flags:  
  
> -s ASYNCIFY=1  
> -s WASM=0  
> -std=c++17  
> -s DISABLE_EXCEPTION_CATCHING=0  
  
```  
#include <cstdio>  
#include <string>  
#include <cassert>  
#include <sstream>  
#include <boost/noncopyable.hpp>  
#include <boost/current_function.hpp>  
#include <emscripten.h>  
  
using std::string;  
  
void trace_funct(int line,const char* funct, const string& str)  
{  
  emscripten_log( EM_LOG_CONSOLE, "%03d %s %s", line, funct, str.c_str());  
}  
  
string tostr(int v)  
{  
  std::ostringstream ss;  
  ss << v;  
  return ss.str();  
}  
  
#define TRACE(str) trace_funct(__LINE__,BOOST_CURRENT_FUNCTION,str)  
  
class Context  
  : public boost::noncopyable  
{  
public:  
  typedef void (*Funct)(void* arg);  
public:  
  //! empty constructor gets thread context  
  Context()  
  {  
    assert(!s_InsideCoroutine);  
    assert(!s_ThreadContextPresent);  
    m_ThreadContext = true;  
    s_ThreadContextPresent = true;  
    m_Coroutine = 0;  
  }  
  Context(Funct funct,void* arg)  
  {  
    m_Coroutine = emscripten_coroutine_create(funct, arg, 0);  
  }  
  ~Context()  
  {  
    if ( m_ThreadContext ) {  
      TRACE("thread context");  
      assert(s_ThreadContextPresent);  
      s_ThreadContextPresent = false;  
    }  
    else {  
      TRACE("coroutine context");  
      s_SwitchAllowed = false;  
      int res = emscripten_coroutine_next(m_Coroutine); // free memory  
      TRACE("next done");  
      assert(res==0);  
      s_SwitchAllowed = true;  
    }  
  }  
  void switchTo()  
  {  
    TRACE("");  
    assert(s_SwitchAllowed);  
    if ( s_InsideCoroutine ) {  
      TRACE("insideCoroutine");  
      if ( m_ThreadContext ) {  
        TRACE("preparing yeld to thread context");  
        s_Next = nullptr;  
      }  
      else {  
        TRACE("preparing yeld to antother coroutine");  
        s_Next = this;  
        s_SwitchRequired = true;  
      }  
      TRACE("yelding...");  
      emscripten_yield();  
      TRACE("yelding done");  
    }  
    else {  
      TRACE("outsideCoroutine");  
      assert(!m_ThreadContext);  
      s_Next = this;  
      for(;;) {  
        s_InsideCoroutine = true;  
        s_SwitchRequired = false;  
        TRACE("calling coroutine_next...");  
        int res = emscripten_coroutine_next(s_Next->m_Coroutine);  
        TRACE("calling coroutine_next done res="+tostr(res));  
        s_InsideCoroutine = false;  
        assert(res != 0);  
        if ( !s_SwitchRequired ) {  
          assert(!s_Next);  
          TRACE("exiting to thread context");  
          break;  
        }  
      }  
    }  
  }  
private:  
  emscripten_coroutine m_Coroutine;  
  bool m_ThreadContext=false;  
  static bool s_ThreadContextPresent; // TODO: make thread local  
  static bool s_InsideCoroutine; // TODO: make thread local  
  static bool s_SwitchRequired; // TODO: make thread local  
  static bool s_SwitchAllowed; // TODO: make thread local  
  static Context* s_Next; // TODO: make thread local  
};  
  
// static   
bool Context::s_ThreadContextPresent = false;  
// static   
bool Context::s_InsideCoroutine = false;  
// static   
bool Context::s_SwitchRequired = false;  
// static   
bool Context::s_SwitchAllowed = true;  
// static   
Context* Context::s_Next = nullptr;  
//////////////////////////////////////////////////////  
  
  
  
namespace switching_between_child_and_thread {  
  
struct TestParams  
{  
  Context* ctxThread;  
  Context* ctx1;  
};  
  
void fun1(void* arg)  
{  
  TestParams& tp = *(TestParams*)arg;  
  for(int i=0;i<100;++i) {  
    TRACE("switching to ctxThread...");  
    tp.ctxThread->switchTo();  
    TRACE("switching to ctxThread done");  
  }  
}  
  
void test()  
{  
  TRACE("");  
  TestParams tp;  
  Context ctxThread;  
  Context ctx1(fun1,&tp);  
  tp.ctxThread = &ctxThread;  
  tp.ctx1 = &ctx1;  
  for(int i=0;i<100;++i) {  
    TRACE("switching to ctx1...");  
    tp.ctx1->switchTo();  
    TRACE("switching to ctx1 done");  
  }  
  TRACE("return");  
}  
  
  
} // ns  
  
namespace switch_between_child_contexts {  
  
struct TestParams  
{  
  Context* ctxThread;  
  Context* ctx1;  
  Context* ctx2;  
};  
  
void fun1(void* arg)  
{  
  TestParams& tp = *(TestParams*)arg;  
  TRACE("");  
  for(int i=0;i<100;++i) {  
    TRACE("switching to ctx2...");  
    tp.ctx2->switchTo();  
    TRACE("switching to ctx2 done");  
  }  
  TRACE("switching to ctxThread...");  
  tp.ctxThread->switchTo();  
  TRACE("switching to ctxThread done");  
  TRACE("return");  
}  
  
void fun2(void* arg)  
{  
  TestParams& tp = *(TestParams*)arg;  
  TRACE("");  
  for(int i=0;i<100;++i) {  
    TRACE("switching to ctx1...");  
    tp.ctx1->switchTo();  
    TRACE("switching to ctx1 done");  
  }  
  TRACE("return");  
}  
  
void test()  
{  
  TRACE("");  
  TestParams tp;  
  Context ctxThread;  
  Context ctx1(fun1,&tp);  
  Context ctx2(fun2,&tp);  
  tp.ctxThread = &ctxThread;  
  tp.ctx1 = &ctx1;  
  tp.ctx2 = &ctx2;  
  TRACE("switching to ctx1");  
  tp.ctx1->switchTo();  
  TRACE("return");  
}  
  
} // ns  
  
  
int main()  
{  
  TRACE("");  
  
  switching_between_child_and_thread::test();  
  switch_between_child_contexts::test();  
  
  
  TRACE("return");  
  return 0;  
}  
  
```

---

## Comment 4

> Username: unicomp21  
> Created at: 2020-01-26 19:20:27 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-578533753  

clang can now target webassembly, does that change things on this front?

---

## Comment 5

> Username: unicomp21  
> Created at: 2020-01-26 19:22:30 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-578533943  

If we could easily make boost fibers work on clang/webassembly, a plethora of opportunities would be opened up.  In addition, I think the emscripten ASYNCIFY stuff might have issues.

---

## Comment 6

> Username: olk  
> Created at: 2020-01-27 05:05:26 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-578592917  

I'm not familiar with webassembly...  
boost.context does use the calling convention and does some tricks like swapping stacks.  
I don't know if this is applicable to webassembly.

---

## Comment 7

> Username: unicomp21  
> Created at: 2020-01-28 00:46:32 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-579026753  

Any idea who we could ping?  I'm trying to reach Gor Nishanov, not sure if anyone else might know?

---

## Comment 8

> Username: unicomp21  
> Created at: 2020-01-28 00:47:20 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-579026926  

Perhaps we could write a test using emscripten?

---

## Comment 9

> Username: unicomp21  
> Created at: 2020-01-28 00:50:16 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-579027618  

It's hard for me to put in words what a huge deal this could be, all kinds of projects become possible within the web browser, all legacy code bases containing threads can be leveraged in the browser using fibers.

---

## Comment 10

> Username: olk  
> Created at: 2020-01-29 05:36:02 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-579602030  

Supporitng WebAssembly requires patching LLVM and Emscripten - I think this hughe amount of work is only justified if fcontext's std-equivalent in [P0876R10](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2020/p0876r10.pdf) has been accepted.

---

## Comment 11

> Username: unicomp21  
> Created at: 2020-01-29 11:27:01 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-579713052  

I wonder if it would be easier to implement initially in wasm3?  
  
https://github.com/wasm3/wasm3

---

## Comment 12

> Username: Akaricchi  
> Created at: 2020-03-06 22:50:16 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-595998014  

This can now be implemented for Emscripten with the [new fibers API](https://emscripten.org/docs/api_reference/fiber.h.html).

---

## Comment 13

> Username: olk  
> Created at: 2020-04-24 09:02:55 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-618894549  

you are welcome to provide a patch

---

## Comment 14

> Username: olk  
> Created at: 2021-07-13 19:06:43 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-879330278  

I think this is not possible.

---

## Comment 15

> Username: Klayflash  
> Created at: 2021-07-14 06:42:57 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-879635976  

The feature is not planned more?

---

## Comment 16

> Username: olk  
> Created at: 2021-07-14 08:53:35 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-879718076  

I think it is not possible to implement support for WebAssembly.

---

## Comment 17

> Username: unicomp21  
> Created at: 2021-07-14 11:08:55 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-879802193  

Can we leave this open for some brave soul who might come along later?

---

## Comment 18

> Username: olk  
> Created at: 2021-07-14 13:38:33 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-879900283  

boost.context accesses/uses the registers of the CPU while webassembly is bytecode running in a virtual machine - therefore your request makes no sense.

---

## Comment 19

> Username: unicomp21  
> Created at: 2021-07-14 13:48:56 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-879908723  

@olk the vm doesn't have a way to mimick registers?

---

## Comment 20

> Username: Akaricchi  
> Created at: 2021-07-15 03:21:17 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-880361500  

It's possible with the Asyncify transform and some help from the embedder/runtime — which is what the emscripten fibers feature is all about. An emscripten-specific backend is perfectly feasible. [Here](https://github.com/taisei-project/koishi/blob/master/src/emscripten/emscripten.c) is the implementation in my own C coroutine library.

---

## Comment 21

> Username: olk  
> Created at: 2021-07-15 04:42:08 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-880386272  

I'll take a look at it.

---

## Comment 22

> Username: olk  
> Created at: 2021-07-15 05:31:42 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-880408167  

emscripten fiber seams not to be used - at least Google couldn't find examples/usage of emscripten fibers.

---

## Comment 23

> Username: Akaricchi  
> Created at: 2021-07-15 23:20:11 UTC  
> Url: https://github.com/boostorg/context/issues/109#issuecomment-881069823  

I literally just linked you a usage example (we use it in the web port of Taisei Project). [Here](https://github.com/Wizcorp/byuu-web/blob/a45b6a7d47098fc006c484480a3d759cc5e45c4a/libco/emscripten_fiber.c) is another one from an emscripten port of the byuu emulator. [Another](https://github.com/BinBashBanana/webretro/blob/99b4fa6c4681a8c047183f2ff716de3d48ee4ccd/source/overrides/cores/mupen64plus-libretro-nx/libretro-common/libco/emscripten_fiber.c) emulator. [Here](https://github.com/edubart/minicoro) is another multi-backend coroutine library that uses emscripten fibers. [And another one](https://github.com/Marcos30004347/Jobin/blob/11336e7420e2808e547c74e38a7b8da102a573b3/jobin/fiber.cpp). [And here it is used in Ruby](https://github.com/ruby/ruby/blob/1467328edc877ada0361e89f55158d2ed1bbb075/coroutine/emscripten/Context.h).  
  
I also linked [the documentation](https://emscripten.org/docs/api_reference/fiber.h.html) earlier.
