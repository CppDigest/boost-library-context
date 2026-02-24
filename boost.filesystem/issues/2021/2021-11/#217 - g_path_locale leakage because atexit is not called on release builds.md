# #217 - g_path_locale leakage because atexit is not called on release builds [Closed]

> Username: Maximus5  
> Created at: 2021-11-22 08:34:18 UTC  
> Updated at: 2021-11-23 00:11:48 UTC  
> Closed at: 2021-11-22 14:28:22 UTC  
> Url: https://github.com/boostorg/filesystem/issues/217  

Platform: Windows  
Compiler: VS 16.11.6, CL 19.29.30137, LINK 14.29.30137.0  
Boost version: 1.77 and 1.78.0.b1  
Boost build: b2.exe runtime-link=static  
  
We recently have updated to boost 1.77 from boost 1.76 (was an update in vcpkg) and AppVerifier started to complain about memory leakage.  
  
The leak happens because `destroy_path_globals` is not called during our dll unloading. And this happens because `init_path_globals` is not called during CRT startup. Happens only with **release** builds.  
  
This trick with allocating a pointer in the `.CRT$XCM` works with debug builds, but unfortunately the `p_init_path_globals` variable is not present in release binaries due to aggressive optimization.  
  
in boost 1.77  
```  
#pragma section(".CRT$XCM", long, read)  
__declspec(allocate(".CRT$XCM")) extern const init_func_ptr_t p_init_path_globals = &init_path_globals;  
```  
  
or current master  
```  
#pragma section(".CRT$XCM", long, read)  
__declspec(allocate(".CRT$XCM")) BOOST_ATTRIBUTE_UNUSED BOOST_FILESYSTEM_ATTRIBUTE_RETAIN  
extern const init_func_ptr_t p_init_path_globals = &init_path_globals;  
```  
  
The only way I've found for now to workaround the problem - use of statically initialized object which gets the address of the variable. Sample fix for 1.77 works for me.  
  
```diff  
diff --git a/src/path.cpp b/src/path.cpp  
index cc63e04..f72dffe 100644  
--- a/src/path.cpp  
+++ b/src/path.cpp  
@@ -1163,6 +1163,23 @@ BOOST_FILESYSTEM_INIT_FUNC init_path_globals()  
 #pragma section(".CRT$XCM", long, read)  
 __declspec(allocate(".CRT$XCM")) extern const init_func_ptr_t p_init_path_globals = &init_path_globals;  
   
+struct path_globals_atexit_initilizer  
+{  
+    const void* ptr_save;  
+  
+    path_globals_atexit_initilizer()  
+    {  
+        ptr_save = &p_init_path_globals;  
+    }  
+  
+    ~path_globals_atexit_initilizer()  
+    {  
+        ptr_save = nullptr;  
+    }  
+};  
+  
+const path_globals_atexit_initilizer g_path_globals_atexit_initilizer = {};  
+  
 #else // _MSC_VER >= 1400  
   
 #if (_MSC_VER >= 1300) // 1300 == VC++ 7.0  
```  
  
Are there chances such a patch to be accepted?

---

## Comment 1

> Username: Lastique  
> Created at: 2021-11-22 13:04:38 UTC  
> Url: https://github.com/boostorg/filesystem/issues/217#issuecomment-975501440  

Thanks for the report and the suggested change. I have committed a workaround in a feature branch. Please, see if you could test it.

---

## Comment 2

> Username: Kojoley  
> Created at: 2021-11-22 20:23:40 UTC  
> Url: https://github.com/boostorg/filesystem/issues/217#issuecomment-975888868  

This looks very similar to https://github.com/boostorg/thread/pull/320 where the root cause was that `extern` keyword in anonymous namespace does nothing. I don't know why compilers don't warn on that, or they do but no-one paid attention?  
  
I'm afraid the workaround is unstable. Can't the compiler eliminate `g_path_globals_atexit_initilizer` and then remove `p_init_path_globals` like before?

---

## Comment 3

> Username: Lastique  
> Created at: 2021-11-22 22:09:00 UTC  
> Updated at: 2021-11-22 22:13:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/217#issuecomment-975960938  

