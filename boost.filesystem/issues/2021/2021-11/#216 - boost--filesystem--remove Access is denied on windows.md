# #216 - boost::filesystem::remove Access is denied on windows [Closed]

> Username: mathisloge  
> Created at: 2021-11-17 14:30:06 UTC  
> Updated at: 2022-03-01 11:00:22 UTC  
> Closed at: 2022-03-01 11:00:22 UTC  
> Url: https://github.com/boostorg/filesystem/issues/216  

## General info  
* Version: 1.77  
* Enviroment: Github Actions  
* Windows server 2019 & windows server 2022  
* c++14  
  
## Description  
I'm currently adding github actions testing workflow for mapnik and while doing so, I'm experiencing `boost::filesystem::remove: Access is denied error`. I've replaced `boost::filesystem::remove` with c++20 `std::filesystem::remove` and that worked and don't had an access denied error. I've also added a simple `std::this_thread::sleep_for(1s)` before the remove to let any other processes from the creation release a potential file access. But that didn't work either.  
  
On my local machines (windows 10 & windows 11) there is no such problem and because this is on a CI enviroment (github actions) where I don't have a deep access to debugging is very hard. Ubuntu and macos are working both in local and CI enviroment.   
  
The file itself is created while running the tests and should be removed after the test run. The file does exists (I've downloaded the whole build dir from the CI).   
  
I've created a [github virtual enviroment repo discussion](https://github.com/actions/virtual-environments/discussions/4525) to ask them if it is something with the image. I have added `TAKEOWN \F <dir>` but that didn't helped.   
  
Is there something I can do, to narrow the problem down and fix it?  
  
## useful links:   
* Code in question:   
  * [Test Code](https://github.com/mathisloge/mapnik/blob/7bf448ffe1bebb5929bfdffdeefd8efcb03e5182/test/unit/datasource/shapeindex.cpp#L112)  
  * [filesystem::remove](https://github.com/mathisloge/mapnik/blob/7bf448ffe1bebb5929bfdffdeefd8efcb03e5182/src/fs.cpp#L72)  
  * [GA workflow run](https://github.com/mathisloge/mapnik/runs/4237485122?check_suite_focus=true)  
  * [GA workflow file](https://github.com/mathisloge/mapnik/blob/gh_ci/.github/workflows/build_and_test.yml)  
  * [github virtual enviroment repo discussion](https://github.com/actions/virtual-environments/discussions/4525)

---

## Comment 1

> Username: mathisloge  
> Created at: 2021-11-17 14:42:35 UTC  
> Url: https://github.com/boostorg/filesystem/issues/216#issuecomment-971647013  

This might be a related PR in the microsoft stl lib https://github.com/microsoft/STL/pull/1559

---

## Comment 2

> Username: Lastique  
> Created at: 2021-11-17 14:56:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/216#issuecomment-971659982  

If this is indeed related to read-only attribute, you can show the attribute in the GHA log by executing [`attrib`](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/attrib) command. Please confirm that this is the actual source of the problem. If not, I will need a small self-contained repro.

---

## Comment 3

> Username: mathisloge  
> Created at: 2021-11-17 17:04:11 UTC  
> Url: https://github.com/boostorg/filesystem/issues/216#issuecomment-971777552  

So i've tried to  create a repro that maps the flow in a simple way in https://github.com/mathisloge/boost-repro   
However I can't reproduce the same error as in mapnik.   
  
I've used attrib to get the flag. But that looks like it has all the needed attibutes:   
`A                    D:\a\mapnik\mapnik\build\windows-ci\out\test\data\shp\boundaries.index`  
  
Full:   
```  
      Start 19: invalid shapeindex  
19/87 Test #19: invalid shapeindex .......................................***Failed    0.06 sec  
A                    D:\a\mapnik\mapnik\build\windows-ci\out\test\data\shp\boundaries.index  
Filters: invalid shapeindex  
  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
mapnik-test-unit.exe is a Catch v2.13.7 host application.  
Run with -? for options  
  
-------------------------------------------------------------------------------  
invalid shapeindex  
  Invalid index  
-------------------------------------------------------------------------------  
D:\a\mapnik\mapnik\test\unit\datasource\shapeindex.cpp(102)  
...............................................................................  
  
D:\a\mapnik\mapnik\test\unit\datasource\shapeindex.cpp(102): FAILED:  
  {Unknown expression after the reported line}  
due to unexpected exception with message:  
  boost::filesystem::remove: Access is denied: "test/data/shp/boundaries.index"  
```  
  
  
I'll keep trying to reproduce the error.

---

## Comment 4

> Username: Lastique  
> Created at: 2021-11-17 19:59:37 UTC  
> Url: https://github.com/boostorg/filesystem/issues/216#issuecomment-971925680  

I have implemented support for deleting read-only files in branch `feature/windows_remove_read_only`. Could you test if it resolves your issue?

---

## Comment 5

> Username: mathisloge  
> Created at: 2021-11-17 21:40:57 UTC  
> Updated at: 2021-11-19 09:35:00 UTC  
> Url: https://github.com/boostorg/filesystem/issues/216#issuecomment-972081409  

wow, that was pretty fast. `A D:\a\mapnik\mapnik\build\windows-ci\out\test\data\shp\boundaries.index` had the correct attributes. I'm curious to see if it solves the problem anyway. The CI is running and might take a hour. Will report tomorrow.   
I also added some other debug output in two other runs to narrow it further down.   
  
If the run fails with the file system change, I will somehow try to debug it further before you put more effort into it. Thanks!

---

## Comment 6

> Username: mathisloge  
> Created at: 2021-11-18 10:58:10 UTC  
> Url: https://github.com/boostorg/filesystem/issues/216#issuecomment-972757612  

So I've runned with the feature branch. But unfortunately it doesn't resolve my issue.   
In the current runs, I do a side-by-side comparison by replacing boost::filesystem::remove with the stl equivalent. Both compiled as c++20 so as not to get any stl side effects from the version as there might be in the previous attemps.

---

## Comment 7

> Username: Lastique  
> Created at: 2021-11-18 11:48:33 UTC  
> Updated at: 2021-11-18 11:49:18 UTC  
> Url: https://github.com/boostorg/filesystem/issues/216#issuecomment-972793137  

In that case I need a repro.  
  
My guess is that the file is opened (by the same or a different process) without the `FILE_SHARE_DELETE` flag, and this prevents the file from being deleted via `DeleteFileW`. MSVC standard library uses a different, not fully documented way of deleting files that enables POSIX semantics of file deletion, which I guess is not affected by this (I don't know if this is indeed the case). This feature only appeared in recent Windows 10 versions, and Boost.Filesystem supports older Windows versions as well, so I won't be able to use the same approach universally. Which means that your test will fail on older Windows versions regardless of `filesystem::remove` implementation.  
  
I probably will implement the POSIX removal semantics on Windows at some point, but it will have to be opportunistic, and therefore will not be trivial. In any case, this will not be done in the immediate future.

---

## Comment 8

> Username: mathisloge  
> Created at: 2021-11-18 14:59:54 UTC  
> Updated at: 2021-11-18 15:00:22 UTC  
> Url: https://github.com/boostorg/filesystem/issues/216#issuecomment-972944775  

> My guess is that the file is opened    
  
think so too. The lib uses memory mapped files to access other files. After disabling this the tests are running successful.   
The question is why this happens only on the CI system but not on my local systems (CI is running windows-server 2019 and 2022).   
  
> recent Windows 10 versions  
  
maybe this is not implemented in windows server enviroments, too?   
  
I will try to create a repro for your future use

---

## Comment 9

> Username: Lastique  
> Created at: 2021-11-18 15:29:48 UTC  
> Url: https://github.com/boostorg/filesystem/issues/216#issuecomment-972973266  

> maybe this is not implemented in windows server enviroments, too?  
  
I don't know how Windows server editions correspond to desktop ones. The POSIX file delete semantics is reportedly implemented since Windows 10 1709.  
  
It may well be something else entirely.

---

## Comment 10

> Username: mathisloge  
> Created at: 2021-12-15 21:36:43 UTC  
> Url: https://github.com/boostorg/filesystem/issues/216#issuecomment-995232601  

So yeah this   
> My guess is that the file is opened  
  
is correct. There seems to be differences between windows desktop and windows server enviroments. If I'm running the test case of https://github.com/mathisloge/boost-repro (currently a bit too much code, I make it simpler) on my windows desktop it  is successful and if I'm running it on the CI (windows server 2022) it will fail.
