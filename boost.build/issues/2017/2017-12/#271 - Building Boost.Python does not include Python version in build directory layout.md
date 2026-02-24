# #271 - Building Boost.Python does not include Python version in build directory layout [Closed]

> Username: stefanseefeld  
> Created at: 2017-12-09 04:01:57 UTC  
> Updated at: 2018-01-24 17:45:50 UTC  
> Closed at: 2018-01-24 17:45:50 UTC  
> Url: https://github.com/boostorg/build/issues/271  

`b2` is supposed to support building Boost.Python against multiple Python versions in a single process.  
However, the build tree doesn't encode the Python version in its directory tree. As a result, `boost_python` and `boost_python3` (which the Boost.Python Jamfile allows to build) will be linked from the same object files, resulting in one of the two to be unusable (as it's built against the wrong Python version).  
  
(In addition, it would be great if the full Python version was embedded into the generated library names (`boost_python` as well as `boost_numpy`), though I'm not sure how to change Boost.Python's Jamfile for that to work, and would appreciate some help from the Boost.Build experts.)

---

## Comment 1

> Username: swatanabe  
> Created at: 2017-12-09 16:16:26 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-350483355  

AMDG  
  
On 12/08/2017 09:01 PM, Stefan Seefeld wrote:  
> `b2` is supposed to support building Boost.Python against multiple Python versions in a single process.  
> However, the build tree doesn't encode the Python version in its directory tree. As a result, `boost_python` and `boost_python3` (which the Boost.Python Jamfile allows to build) will be linked from the same object files, resulting in one of the two to be unusable (as it's built against the wrong Python version).  
>   
  
It seems to work for me with the current develop branch.  
  
user-config.jam:  
using python : 2.7 : /usr/bin/python2.7 ;  
using python : 3.3 : /usr/bin/python3.3 ;  
  
```  
$ b2 --with-python  
<snip>  
...patience...  
...patience...  
...found 6800 targets...  
...updating 144 targets...  
gcc.compile.c++  
bin.v2/libs/python/build/gcc-gnu-4.8.3/release/threadapi-pthread/threading-multi/list.o  
<snip>  
gcc.link.dll  
bin.v2/libs/python/build/gcc-gnu-4.8.3/release/threadapi-pthread/threading-multi/libboost_python.so.1.66.0  
common.copy stage/lib/libboost_python.so.1.66.0  
ln-UNIX stage/lib/libboost_python.so  
gcc.compile.c++  
bin.v2/libs/python/build/gcc-gnu-4.8.3/release/python-3.3/threadapi-pthread/threading-multi/list.o  
<snip>  
gcc.link.dll  
bin.v2/libs/python/build/gcc-gnu-4.8.3/release/python-3.3/threadapi-pthread/threading-multi/libboost_python3.so.1.66.0  
common.copy stage/lib/libboost_python3.so.1.66.0  
ln-UNIX stage/lib/libboost_python3.so  
gcc.compile.c++  
bin.v2/libs/python/build/gcc-gnu-4.8.3/release/link-static/threadapi-pthread/threading-multi/list.o  
<snip>  
gcc.archive  
bin.v2/libs/python/build/gcc-gnu-4.8.3/release/link-static/threadapi-pthread/threading-multi/libboost_python.a  
common.copy stage/lib/libboost_python.a  
gcc.compile.c++  
bin.v2/libs/python/build/gcc-gnu-4.8.3/release/link-static/python-3.3/threadapi-pthread/threading-multi/list.o  
<snip>  
gcc.archive  
bin.v2/libs/python/build/gcc-gnu-4.8.3/release/link-static/python-3.3/threadapi-pthread/threading-multi/libboost_python3.a  
common.copy stage/lib/libboost_python3.a  
...updated 144 targets...  
```  
  
> (In addition, it would be great if the full Python version was embedded into the generated library names (`boost_python` as well as `boost_numpy`), though I'm not sure how to change Boost.Python's Jamfile for that to work, and would appreciate some help from the Boost.Build experts.)  
>

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2017-12-09 17:40:30 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-350492853  

