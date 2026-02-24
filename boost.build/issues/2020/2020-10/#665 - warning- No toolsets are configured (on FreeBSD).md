# #665 - warning: No toolsets are configured (on FreeBSD) [Closed]

> Username: yurivict  
> Created at: 2020-10-24 17:13:29 UTC  
> Updated at: 2024-04-20 20:35:36 UTC  
> Closed at: 2024-04-20 20:35:36 UTC  
> Url: https://github.com/boostorg/build/issues/665  

While trying to update the FreeBSD port devel/boost_build, I encountered this problem:  
  
./bootstrap.sh succeeds, but ```./b2 install --prefix=${STAGEDIR}${PREFIX}``` fails:  
```  
warning: No toolsets are configured.  
warning: Configuring default toolset "gcc".  
warning: If the default is wrong, your build may not work correctly.  
warning: Use the "toolset=xxxxx" option to override our guess.  
warning: For more configuration options, please consult  
warning: http://boost.org/boost-build2/doc/html/bbv2/advanced/configuration.html  
/usr/ports/devel/boost_build/work/build-4.3.0/src/tools/gcc.jam:226: in gcc.init from module gcc  
error: toolset gcc initialization:  
error: no command provided, default command 'g++' not found  
```  
  
clang should be default on FreeBSD.  
  
README/bootstrap.sh say nothing about how to choose toolset.  
  
How to fix the 'install' step?

---

## Comment 1

> Username: fasxmut  
> Created at: 2021-05-02 20:40:15 UTC  
> Url: https://github.com/boostorg/build/issues/665#issuecomment-830868558  

You can do it like this:  
b2 toolset=clang  
  
And an alt choice if you are writing your own's project, add this line at the top of your Jamfile:  
using clang ;

---

## Comment 2

> Username: yurivict  
> Created at: 2021-05-02 20:46:02 UTC  
> Url: https://github.com/boostorg/build/issues/665#issuecomment-830869322  

b2 should have the clang toolset set as default on FreeBSD because FreeBSD uses clang as its main compiler.

---

## Comment 3

> Username: fasxmut  
> Created at: 2021-05-02 21:07:27 UTC  
> Url: https://github.com/boostorg/build/issues/665#issuecomment-830872098  

> b2 should have the clang toolset set as default on FreeBSD because FreeBSD uses clang as its main compiler.  
  
I am a freebsd user too, and new to b2. I started learning b2 building system few days ago, just found b2 is very simple and powerful. B2 uses many rewritable module files to store many rules. They are located at /usr/local/share/boost-build/src/ , the toolset setting can be programmed too.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:17 UTC  
> Url: https://github.com/boostorg/build/issues/665#issuecomment-850859568  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
