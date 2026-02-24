# #106 - unit-test rule breaks if "using boost" section of site-config.jam is missing or wrongly specified [Open]

> Username: TimSimpson  
> Created at: 2015-11-15 01:11:05 UTC  
> Updated at: 2015-11-20 04:14:18 UTC  
> Url: https://github.com/boostorg/build/issues/106  

In Boost Build 1.59 (and the current module boost trunk) the "unit-test" rule will break the entire build, showing a stack trace with a bunch of internal Boost Build jam files, if the "using boost" line of the site-config.jam file is missing or incorrectly specified. I've reproduced this on two Windows 10 machines using msvc 12 and 14.  
  
To get more specific, the following Boost Build example works just fine in previous versions of Boost Build: https://github.com/TimSimpson/BoostBuildExamples/blob/master/boost-test/Jamroot.jam  
  
All this time my site-config.jam files looked like this:  
  
``` bjam  
using boost  
    : 1.59   
    ;  
```  
  
(of course previously they were 1.58, 1.57, etc etc).  
  
When I build the boost-test example linked to above I see this:  
  
https://gist.github.com/TimSimpson/027b5c0b1b32793471a1  
  
``` bjam  
...  
C:/Tools/Local/boost/boost_1_59_0/tools/build/src/tools\builtin.jam:769: in class@linking-generator.run  
*** argument error  
* rule type.is-derived ( type base )  
* called with: ( SHARED_LIB )  
* missing argument base  
C:/Tools/Local/boost/boost_1_59_0/tools/build/src/build\type.jam:219:see definition of rule 'type.is-derived' being c  
alled  
C:/Tools/Local/boost/boost_1_59_0/tools/build/src/build\generators.jam:994: in try-one-generator-really  
...etc etc...  
```  
  
I tried debugging it by putting echo statements all over the source, and while I am not a Jam expert it seems like the unit-test rule has its sources expanded to include all of the Boost modules, and at some point boost accumulators is pulled in which has no concrete type and things blow up.  
  
Now if I change the site-config.jam to look like this:  
  
``` bjam  
using boost  
  : 1.59  
  : <include>C:/Tools/Local/boost/boost_1_59_0  
    <library>C:/Tools/Local/boost/boost_1_59_0/stage/lib  
  ;  
```  
  
It works- in that instead I get a linker error: "LINK : fatal error LNK1181: cannot open input file 'boost_test_exec_monitor-vc120-mt-gd-1_59.lib'" (though never have I been so overjoyed to see a linker error).   
  
Now here's the funny thing: as part of trouble shooting I set up Boost 1.58 from scratch, using a clean command prompt. It all worked, but only using the old site-config.jam  (the one that didn't specify the include or library arguments) with 1.59 changed to 1.58. When I changed the site-config.jam to specify the include and library arguments, I now get linker errors, for the similar file "boost_test_exec_monitor-vc120-mt-gd-1_58.lib".  
  
It seems like in the past the include and library arguments did not need to be specified but now they do. That isn't a huge deal, but I'd be embarrassed to admit how long it took me to figure out what needed to change. It seems like Boost Build should be validating the "using boost" statement and giving a nice, solid error message when users aren't specifying everything.  
  
Additionally, if I completely omit the "using boost" I get another stack trace / non-error message situation:  
  
https://gist.github.com/TimSimpson/e0d168f02bee2ea2c065  
  
``` bjam  
...  
C:/Tools/Local/boost/boost_1_59_0/tools/build/src/build\generators.jam:1102: in ensure-type from module generators  
error: target { link%link.do-link-recursively-chrono/stopwatches-headers. { libs/chrono/stopwatches/include/boost. }  
} has no type  
C:/Tools/Local/boost/boost_1_59_0/tools/build/src/build\generators.jam:1359: in generators.construct from module gene  
rators  
...etc etc  
```  
  
In Boost Build 1.58 the boost-test example builds perfectly, running the tests along the way.  
  
So again- it looks like the unit-test rule isn't validating this at all, but I also wonder why it suddenly needs "using boost" to be there in the first place.  
  
Finally, about the file "boost_test_exec_monitor-vc120-mt-gd-1_58.lib" / "boost_test_exec_monitor-vc120-mt-gd-1_59.lib"- I've been using Boost Build for years now so I had my old 1.56 directory sitting around. I do not see any files named "boost_test_exec_monitor-etc" but I do see "boost_prg_exec_monitor-etc". I'm guessing the test monitor is static only which is why that file doesn't exist, and Boost Build normally just figures this out and uses the correct thing. However what's troubling me now is I still am left with a non-working Boost Build 1.59 that won't run my tests.

---

## Comment 1

> Username: vprus  
> Created at: 2015-11-19 18:13:14 UTC  
> Url: https://github.com/boostorg/build/issues/106#issuecomment-158141923  

Tim,  
  
it an unexpected failure mode. I'll take a look at in in some near future, feel free to ping.

---

## Comment 2

> Username: TimSimpson  
> Created at: 2015-11-20 04:14:18 UTC  
> Url: https://github.com/boostorg/build/issues/106#issuecomment-158274458  

Thanks @vprus ! Let me know if I can provide any info or can do anything else to help out somehow.
