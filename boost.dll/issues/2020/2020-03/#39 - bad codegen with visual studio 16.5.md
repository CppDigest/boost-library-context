# #39 - bad codegen with visual studio 16.5 [Closed]

> Username: BurningEnlightenment  
> Created at: 2020-03-23 07:10:04 UTC  
> Updated at: 2020-03-23 07:56:06 UTC  
> Closed at: 2020-03-23 07:49:23 UTC  
> Url: https://github.com/boostorg/dll/issues/39  

~The ["Importing a C function from windows dll" example](https://www.boost.org/doc/libs/1_72_0/doc/html/boost_dll/tutorial.html#boost_dll.tutorial.importing_a_c_function_from_windows_dll) currently leads to bogus assembly generation with visual studio 16.5.~  
  
I refactored the code a bit to get an easily digestible assembly snippet, ~but the issue can also be reproduced with the code from the example copied verbatim~ _and made GetStdHandle_t a goddamn pointer_.  
  
```cpp  
using GetStdHandle_t =  void *(__stdcall *)(unsigned int);  
namespace dll = boost::dll;  
  
struct stable_layout  
{  
    dll::shared_library lib;  
    GetStdHandle_t getStdHandle;  
  
    stable_layout()  
        : lib("Kernel32.dll", dll::load_mode::search_system_folders)  
        , getStdHandle(nullptr)  
    {}  
};  
  
void func_lookup(stable_layout &self)  
{  
    self.getStdHandle = self.lib.get<GetStdHandle_t>("GetStdHandle");  
}  
```  
if compiled with `cl /I..\boost\include /DBOOST_DLL_USE_STD_FS=1 /std:c++17 /EHsc /Zi x.cpp` `func_lookup` will be lowered to the following x86 assembly:  
```asm  
push    ebp  
mov     ebp,esp  
push    ecx  
mov     eax,dword ptr ss:[ebp+0x8]  ; [ebp+0x8]: "self" parameter on stack  
mov     dword ptr ss:[ebp-0x4],eax  
push    x.5A9E50                    ; 5A9E50: "GetStdHandle"  
mov     ecx,dword ptr ss:[ebp-0x4]  
call    x.543BFC                    ; 543BFC: shared_library::get  
                                    ; function address is returned in eax  
mov     ecx,dword ptr ss:[ebp+0x8]  
mov     edx,dword ptr ds:[eax]      ; dereference of eax/function address  
mov     dword ptr ds:[ecx+0x4],edx  ; [ecx+0x4]: self.getStdHandle  
mov     esp,ebp  
pop     ebp  
ret  
```  
The lowered x64 assembly is also borked. Boost version 1.72; [x.cpp](https://github.com/boostorg/dll/files/4367696/x.cpp.txt). [MS DevCom Ticket](https://developercommunity.visualstudio.com/content/problem/958905/bad-codegen-with-boostdll-returned-function-addres.html)

---

## Comment 1

> Username: BurningEnlightenment  
> Created at: 2020-03-23 07:49:23 UTC  
> Url: https://github.com/boostorg/dll/issues/39#issuecomment-602437027  

Nevermind. I somehow completely missed the fact that `shared_library::get` doesn't work with function _pointer_ types. I'm sorry for the noise.
