# #62 - jump_fcontext crashes in 32bit iOS simulator [Closed]

> Username: mjohns  
> Created at: 2017-07-28 16:19:47 UTC  
> Updated at: 2017-08-08 14:01:57 UTC  
> Closed at: 2017-08-08 14:01:57 UTC  
> Url: https://github.com/boostorg/context/issues/62  

jump_fcontext crashes in jump_i386_sysv_macho_gas.S when trying mov the "to" fctx parameter into eax. This is easy to reproduce on a mac by compiling a simple program with the assembly files and a simple main function. (This example works with the old assembly files before the refactoring to have transfer_t etc)  
  
With a little debugging it seems like the offsets to get the "to" and "data" parameters are not correct anymore. The seem to be off by 4 bytes.  
  
Compile with:  g++ -g *.S main.cc -m32 -std=c++11  
  
#include <cstdio>  
#include <cstdlib>  
  
typedef void* fcontext_t;  
  
struct transfer_t {  
  fcontext_t fctx;  
  void* data;  
};  
  
extern "C"  
transfer_t jump_fcontext(fcontext_t const to, void* vp);  
extern "C"  
fcontext_t make_fcontext(void* stack_pointer, size_t size,  
                                           void (*fn)(transfer_t));  
  
void Run(transfer_t t) {  
  printf("not reached");  
}  
  
int main() {  
  const size_t size = 131072;  
  char* smem = (char*)malloc(size);  
  void* sp = smem + size;  
    
  fcontext_t fctx = make_fcontext(sp, size, &Run);  
  // jump_fcontext crashes.  
  jump_fcontext(fctx, nullptr);  
}

---

## Comment 1

> Username: olk  
> Created at: 2017-07-28 18:40:40 UTC  
> Url: https://github.com/boostorg/context/issues/62#issuecomment-318731899  

boost's regression (develop - http://www.boost.org/development/tests/develop/developer/context.html) tests for context/coroutine/coroutine2/fiber show no errors on MacOS X.  
Note that jump_fcontext is a internal API and should not be used by users (not supported).  
  
seams to me this bug report is invalid

---

## Comment 2

> Username: mjohns  
> Created at: 2017-07-28 20:13:19 UTC  
> Url: https://github.com/boostorg/context/issues/62#issuecomment-318751081  

Are there actually 32bit MacOS tests? I only see one test for darwin where windows has multiple 32bit ones listed. It is admittedly a rare platform since all osx machines are typically 64bit

---

## Comment 3

> Username: mjohns  
> Created at: 2017-07-28 22:55:17 UTC  
> Url: https://github.com/boostorg/context/issues/62#issuecomment-318780155  

I was able to build 32bit osx boost and use the real execution_context interface to reproduce the same crash. I don't think the i386_macho_gas assembly files are exercised in the regression tests.  
  
Here is the example program that crashed in the same way (taken from one of the regression tests):  
`  
#include <boost/context/execution_context_v2.hpp>  
#include <cstdio>  
  
namespace ctx = boost::context;  
  
double value3 = 0.0;  
  
ctx::execution_context< void > fn3( double d, ctx::execution_context< void > && ctx) {  
  d += 3.45;  
  value3 = d;  
  return std::move( ctx);  
}  
  
int main() {  
  double d = 7.13;  
  ctx::execution_context< void > ctx( fn3, d);  
  ctx();  
  
  printf("value3 %f\n", value3);  
  // expected 10.58  
  return 0;  
}  
`

---

## Comment 4

> Username: olk  
> Created at: 2017-07-29 15:44:40 UTC  
> Url: https://github.com/boostorg/context/issues/62#issuecomment-318839220  

Hmm, I don't own a Mac - so I can't test/debug it ... could you tell me the code line in jump_fcontext that causes the fault, please?

---

## Comment 5

> Username: mjohns  
> Created at: 2017-07-29 18:43:52 UTC  
> Url: https://github.com/boostorg/context/issues/62#issuecomment-318850387  

It crashes on line 56 with the boost 1.64 version.  
 /* return parent fcontext_t */  
    movl  %ecx, (%eax)  
  
I first notice problems when it is trying to get the fctx "to" parameter and load it into eax. If the data param is nullptr this line crashes.

---

## Comment 6

> Username: mjohns  
> Created at: 2017-07-29 18:44:53 UTC  
> Url: https://github.com/boostorg/context/issues/62#issuecomment-318850451  

/* first arg of jump_fcontext() == fcontext to jump to */  
    movl  0x20(%esp), %eax  
  
    /* second arg of jump_fcontext() == data to be transferred */  
    movl  0x24(%esp), %edx  
  
Inspecting the memory it seems like the to parameter is at 0x1C(%esp) and data is at 0x20(%esp)

---

## Comment 7

> Username: olk  
> Created at: 2017-07-30 08:01:20 UTC  
> Updated at: 2017-07-30 08:02:06 UTC  
> Url: https://github.com/boostorg/context/issues/62#issuecomment-318885696  

the code works on i386 ...  
what happens if you change the code in jump_fcontext to:  
  
`movl  0x1c(%esp), %eax`  
` movl  0x20(%esp), %edx`

---

## Comment 8

> Username: mjohns  
> Created at: 2017-07-31 18:23:06 UTC  
> Url: https://github.com/boostorg/context/issues/62#issuecomment-319153746  

I get a misaligned stack exception in context_entry which is being entered via the trampoline in make_fcontext. I would guess it is related to the 16byte stack alignment requirement described in: https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/LowLevelABI/130-IA-32_Function_Calling_Conventions/IA32.html#//apple_ref/doc/uid/TP40002492-SW7

---

## Comment 9

> Username: mjohns  
> Created at: 2017-08-02 16:57:59 UTC  
> Url: https://github.com/boostorg/context/issues/62#issuecomment-319733740  

I think the main issue here is the special case for returning structs of 8bytes in size on osx (which is listed as a difference between the osx abi and system v abi). The first pointer in the struct is returned in eax and the second pointer is returned in edx. The memory layout in the comments seems to expect that the return address of the struct is passed on the stack.  
  
Inspecting the assembly for a simple function with the signature of jump_fcontext I can see that they do return the transfer_t in registers eax,edx.  
  
See https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/LowLevelABI/130-IA-32_Function_Calling_Conventions/IA32.html  
  
Looking into a potential fix now.

---

## Comment 10

> Username: olk  
> Created at: 2017-08-02 17:38:01 UTC  
> Url: https://github.com/boostorg/context/issues/62#issuecomment-319744459  

> Looking into a potential fix now.  
That would be create, thanks!

---

## Comment 11

> Username: mjohns  
> Created at: 2017-08-02 19:54:28 UTC  
> Url: https://github.com/boostorg/context/issues/62#issuecomment-319780112  

Created a pull request: #64

---

## Comment 12

> Username: olk  
> Created at: 2017-08-03 05:21:50 UTC  
> Url: https://github.com/boostorg/context/issues/62#issuecomment-319870756  

the unit-tests (sub-directory 'test') work?

---

## Comment 13

> Username: mjohns  
> Created at: 2017-08-03 17:15:58 UTC  
> Url: https://github.com/boostorg/context/issues/62#issuecomment-320033455  

I ran select execution_context tests manually in separate binaries using the object files from building boost. I am not familiar with how the boost build system works in order to run the real test suite and could not find any documentation.  
  
To build boost I ran:  
./bootstrap.sh toolset=darwin architecture=x86 address-model=32  
./b2 --build-dir=build-out toolset=darwin architecture=x86 address-model=32 stage  
g++ -m32 -std=c+11 ....