> `extern` keyword in anonymous namespace does nothing.  
  
It sure does, in terms of language - it makes the object have external linkage. The fact that other TUs can't reference the object through its name is entirely separate from linkage.  
  
> I don't know why compilers don't warn on that, or they do but no-one paid attention?  
  
MSVC doesn't have such warning, to my knowledge.  
  
> Can't the compiler eliminate `g_path_globals_atexit_initilizer` and then remove `p_init_path_globals` like before?  
  
Yes, that is possible. The workaround relies on that the `globals_retainer` constructor has a user-defined body that potentially could have side effects. As long as the compiler is not smart enough to see that it doesn't affect any global state, it must generate a global initializer for it, which has a reference to `p_init_path_globals`.  
  
My opinion on this is that this is a compiler bug because clearly removing `p_init_path_globals` does affect program behavior. It would have been a valid optimization if `p_init_path_globals` was a regular global variable, but it is not.

---

## Comment 4

> Username: Kojoley  
> Created at: 2021-11-22 22:13:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/217#issuecomment-975964275  

> My opinion on this is that this is a compiler bug because clearly removing `p_init_path_globals` does affect program behavior. It would have been a valid optimization if `p_init_path_globals` was a regular global variable, but it is not.  
  
All linkers are stripping unused internal linking symbols, it is just MS was slow to implement that and now we are discovering code issues. MS also told multiple times it is not a bug and works as intended. You can though tell the linker to not strip a particular symbols if you don't want to fix the code.

---

## Comment 5

> Username: Lastique  
> Created at: 2021-11-22 22:17:02 UTC  
> Url: https://github.com/boostorg/filesystem/issues/217#issuecomment-975966307  

> All linkers are stripping unused internal linking symbols, it is just MS was slow to implement that and now we are discovering code issues.  
  
Sure, that is a valid optimization. But as with any other optimization, it must not affect code behavior. If it does, it is a compiler bug in my book.  
  
> You can though tell the linker to not strip a particular symbols if you don't want to fix the code.  
  
How? I did not find an attribute similar to `__attribute__((used))` in MSVC docs.

---

## Comment 6

> Username: Kojoley  
> Created at: 2021-11-22 22:21:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/217#issuecomment-975968961  

> > `extern` keyword in anonymous namespace does nothing.  
>   
> It sure does, in terms of language - it makes the object have external linkage. The fact that other TUs can't reference the object through its name is entirely separate from linkage.  
  
All compilers disagree with you. The [\[basic.link\]/4.7](https://eel.is/c++draft/basic.link#4.7) says:  
> has its linkage determined as follows:  
>  
> - if the enclosing namespace has internal linkage, the name has internal linkage;  
  
> > You can though tell the linker to not strip a particular symbols if you don't want to fix the code.  
>   
> How? I did not find an attribute similar to `__attribute__((used))` in MSVC docs.  
  
Move the symbol to a named namespace.

---

## Comment 7

> Username: Lastique  
> Created at: 2021-11-22 22:44:42 UTC  
> Url: https://github.com/boostorg/filesystem/issues/217#issuecomment-975982322  

> Move the symbol to a named namespace.  
  
This doesn't work with LTO - the linker still removes the symbol.

---

## Comment 8

> Username: Kojoley  
> Created at: 2021-11-22 23:11:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/217#issuecomment-975996081  

> > Move the symbol to a named namespace.  
>   
> This doesn't work with LTO - the linker still removes the symbol.  
  
Strange, I thought that the external linkage symbol should be still available in case of shared library needs it.  
  
---  
  
If `path::compare(const value_type*)` overload would have been cheaper (currently it does `compare(path(s))` ) these gymnastics with global variables were simply unnecessary.

---

## Comment 9

> Username: Lastique  
> Created at: 2021-11-22 23:20:17 UTC  
> Updated at: 2021-11-22 23:25:34 UTC  
> Url: https://github.com/boostorg/filesystem/issues/217#issuecomment-976000986  

> Strange, I thought that the external linkage symbol should be still available in case of shared library needs it.  
  
Boost.Filesystem library doesn't export it (and I don't want to export it), so when the shared library is LTO'ed, it becomes obviously "unused". Same will happen when you build an LTO'ed executable linking with static library of Boost.Filesystem.  
  
