# #617 - Incorrect library name on Windows with layout=versioned [Open]

> Username: msmolensky  
> Created at: 2020-06-09 06:49:51 UTC  
> Updated at: 2021-05-29 17:22:33 UTC  
> Url: https://github.com/boostorg/build/issues/617  

Tried boost v 1.66 and 1.73. B2 generates wrong file name for versioned layout - address-model+architecture part is missing, e.g. boost_unit_test_framework-vc141-mt-gd-1_66.lib instead of boost_unit_test_framework-vc141-mt-gd-x64-1_66.lib  
  
Adding <arch-and-model> to boost.jam tag_versioned rule fixes the problem. (sorry for not attaching the diff)

---

## Comment 1

> Username: mloskot  
> Created at: 2020-06-09 08:35:18 UTC  
> Updated at: 2020-06-09 08:36:15 UTC  
> Url: https://github.com/boostorg/build/issues/617#issuecomment-641125679  

Did you run `b2 --layout=versioned address-model=64`?

---

## Comment 2

> Username: msmolensky  
> Created at: 2020-06-09 12:23:33 UTC  
> Url: https://github.com/boostorg/build/issues/617#issuecomment-641253908  

Yes. Without this tag it is ignored in format-name (in common.jam), no  
matter if you add address-model and/or architecture to command line or  
Jamfile. I ran it in debugger.  
  
   
  
Michael Smolensky  
  
  _____    
  