Good ! What about the current release candidate ? That's where the error was reported for (see https://github.com/boostorg/python/issues/129).

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2018-01-23 22:49:12 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-359958333  

@swatanabe , ping ?

---

## Comment 4

> Username: swatanabe  
> Created at: 2018-01-24 00:10:22 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-359975873  

AMDG  
  
On 01/23/2018 03:49 PM, Stefan Seefeld wrote:  
> @swatanabe , ping ?  
>   
  
It was fixed by:  
  
https://github.com/boostorg/python/commit/8c170d91939f6b6506d3b71715688afc16e4ccc3  
  
but it doesn't seem to have made it into 1.66 even  
though git claims that 1.66 is  
https://github.com/boostorg/python/commit/7c33ff0c59af12b8490d4f84ed0d8a2581e34150  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2018-01-24 01:55:48 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-359994680  

I do see https://github.com/boostorg/python/commit/8c170d91939f6b6506d3b71715688afc16e4ccc3 in my local repo, yet when building I find the same (wrong) behaviour where both versions of the library are linked from the same object files. So it seems like this changeset isn't enough to correct the problem.

---

## Comment 6

> Username: swatanabe  
> Created at: 2018-01-24 02:24:54 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-359999650  

AMDG  
  
Here's what I see:  
  
$ cd $BOOST_ROOT  
$ cat project-config.jam  
using python : : /usr/bin/python ;  
using python : : /usr/bin/python3 ;  
$ b2 --config=project-config.jam -n --with-python  
...  
gcc.archive .../libboost_python3  
  /usr/bin/ar ... link-static/python-3.6/threading-multi/list.o ...  
  
So it seems to be working.  How is your configuration  
different?  
  
Boost.python @ d6d54ce4 (current develop)  
  
In Christ,  
Steven Watanabe

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2018-01-24 02:53:35 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-360004299  

