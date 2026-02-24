# #219 - The floating_point_examples test requirement [Closed]

> Username: eldiener  
> Created at: 2020-04-15 03:16:27 UTC  
> Updated at: 2020-04-21 10:00:26 UTC  
> Closed at: 2020-04-21 10:00:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/219  

For the floating_point_examples.cpp test you have the requirement:  
  
`<toolset>gcc-mingw:<cxxflags>-Wa,-mbig-obj`  
  
but gcc-mingw is not a toolset, although maybe it once was. I tried instead:  
  
`<toolset>gcc/<target-os>windows:<cxxflags>-Wa,-mbig-obj`  
  
but despite compiling with mingw-w64/gcc on Windows the -Wa,-mbig-obj was not being set, so I do not know what should work in that case. Of course if I add to my user-config.jam for gcc on Windows a <cxxflags>-Wa,-mbig-obj then it gets picked up from there, but I do not know how to get it picked up from your test requirement. However even when it does get picked up the mingw-w64/gcc-9.3 compiler still gives errors from the assembly. What can one expect on Windows <g>.

---

## Comment 1

> Username: pabristow  
> Created at: 2020-04-15 08:57:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/219#issuecomment-613911254  

From: Edward Diener <notifications@github.com>  
Sent: 15 April 2020 04:17  
To: boostorg/multiprecision <multiprecision@noreply.github.com>  
Cc: Subscribed <subscribed@noreply.github.com>  
Subject: [boostorg/multiprecision] The floating_point_examples test requirement (#219)  
  
  
For the floating_point_examples.cpp test you have the requirement:  
  
<toolset>gcc-mingw:<cxxflags>-Wa,-mbig-obj  
  
but gcc-mingw is not a toolset,  
  
Well this command <toolset>gcc-mingw:<link>static  
  
in  
  
:\boost\libs\math\example\Jamfile.v2  
  
Seems to think that it is a ‘toolset’  
  
And this command  
  
      [ run floating_point_examples.cpp no_eh_eg_support : : : <toolset>gcc-mingw:<cxxflags>-Wa,-mbig-obj ] # See note above.  
  
And it does the business for this application, so I’m not clear why it doesn’t work for you.  
  
I think that b2 is being clever here in creating a sub-toolset as it does allowing toolsets like gcc and gcc-9.2, gcc-8.1.0…?  
  
Not sure if this helps…  
  
Paul  
  
PS Aside - I have installed GCC 10 from winlibs.com and works OK.  Are you getting GCC 9.3 from some other location, or rolling your own?  
  
although maybe it once was. I tried instead:  
  
<toolset>gcc/<target-os>windows:<cxxflags>-Wa,-mbig-obj  
  
but despite compiling with mingw-w64/gcc on Windows the -Wa,-mbig-obj was not being set, so I do not know what should work in that case. Of course if I add to my user-config.jam for gcc on Windows a -Wa,-mbig-obj then it gets picked up from there, but I do not know how to get it picked up from your test requirement. However even when it does get picked up the mingw-w64/gcc-9.3 compiler still gives errors from the assembly. What can one expect on Windows .  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/multiprecision/issues/219>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/AAIG4AOCFDLNMEBU7R6TKZLRMURJRANCNFSM4MIHKMTA>.

---

## Comment 2

> Username: eldiener  
> Created at: 2020-04-15 11:09:28 UTC  
> Updated at: 2020-04-15 11:10:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/219#issuecomment-613974060  

What do you mean by 'And it does the business for this application, so I’m not clear why it doesn’t work for you.' ?  
  
If I run:  
  
b2 toolset=gcc-9.3 etc.  
  
it does not think that this is 'gcc-mingw', whatever that is supposed to mean, despite the fact that the gcc is on Windows and is a mingw-w64/gcc-9.3 implementation. It does in the gcc.jam file identify my gcc as mingw using its internal logic, and it does configure gcc with my user-config.jam statement of:  
  
using gcc : 9.3 :   
    "my_path/my_command_etc" :  
    <cxxflags>-Wno-unused-local-typedefs   
    <cxxflags>-ftrack-macro-expansion=0   
    <cxxflags>-Wno-unused-variable   
    <cxxflags>-D_GLIBCXX_USE_CXX11_ABI=1   
    <root>"c:/Utilities/mingw-w64/x86_64-9.3.0-posix-seh-rt_v7-rev0/mingw64"  
    ;  
  
correctly.  
  
BTW, since you asked, I am building gcc-9.3 on Windows using MSYS2 from the implementation at https://github.com/niXman/mingw-builds . It has worked perfectly fine for me. I recomnmend it.  
  
Again I have no idea what gcc-mingw is supposed to mean. This should either be a toolset, like 'clang-win' or 'clang-linux' with its own jam file in Boost Build, or it should be some subfeature of the gcc toolset, which does exist as 'gcc.jam'. But there is no Boost Build jamfile called 'gcc-mingw.jam' and I do not see a subfeature of gcc called 'mingw' anywhere. Maybe you are right and Boost Build does some sort of "magic" to associate 'gcc-mingw' with gcc running on Windows using mingw(-w64)/gcc, but I would really like to know what this "magic" is since it clearly is not working for me.

---

## Comment 3

> Username: pabristow  
> Created at: 2020-04-15 11:35:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/219#issuecomment-613984792  

From: Edward Diener <notifications@github.com>  
Sent: 15 April 2020 12:10  
To: boostorg/multiprecision <multiprecision@noreply.github.com>  
Cc: Paul A. Bristow <paul@pbristow.uk>; Comment <comment@noreply.github.com>  
Subject: Re: [boostorg/multiprecision] The floating_point_examples test requirement (#219)  
  
  
What do you mean by 'And it does the business for this application, so I’m not clear why it doesn’t work for you.' ?  
  
If I run:  
  
b2 toolset=gcc-9.3 etc.  
  
it does not think that this is 'gcc-mingw', whatever that is supposed to mean, despite the fact that the gcc is on Windows and is a mingw-w64/gcc-9.3 implementation. It does in the gcc.jam file identify my gcc as mingw using its internal logic, and it does configure gcc with my user-config.jam statement of:  
  
using gcc : 9.3 :  
"my_path/my_command_etc" :  
-Wno-unused-local-typedefs  
-ftrack-macro-expansion=0  
-Wno-unused-variable  
-D_GLIBCXX_USE_CXX11_ABI=1  
"c:/Utilities/mingw-w64/x86_64-9.3.0-posix-seh-rt_v7-rev0/mingw64"  
;  
  
correctly.  
  
Mine is similar, but configured for __float128 using quadmath  
  
using gcc  
  
  : # version  
  
    10.0.1  
  
  : # GCC compiler exe location  
  
    "C:/Program Files/mingw-w64/mingw-w64-x86_64-10.0.1-snapshot20200223-7.0.0-r1/mingw64/bin/g++.exe"  
  
  : # Options  
  
    <define>BOOST_CONFIG_SUPPRESS_OUTDATED_MESSAGE  
  
   <linkflags>-lquadmath # Essential for float128  
  
     # path for -lquadmath may be needed  
  
    <linkflags>-L"\"C:\\Program Files\\mingw-w64\\mingw-w64-x86_64-10.0.1-snapshot20200223-7.0.0-r1\\mingw64\\x86_64-w64-mingw32\lib\"" # Note /lib  
  
FWIW.  
  
BTW, since you asked, I am building gcc-9.3 on Windows using MSYS2 from the implementation at https://github.com/niXman/mingw-builds . It has worked perfectly fine for me. I recomnmend it.  
  
Again I have no idea what gcc-mingw is supposed to mean. This should either be a toolset, like 'clang-win' or 'clang-linux' with its own jam file in Boost Build, or it should be some subfeature of the gcc toolset, which does exist as 'gcc.jam'. But there is no Boost Build jamfile called 'gcc-mingw.jam' and I do not see a subfeature of gcc called mingw anywhere. Maybe you are right and Boost Build does some sort of "magic" to associate 'gcc-mingw' with gcc running on Windows using mingw(-w64)/gcc, but I would really like to know what this "magic" is since it clearly is not working for me.  
  
Ask the magicians on the Boost.Build list?  
  
I’ve found several helpers there on the mysteries of bjam/b2..  
  
Paul

---

## Comment 4

> Username: eldiener  
> Created at: 2020-04-15 12:21:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/219#issuecomment-614004644  

I asked on the Boost.Build list.  
  
Thanks for the heads up on using quadmath.  
  
Are you sure that with your toolset definition for 10.0.1 that '-Wa,-mbig-obj' is being added to the command line when running the 'floating_point_examples.cpp test' ? Try adding '-d2" to your b2 invocation to find out.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-04-15 13:48:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/219#issuecomment-614051761  

It's not working for me either - though it used to.  
  
I think the best way to fix this may be to write a short test to check whther that command line option is supported and use [ check-target-builds ] to add the option when supported.

---

## Comment 6

> Username: pabristow  
> Created at: 2020-04-15 14:36:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/219#issuecomment-614077894  

From: Edward Diener <notifications@github.com>   
Sent: 15 April 2020 13:22  
To: boostorg/multiprecision <multiprecision@noreply.github.com>  
Cc: Paul A. Bristow <paul@pbristow.uk>; Comment <comment@noreply.github.com>  
Subject: Re: [boostorg/multiprecision] The floating_point_examples test requirement (#219)  
I asked on the Boost.Build list.  
Thanks for the heads up on using quadmath.  
  
Are you sure that with your toolset definition for 10.0.1 that '-Wa,-mbig-obj' is being added to the command line when running the 'floating_point_examples.cpp test' ? Try adding '-d2" to your b2 invocation to find out.  
  
It thought that it ‘cured’ the link failure when I used it with previous gcc versions.  
  
But Aaaargh - I now find that it doesn’t (the normal Boost testing system doesn’t do any mingw testing so I hadn’t noticed before)  
  
    "C:/Program Files/mingw-w64/mingw-w64-x86_64-10.0.1-snapshot20200223-7.0.0-r1/mingw64/bin/g++.exe"   -fvisibility-inlines-hidden -m64 -mthreads -O0 -fno-inline -Wall -g -fvisibility=hidden "-Wa,-mbig-obj" -Wno-missing-braces -DBOOST_ALL_NO_LIB=1  -I"..\..\.." -I"..\include" -c -o "..\..\..\bin.v2\libs\multiprecision\example\floating_point_examples.test\gcc-10.0.1\debug\threading-multi\visibility-hidden\floating_point_examples.o" "floating_point_examples.cpp"  
C:\Users\Paul\AppData\Local\Temp\cceuc9I4.s: Assembler messages:   
C:\Users\Paul\AppData\Local\Temp\cceuc9I4.s: Fatal error: can't close ..\..\..\bin.v2\libs\multiprecision\example\floating_point_examples.test\gcc-10.0.1\debug\threading-multi\visibility-hidden\floating_point_examples.o: file too big  
...failed gcc.compile.c++ ..\..\..\bin.v2\libs\multiprecision\example\floating_point_examples.test\gcc-10.0.1\debug\threading-multi\visibility-hidden\floating_point_examples.o...  
If I set these options for all GCC versions  
<toolset>gcc:<cxxflags>-Wa,-mbig-obj  
      [ run floating_point_examples.cpp no_eh_eg_support : : : <toolset>gcc:<cxxflags>-Wa,-mbig-obj ] # See note above.  
  
It appears to pass the options  -Wa,-mbig-obj  correctly  
    "C:/Program Files/mingw-w64/mingw-w64-x86_64-10.0.1-snapshot20200223-7.0.0-r1/mingw64/bin/g++.exe"   -fvisibility-inlines-hidden -m64 -mthreads -O0 -fno-inline -Wall -g -fvisibility=hidden -Wa,-mbig-obj -Wno-missing-braces -DBOOST_ALL_NO_LIB=1  -I"..\..\.." -I"..\include" -c -o "..\..\..\bin.v2\libs\multiprecision\example\floating_point_examples.test\gcc-10.0.1\debug\threading-multi\visibility-hidden\floating_point_examples.o" "floating_point_examples.cpp"  
But fails thus:  
C:\Users\Paul\AppData\Local\Temp\ccNnIeIa.s: Fatal error: can't close ..\..\..\bin.v2\libs\multiprecision\example\floating_point_examples.test\gcc-10.0.1\debug\threading-multi\visibility-hidden\floating_point_examples.o: file too big  
  
So is it a problem with later versions of GCC?  The oldest I have is 8.1.0  and this still failed for me today ☹  
  
Mingw-w64: How to fix “File too big/too many sections”  
https://digitalkarabela.com/mingw-w64-how-to-fix-file-too-big-too-many-sections/  
is fairly recent (comments 4 months ago)  
  
As usual, having b2/bjam written in Outer Mongolian mangling ones commands in unpredictable ways and needing escaped quotes in unpredictable places doesn't help.  
  
I’m baffled.  
  
Paul

---

## Comment 7

> Username: jzmaddock  
> Created at: 2020-04-18 11:13:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/219#issuecomment-615843434  

Should be fixed in https://github.com/boostorg/multiprecision/pull/221