From: Mateusz Łoskot [mailto:notifications@github.com]   
Sent: Tuesday, June 09, 2020 4:36 AM  
To: boostorg/build  
Cc: msmolensky; Author  
Subject: Re: [boostorg/build] Incorrect library name on Windows with  
layout=versioned (#617)  
  
   
  
Did you run b2 address-model=64?  
  
-  
You are receiving this because you authored the thread.  
Reply to this email directly, view  
<https://github.com/boostorg/build/issues/617#issuecomment-641125679>  it on  
GitHub, or unsubscribe  
<https://github.com/notifications/unsubscribe-auth/AP4STMZFPS7VZ6V54SXCLATRV  
XX5JANCNFSM4NZDHCWA> .  
<https://github.com/notifications/beacon/AP4STM7DCGI2P3GKWI2OZLLRVXX5JA5CNFS  
M4NZDHCWKYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOEY3M2LY  
.gif>

---

## Comment 3

> Username: mloskot  
> Created at: 2020-06-09 13:58:35 UTC  
> Url: https://github.com/boostorg/build/issues/617#issuecomment-641315408  

Sorry, I don't understand what you are doing. Next time, simply paste full command you did run.  
  
Today, I checked building 1.73 with VS 2019:  
  
  
```  
D:\tmp\boost_1_73_0> bootstrap.bat  
D:\tmp\boost_1_73_0> b2 --layout=versioned variant=release,debug toolset=msvc-14.2 --with-test  
...  
D:\tmp\boost_1_73_0>ls -1 D:\tmp\boost_1_73_0\stage\lib  
cmake  
libboost_prg_exec_monitor-vc142-mt-gd-x32-1_73.lib  
libboost_prg_exec_monitor-vc142-mt-gd-x64-1_73.lib  
libboost_prg_exec_monitor-vc142-mt-x32-1_73.lib  
libboost_prg_exec_monitor-vc142-mt-x64-1_73.lib  
libboost_test_exec_monitor-vc142-mt-gd-x32-1_73.lib  
libboost_test_exec_monitor-vc142-mt-gd-x64-1_73.lib  
libboost_test_exec_monitor-vc142-mt-x32-1_73.lib  
libboost_test_exec_monitor-vc142-mt-x64-1_73.lib  
libboost_unit_test_framework-vc142-mt-gd-x32-1_73.lib  
libboost_unit_test_framework-vc142-mt-gd-x64-1_73.lib  
libboost_unit_test_framework-vc142-mt-x32-1_73.lib  
libboost_unit_test_framework-vc142-mt-x64-1_73.lib  
```  
  
You can also be explicit about the address model, requesting both 32 and 64 bit targets:  
  
```  
b2 --layout=versioned address-model=32,64 variant=release,debug toolset=msvc-14.2  
```

---

## Comment 4

> Username: msmolensky  
> Created at: 2020-06-09 14:14:31 UTC  
> Url: https://github.com/boostorg/build/issues/617#issuecomment-641325529  

Sorry for not being clear. I am not building boost - it works fine. I made a  
Jamfile and ran it with b2. The Jamfile referred to a boost library built  
with versioned layout. The build failed because the library name was  
generated incorrectly -x64 was missing. Unfortunately, I did it on a  
different machine that I've submitted bug from so I could not copy command  
line, error or Jamfile. The Jamfile was something like:  
  
   
  
exe Sample  
  
: ...  
  
: ...  
  
 <library>/boost//unit_test_framework  
  
;  
  
   
  
If it is still not clear, I will reproduce it locally and submit code sample  
and error output  
  
   
  
Michael Smolensky  
  
  _____    
  
From: Mateusz Łoskot [mailto:notifications@github.com]   
Sent: Tuesday, June 09, 2020 9:59 AM  
To: boostorg/build  
Cc: msmolensky; Author  
Subject: Re: [boostorg/build] Incorrect library name on Windows with  
layout=versioned (#617)  
  
   
  
Sorry, I don't understand what you are doing. Next time, simply paste full  
command you did run.  
  
Today, I checked building 1.73 with VS 2019:  
  
D:\tmp\boost_1_73_0> bootstrap.bat  
D:\tmp\boost_1_73_0> b2 --layout=versioned variant=release,debug  
toolset=msvc-14.2 --with-test  
...  
D:\tmp\boost_1_73_0>ls -1 D:\tmp\boost_1_73_0\stage\lib  
cmake  
libboost_prg_exec_monitor-vc142-mt-gd-x32-1_73.lib  
libboost_prg_exec_monitor-vc142-mt-gd-x64-1_73.lib  
libboost_prg_exec_monitor-vc142-mt-x32-1_73.lib  
libboost_prg_exec_monitor-vc142-mt-x64-1_73.lib  
libboost_test_exec_monitor-vc142-mt-gd-x32-1_73.lib  
libboost_test_exec_monitor-vc142-mt-gd-x64-1_73.lib  
libboost_test_exec_monitor-vc142-mt-x32-1_73.lib  
libboost_test_exec_monitor-vc142-mt-x64-1_73.lib  
libboost_unit_test_framework-vc142-mt-gd-x32-1_73.lib  
libboost_unit_test_framework-vc142-mt-gd-x64-1_73.lib  
libboost_unit_test_framework-vc142-mt-x32-1_73.lib  
libboost_unit_test_framework-vc142-mt-x64-1_73.lib  
  
You can also be explicit about the address model, requesting both 32 and 64  
bit targets:  
  
b2 --layout=versioned address-model=32,64 variant=release,debug  
toolset=msvc-14.2  
  
-  
You are receiving this because you authored the thread.  
Reply to this email directly, view  
<https://github.com/boostorg/build/issues/617#issuecomment-641315408>  it on  
GitHub, or unsubscribe  
<https://github.com/notifications/unsubscribe-auth/AP4STM6SE56I3N3L2IVXKELRV  
Y5ZXANCNFSM4NZDHCWA> .  
<https://github.com/notifications/beacon/AP4STM7NQZ2IGI6SO4C4LGDRVY5ZXA5CNFS  
M4NZDHCWKYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOEY43EUA  
.gif>

---

## Comment 5

> Username: msmolensky  
> Created at: 2020-06-11 15:19:20 UTC  
> Url: https://github.com/boostorg/build/issues/617#issuecomment-642730989  

Noticed that the original post was auto formatted.  Should be "Adding **\<arc-and-model>** to boost.jam tag_versioned rule fixes the problem"

---

## Comment 6

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:03 UTC  
> Url: https://github.com/boostorg/build/issues/617#issuecomment-850868265  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