I started a new repo clone (following instructions from https://github.com/boostorg/boost/wiki/Getting-Started), then created a config file with the same content as yours. Following that, I run `./b2 --config=config.jam -n --with-python`, which yields  
  
...  
gcc.archive bin.v2/libs/python/build/gcc-7/release/link-static/threadapi-pthread/threading-multi/libboost_numpy3.a  
...  
  
i.e. no sign of a python version in the relative path.  
  
(In the toplevel, `git status` reports the "develop" branch, but stepping into `libs/python`, `git status` reports "* (HEAD detached at 7c33ff0c)". No idea why `git checkout develop` in the superproject doesn't checkout the "develop" branch in the sub-repos. Checking out the "develop" branch in the "python" sub-repo explicitly and repeating the above comment results in the same output.

---

## Comment 8

> Username: swatanabe  
> Created at: 2018-01-24 03:07:57 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-360006684  

AMDG  
  
On 01/23/2018 07:53 PM, Stefan Seefeld wrote:  
> I started a new repo clone (following instructions from https://github.com/boostorg/boost/wiki/Getting-Started), then created a config file with the same content as yours. Following that, I run `./b2 --config=config.jam -n --with-python`, which yields  
>   
  
What does --debug-configuration say?  
  
> ...  
> gcc.archive bin.v2/libs/python/build/gcc-7/release/link-static/threadapi-pthread/threading-multi/libboost_numpy3.a  
> ...  
>   
  
  What revision of tools/build are you at?  
dd694f0 I would guess?  Try develop instead.  
(--config was implemented just after the last  
time I merged to master.  The options you  
need in master to get the same effect are  
very convoluted.)  
  
> i.e. no sign of a python version in the relative path.  
>   
> (In the toplevel, `git status` reports the "develop" branch, but stepping into `libs/python`, `git status` reports "* (HEAD detached at 7c33ff0c)".  
  
This revision is master.  
  
> No idea why `git checkout develop` in the superproject doesn't checkout the "develop" branch in the sub-repos.  
  
That's how git works.  You need `git submodule update` to  
sync the submodules.  
  
> Checking out the "develop" branch in the "python" sub-repo explicitly and repeating the above comment results in the same output.  
>   
>   
  
In Christ,  
Steven Watanabe

---

## Comment 9

> Username: stefanseefeld  
> Created at: 2018-01-24 03:26:32 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-360009470  

Ah, switching the 'build' sub-repo to the 'develop' branch has fixed the problem.

---

## Comment 10

> Username: stefanseefeld  
> Created at: 2018-01-24 03:30:28 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-360010067  

So if the default for sub-repos is 'master', does this mean the fix (in the 'build' repo) made it into the 'develop' branch but not into 'master', and thus not into any release ?

---

## Comment 11

> Username: swatanabe  
> Created at: 2018-01-24 03:31:19 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-360010195  

AMDG  
  
On 01/23/2018 08:26 PM, Stefan Seefeld wrote:  
> Ah, switching the 'build' sub-repo to the 'develop' branch has fixed the problem.  
>   
  
  Okay.  If you you remove the --config argument  
it will probably fail again.  If I'm correct about  
that, then you should check your user-config.jam  
to see how it is initializing python.  
  
In Christ,  
Steven Watanabe

---

## Comment 12

> Username: stefanseefeld  
> Created at: 2018-01-24 03:32:39 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-360010397  

No, my original `user-config.jam` file is quite similar to yours, and does indeed work, too, after the switch to 'develop'.

---

## Comment 13

> Username: swatanabe  
> Created at: 2018-01-24 03:44:03 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-360011935  

AMDG  
  
On 01/23/2018 08:32 PM, Stefan Seefeld wrote:  
> No, my original `user-config.jam` file is quite similar to yours, and does indeed work, too, after the switch to 'develop'.  
>   
  
  That seems strange to me, as I'm not aware  
of making any changes that would fix this.  
It worked for me back in December, which  
is definitely prior to current master.  
Investigating...  
  
In Christ,  
Steven Watanabe

---

## Comment 14

> Username: swatanabe  
> Created at: 2018-01-24 04:17:46 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-360016128  

AMDG  
  
On 01/23/2018 08:32 PM, Stefan Seefeld wrote:  
> No, my original `user-config.jam` file is quite similar to yours, and does indeed work, too, after the switch to 'develop'.  
>   
  
  Okay.  I've found the problem.  I don't think it's  
fixed.  It's just masked somehow.  The core problem  
is that python.jam adds <target-os>linux:<python>2.7  
to the toolset requirements, which overrides  
the <python>3.6 set on boost_python3.  
  
In Christ,  
Steven Watanabe

---

## Comment 15

> Username: swatanabe  
> Created at: 2018-01-24 04:27:52 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-360017330  

AMDG  
  
On 01/23/2018 08:32 PM, Stefan Seefeld wrote:  
> No, my original `user-config.jam` file is quite similar to yours, and does indeed work, too, after the switch to 'develop'.  
>   
  
There was a bad merge to master somwhere along the line  
which lost part of  
https://github.com/boostorg/build/commit/5de8fc63d20fc0b94559626123c1b501732380f0  
  
In Christ,  
Steven Watanabe

---

## Comment 16

> Username: swatanabe  
> Created at: 2018-01-24 04:50:15 UTC  
> Url: https://github.com/boostorg/build/issues/271#issuecomment-360020099  

AMDG  
  
On 01/23/2018 08:32 PM, Stefan Seefeld wrote:  
> No, my original `user-config.jam` file is quite similar to yours, and does indeed work, too, after the switch to 'develop'.  
>   
  
The origin is here:  
  
https://github.com/boostorg/build/blob/df6712e674558eb3fd0c6d8dd26b35ed79b60f0b/src/tools/python.jam#L924  
  
The same code was committed independently to master  
and develop, which caused it to be duplicated.  
When it was deleted, only one copy was deleted  
from master.  
  
In Christ,  
Steven Watanabe
