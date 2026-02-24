# #57 Modify docs to get more algorithms in the ToC [Closed]

> Username: tonyelewis  
> Created at: 2018-11-14 13:53:40 UTC  
> Updated at: 2019-12-04 01:08:15 UTC  
> Closed at: 2019-12-04 01:08:14 UTC  
> Url: https://github.com/boostorg/algorithm/pull/57  

Here's a PR for #55.  
  
I found it tricky to manipulate the sections to get the entries I wanted in the ToC, such that their links help navigation. I've managed to get something sensible working here. However I should highlight that these changes have:  
 * put the sections of text _inline_ within one page per parent category (such as "C++11 Algorithms")  
 * nested each of the groups of algorithms within another untitled section (because otherwise, I couldn't get them all to appear on the same page)  
  
And of course those changes may have broken some previous URLs into the docs.  
  
What do you think? I think this is broadly a change in the right direction but I'd welcome any suggestions you have about better ways to achieve the same goals.

---

## Comment 1

> Username: tonyelewis  
> Created_at: 2019-04-11 17:00:50 UTC  
> Url: https://github.com/boostorg/algorithm/pull/57#issuecomment-482201738  

I failed to notice that this PRs build had failed but I think that was actually nothing to do with the PR's changes to the docs. I've just pushed a trivial change to see if that triggers a clean build.

---

## Comment 2

> Username: tonyelewis  
> Created_at: 2019-04-11 17:46:25 UTC  
> Url: https://github.com/boostorg/algorithm/pull/57#issuecomment-482223066  

Now _all_ the builds have failed :grimacing: &hellip;  
  
~~~  
$ ./b2 headers  
boost-install.jam: No such file or directory  
Jamroot:307: in boost-install  
ERROR: rule "boost-install.boost-install" unknown in module "Jamfile</home/travis/build/boostorg/boost-root>".  
libs/chrono/build/Jamfile.v2:113: in modules.load  
/home/travis/build/boostorg/boost-root/tools/build/src/build/project.jam:372: in load-jamfile  
/home/travis/build/boostorg/boost-root/tools/build/src/build/project.jam:64: in load  
/home/travis/build/boostorg/boost-root/tools/build/src/build/project.jam:89: in load-used-projects  
/home/travis/build/boostorg/boost-root/tools/build/src/build/project.jam:75: in load  
/home/travis/build/boostorg/boost-root/tools/build/src/build/project.jam:142: in project.find  
/home/travis/build/boostorg/boost-root/tools/build/src/build-system.jam:618: in load  
/home/travis/build/boostorg/boost-root/tools/build/src/kernel/modules.jam:295: in import  
/home/travis/build/boostorg/boost-root/tools/build/src/kernel/bootstrap.jam:139: in boost-build  
/home/travis/build/boostorg/boost-root/boost-build.jam:17: in module scope  
The command "./b2 headers" failed and exited with 1 during .  
Your build has been stopped.  
~~~  
  
But I don't think this anything to do with the changes in my PR.

---

## Comment 3

> Username: mclow  
> Created_at: 2019-12-03 16:40:03 UTC  
> Url: https://github.com/boostorg/algorithm/pull/57#issuecomment-561251545  

Manually applied on develop as a814721

---

## Comment 4

> Username: tonyelewis  
> Created_at: 2019-12-03 16:49:27 UTC  
> Url: https://github.com/boostorg/algorithm/pull/57#issuecomment-561255627  

Thanks for this. Shall I close this PR then?  
  
(As it happens, I've just been watching your std::midpoint() CppCon talk on the tube this morning. I'm currently up to the {oops - literal 0 is also a null pointer} point. It's a great talk - thanks.)

---

## Comment 5

> Username: mclow  
> Created_at: 2019-12-03 17:01:13 UTC  
> Url: https://github.com/boostorg/algorithm/pull/57#issuecomment-561260935  

I will close it when I merge to master - probably later today.

---

## Comment 6

> Username: mclow  
> Created_at: 2019-12-04 01:08:14 UTC  
> Url: https://github.com/boostorg/algorithm/pull/57#issuecomment-561428681  

merged to master for the 1.72.0 release.

---