The bug is the same all along, regardless of the namespace thing. It is that the compiler disregards the side effect of the variable being placed in a special data section, which makes it not your regular variable that can be removed with no consequences.  
  
> If `path::compare(const value_type*)` overload would have been cheaper (currently it does `compare(path(s))` ) these gymnastics with global variables were simply unnecessary.  
  
The locale and other globals are not only used for `path::compare`, so no, these early initialization hacks would have been necessary anyway. BTW, `compare(path(s))` doesn't use the global locale when `s` is `const value_type*`.

---

## Comment 10

> Username: Kojoley  
> Created at: 2021-11-22 23:36:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/217#issuecomment-976008644  

> > Strange, I thought that the external linkage symbol should be still available in case of shared library needs it.  
>   
> Boost.Filesystem library doesn't export it (and I don't want to export it), so when the shared library is LTO'ed, it becomes obviously "unused". Same will happen when you build an LTO'ed executable linking with static library of Boost.Filesystem.  
  
Again, if you don't want an unused symbol to remain - you either make its linkage external, or tell to the linker to not remove it (if you love hacks and workarounds you can do it even with pragma inside the code).  
  
> The bug is the same all along, regardless of the namespace thing. It is that the compiler disregards the side effect of the variable being placed in a special data section, which makes it not your regular variable that can be removed with no consequences.  
  
Linker knows nothing about side-effects, the 'side-effect' also happens in the Windows PE loader and not in your program.  
  
> > If `path::compare(const value_type*)` overload would have been cheaper (currently it does `compare(path(s))` ) these gymnastics with global variables were simply unnecessary.  
>   
> The locale and other globals are not only used for `path::compare`, so no, these early initialization hacks would have been necessary anyway. BTW, `compare(path(s))` doesn't use the global locale when `s` is `const value_type*`.  
  
There is no need to convert the string, it is already `wchar_t`.

---

## Comment 11

> Username: Lastique  
> Created at: 2021-11-22 23:55:53 UTC  
> Updated at: 2021-11-23 00:11:48 UTC  
> Url: https://github.com/boostorg/filesystem/issues/217#issuecomment-976015958  

> > > Strange, I thought that the external linkage symbol should be still available in case of shared library needs it.  
> >   
> >   
> > Boost.Filesystem library doesn't export it (and I don't want to export it), so when the shared library is LTO'ed, it becomes obviously "unused". Same will happen when you build an LTO'ed executable linking with static library of Boost.Filesystem.  
>   
> Again, if you don't want an unused symbol to remain - you either make its linkage external, or tell to the linker to not remove it (if you love hacks and workarounds you can do it even with pragma inside the code).  
  
Evidently, external linkage is not working, and not the issue in the first place.  
  
No, I don't love the hacks. In fact, I hate this part of the code, especially the ugly stuff I had to write specifically for MSVC. If you can suggest a cleaner way that actually works, then please do, I'd appreciate it. No, named namespace doesn't work.  
  
> > The bug is the same all along, regardless of the namespace thing. It is that the compiler disregards the side effect of the variable being placed in a special data section, which makes it not your regular variable that can be removed with no consequences.  
>   
> Linker knows nothing about side-effects, the 'side-effect' also happens in the Windows PE loader and not in your program.  
  
That's the problem - it must either know what it's doing or stay away from it. If hardcoding the list of special data sections is not kosher then provide a more explicit means so that the intent is more apparent and recognized by the compiler/linker, like `__attribute__((init_priority))`. And if there is something special written in the code, like a data section that the linker doesn't know about, then disable the optimization for that stuff. It's that simple.  
  
> > > If `path::compare(const value_type*)` overload would have been cheaper (currently it does `compare(path(s))` ) these gymnastics with global variables were simply unnecessary.  
> >   
> > The locale and other globals are not only used for `path::compare`, so no, these early initialization hacks would have been necessary anyway. BTW, `compare(path(s))` doesn't use the global locale when `s` is `const value_type*`.  
>   
> There is no need to convert the string, it is already `wchar_t`.  
  
Exactly.
