# #459 - Warnings about vswhere not found under Cygwin [Closed]

> Username: Lastique  
> Created at: 2019-07-10 08:52:53 UTC  
> Updated at: 2019-09-29 14:09:03 UTC  
> Closed at: 2019-09-29 14:09:03 UTC  
> Url: https://github.com/boostorg/build/issues/459  

When invoking `b2` under Cygwin and Cygwin64, it displays the following warning twice:  
  
```  
/bin/sh: C:\Program Files (x86)\Microsoft Visual Studio\Installer\vswhere.exe: command not found  
```  
  
I believe, there is no point in looking for `vswhere` when working on Cygwin or any non-Windows system, is there? If there is, it might be better to just silence the warnings.

---

## Comment 1

> Username: haubi  
> Created at: 2019-09-16 15:07:42 UTC  
> Url: https://github.com/boostorg/build/issues/459#issuecomment-531820138  

It does perfectly make sense to use toolset=msvc even with the Cygwin build, to perform kind of "cross" compilation to native Windows from within Cygwin.

---

## Comment 2

> Username: Lastique  
> Created at: 2019-09-16 15:16:15 UTC  
> Url: https://github.com/boostorg/build/issues/459#issuecomment-531823844  

I don't understand. What is the point in calling vswhere if we're compiling under Cygwin? There is no MSVC in Cygwin system, cross-compilation is done with MinGW gcc.  
  
If you want a native build with MSVC you don't run the build process from Cygwin - you run it from cmd/powershell.

---

## Comment 3

> Username: haubi  
> Created at: 2019-09-16 16:06:38 UTC  
> Url: https://github.com/boostorg/build/issues/459#issuecomment-531845299  

I do have need to use the MSVC toolchain instead of the MinGW toolchain to create native Windows binaries to potentially get support from MS, but still want to use a POSIX-ish build environment. The build system around is a portable (read: Unix, Linux, Windows) set of package definitions, where the final application does need to run on native Windows, even if the package manager itself is unable to run in native Windows but Cygwin only.
