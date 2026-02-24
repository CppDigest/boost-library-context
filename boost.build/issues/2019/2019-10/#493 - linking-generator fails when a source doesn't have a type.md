# #493 - linking-generator fails when a source doesn't have a type [Closed]

> Username: pdimov  
> Created at: 2019-10-24 12:50:26 UTC  
> Updated at: 2019-10-25 03:21:08 UTC  
> Closed at: 2019-10-25 03:21:08 UTC  
> Url: https://github.com/boostorg/build/issues/493  

At the moment, `b2 -n` in `status` fails with:  
  
```  
/home/travis/build/boostorg/boost/tools/build/src/tools/generators/linking-generator.jam:56: in linking-generator.run  
*** argument error  
* rule type.is-derived ( type base )  
* called with: ( SHARED_LIB )  
* missing argument base  
/home/travis/build/boostorg/boost/tools/build/src/build/type.jam:220:see definition of rule 'type.is-derived' being called  
/home/travis/build/boostorg/boost/tools/build/src/tools/unix.jam:41: in unix-linking-generator.run  
/home/travis/build/boostorg/boost/tools/build/src/tools/gcc.jam:813: in class@gcc-linking-generator.run  
/home/travis/build/boostorg/boost/tools/build/src/build/generators.jam:1019: in try-one-generator-really  
...  
```  
  
Printf debugging reveals that the cause is  
  
```  
linking-generator: source object(file-target)@412852 BoostConfigVersion.cmake has no type  
```  
  
`BoostConfigVersion.cmake` is this:  
  
https://github.com/boostorg/boost_install/blob/a563ec070783e3f7c6876dc3817b7c22b9ce558c/Jamfile#L9-L10  
  
This raises two (no, three) questions.  
  
First, is a source with no type a legitimate error? If so, linking-generator.jam should issue an error, instead of failing. If not, it should check for a missing type and ignore it, as gcc.linking-generator does here:  
  
https://github.com/boostorg/build/blob/63de6eaed4bebcf9e62bcbdb643b1ce44dde23f9/src/tools/gcc.jam#L795-L796  
  
Second, is there something wrong with the definition of `BoostConfigVersion.cmake`?  
  
And third, why is `gcc.linking-generator` being applied to it? :-)

---

## Comment 1

> Username: pdimov  
> Created at: 2019-10-24 23:08:56 UTC  
> Url: https://github.com/boostorg/build/issues/493#issuecomment-546137313  

The errors are fixed by #496, but I'm still not sure why (a) the `BoostConfigVersion.cmake` target doesn't have a type, (b) why are generators applied to it, and (c) why `b2` in `status` manages to load `tools/boost_install/Jamfile`, when I can see no reason for it to do so. (`b2` from the root doesn't have the described problem.)

---

## Comment 2

> Username: pdimov  
> Created at: 2019-10-25 02:36:43 UTC  
> Url: https://github.com/boostorg/build/issues/493#issuecomment-546175844  

I think I figured out (c). It comes from `tools/check_build/test`, which peeks into the `stage` target to extract the libraries. Since now the target has expanded to install dependencies and CMake configuration, the result is f.ex.  
  
```  
info: library-wave:  
info:    BoostConfigVersion.cmake  
info:    libboost_chrono-vc142-mt-gd-x32-1_72.lib  
info:    libboost_chrono-vc142-mt-gd-x32-1_72.lib  
info:    libboost_thread-vc142-mt-gd-x32-1_72.lib  
info:    libboost_thread-vc142-mt-gd-x32-1_72.lib  
info:    libboost_date_time-vc142-mt-gd-x32-1_72.lib  
info:    libboost_date_time-vc142-mt-gd-x32-1_72.lib  
info:    libboost_filesystem-vc142-mt-gd-x32-1_72.lib  
info:    libboost_filesystem-vc142-mt-gd-x32-1_72.lib  
info:    libboost_wave-vc142-mt-gd-x32-1_72.lib  
info:    libboost_wave-vc142-mt-gd-x32-1_72.lib  
```  
  
which is where `BoostConfigVersion.cmake` comes from.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-10-25 03:21:08 UTC  
> Url: https://github.com/boostorg/build/issues/493#issuecomment-546184312  

Should be fixed by https://github.com/boostorg/check_build/commit/4dcc120cba895cc519954cde8a03d5dfc5cb52ab.  
  
In other news, today I learned that `tools/Jamfile` contains `project : requirements <link>static`, which is inherited by everything in `tools`.  
  
I'm closing this issue, although #496 still seems like something we want to apply.
