# #1 Derive dependencies from the dependency graph automatically. [Merged]

> Username: swatanabe  
> Created at: 2018-10-23 16:15:23 UTC  
> Updated at: 2018-10-24 10:55:53 UTC  
> Merged at: 2018-10-24 10:55:53 UTC  
> Closed at: 2018-10-24 10:55:53 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1  

Note that the dependency on /boost/headers is currently hard-coded, as it is the odd man out.  The dependencies injected into the cmake files are now in the variant specific file, as that matches Boost.Build's model more accurately.  
  
This somewhat reduces the recorded dependencies as it only tracks library dependencies.  The original dependency list also includes header dependencies.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2018-10-23 16:37:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost_install/pull/1#pullrequestreview-167522420  

📁 boost-install.jam

```diff
 299 |+         print.text
 300 |+ 
 301 |+         "find_dependency(boost_$(dep) $(version) EXACT)"
```

> Username: pdimov  
> Created_at: 2018-10-23 16:37:08 UTC  
> Updated_at: 2018-10-24 00:51:06 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#discussion_r227473001  

I wasn't sure about this because in general it will `find_dependency` more than once (for multiconfig generators.)


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2018-10-23 16:40:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost_install/pull/1#pullrequestreview-167524119  

📁 boost-install.jam

```diff
 702 |- 
 764 |     # Target install
 765 |+     generate install-dependencies : $(libraries) : <generating-rule>@boost-install%generate-dependencies <name>install ;
```

> Username: pdimov  
> Created_at: 2018-10-23 16:40:58 UTC  
> Updated_at: 2018-10-24 00:51:07 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#discussion_r227474328  

I suppose that if we use the `boost-install%rulename` syntax in the other places we can avoid exporting the generating rules in Jamroot?


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2018-10-23 16:50:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost_install/pull/1#pullrequestreview-167528080  

📁 boost-install.jam

```diff
 493 |             ""
```

> Username: pdimov  
> Created_at: 2018-10-23 16:50:22 UTC  
> Updated_at: 2018-10-24 00:51:07 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#discussion_r227477472  

You need to move the `include(CMakeFindDependencyMacro)` above the variant includes.


---

## Comment 4

> Username: swatanabe  
> Created_at: 2018-10-23 17:20:08 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432339539  

AMDG  
  
On 10/23/2018 10:42 AM, Peter Dimov wrote:  
>   
> I wasn't sure about this because in general it will `find_dependency` more than once (for multiconfig generators.)  
>   
  
Is that wrong or is it just unnecessary work?  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: swatanabe  
> Created_at: 2018-10-23 17:23:37 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432340811  

AMDG  
  
On 10/23/2018 10:45 AM, Peter Dimov wrote:  
> pdimov commented on this pull request.  
>   
>>      # Target install  
> +    generate install-dependencies : $(libraries) : <generating-rule>@boost-install%generate-dependencies <name>install ;  
>   
> I suppose that if we use the `boost-install%rulename` syntax in the other places we can avoid exporting the generating rules in Jamroot?  
>   
  
Correct.  This syntax is how Boost.Build tracks the  
module internally.  If you just have @rulename, it will  
be converted to @$(current-project-module)%rulename.  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-10-23 17:27:45 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432342264  

> Is that wrong or is it just unnecessary work?  
  
I don't know offhand; we'll see when you fix the `include` as now Appveyor fails because of it. :-)

---

## Comment 7

> Username: pdimov  
> Created_at: 2018-10-23 18:08:25 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432357833  

Hmm.  
  
```  
-- Found boost_system  
--   libboost_system-vc140-mt-gd-x32-1_69.lib  
-- Found boost_headers  
--   libboost_system-vc140-mt-x32-1_69.lib  
```  
  
Something's not right here but I'm not sure what it is.  
  
https://ci.appveyor.com/project/pdimov/boost-install/build/job/ehm3sy38h2g37hh6#L133

---

## Comment 8

> Username: pdimov  
> Created_at: 2018-10-23 18:10:19 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432358552  

Here too:  
  
```  
-- Found boost_filesystem  
--   libboost_filesystem-vc140-mt-gd-x32-1_69.lib  
-- Found boost_system  
--   libboost_system-vc140-mt-gd-x32-1_69.lib  
-- Found boost_headers  
--   libboost_system-vc140-mt-x32-1_69.lib  
--   libboost_filesystem-vc140-mt-x32-1_69.lib  
```  
  
and then  
  
```  
LINK : fatal error LNK1181: cannot open input file '\lib\libboost_filesystem-vc140-mt-x32-1_69.lib' [C:\projects\boost-root\tools\boost_install\test\filesystem\__build__\main.vcxproj]  
```  
  
https://ci.appveyor.com/project/pdimov/boost-install/build/job/imq0apy7t56u7ynq

---

## Comment 9

> Username: swatanabe  
> Created_at: 2018-10-23 18:46:01 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432371856  

AMDG  
  
On 10/23/2018 12:12 PM, Peter Dimov wrote:  
> Here too:  
>   
> ```  
> -- Found boost_filesystem  
> --   libboost_filesystem-vc140-mt-gd-x32-1_69.lib  
> -- Found boost_system  
> --   libboost_system-vc140-mt-gd-x32-1_69.lib  
> -- Found boost_headers  
> --   libboost_system-vc140-mt-x32-1_69.lib  
> --   libboost_filesystem-vc140-mt-x32-1_69.lib  
> ```  
>   
  
  
This appears to be a pre-existing problem:  
  
https://ci.appveyor.com/project/pdimov/boost-install/builds/19654835/job/cl0lnhbj3fub97sp#L132  
  
Both the debug and release configurations are matching,  
because neither Boost_USE_DEBUG_LIBS nor Boost_USE_RELEASE_LIBS  
is set.  
  
"Found boost_headers" is just getting inserted in the middle,  
because the order of execution changed.  
  
  
> and then  
>   
> ```  
> LINK : fatal error LNK1181: cannot open input file '\lib\libboost_filesystem-vc140-mt-x32-1_69.lib' [C:\projects\boost-root\tools\boost_install\test\filesystem\__build__\main.vcxproj]  
> ```  
>   
> https://ci.appveyor.com/project/pdimov/boost-install/build/job/imq0apy7t56u7ynq  
>   
  
_IMPORT_PREFIX is clearly getting clobbered by find_dependency.  
  
In Christ,  
Steven Watanabe

---

## Comment 10

> Username: pdimov  
> Created_at: 2018-10-23 18:56:02 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432375547  

Both debug and release matching is not a problem, it's by design. The question is why they aren't at their proper places. Being listed after `-- Found boost_headers` means that their variants are being included by the config for `boost_headers`, which isn't supposed to happen.

---

## Comment 11

> Username: swatanabe  
> Created_at: 2018-10-23 18:59:28 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432376720  

AMDG  
  
Oh, now I see how debug/release is supposed to work.  
The output is a bit strange, but not a problem by itself.  
Does CMake have any way to handle variable scoping that  
will protect _IMPORT_PREFIX?  
  
In Christ,  
Steven Watanabe

---

## Comment 12

> Username: pdimov  
> Created_at: 2018-10-23 19:00:58 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432377242  

> _IMPORT_PREFIX is clearly getting clobbered by find_dependency.  
  
What fun. Let's make it `_BOOST_IMPORT_PREFIX` then.

---

## Comment 13

> Username: swatanabe  
> Created_at: 2018-10-23 19:10:33 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432380493  

AMDG  
  
On 10/23/2018 01:01 PM, Peter Dimov wrote:  
>> _IMPORT_PREFIX is clearly getting clobbered by find_dependency.  
>   
> What fun. Let's make it `_BOOST_IMPORT_PREFIX` then.  
>   
  
  That may or may not help depending on whether  
the problem is find_dependency itself or the fact that  
boost_headers-config.cmake /also/ uses the name  
_IMPORT_PREFIX.  I don't really understand  
CMake scoping rules.  
  
In Christ,  
Steven Watanabe

---

## Comment 14

> Username: pdimov  
> Created_at: 2018-10-23 19:15:37 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432382146  

Ah. Right. `find_dependency` in the foreach includes the other config file which clobbers everything, not just _IMPORT_PREFIX. It's even funnier than I thought, then.  
  
We have to collect the dependencies in a variable in the variant files, instead of using `find_dependency` there, then do the dependency loop outside.  
  
I wonder how it worked before.

---

## Comment 15

> Username: pdimov  
> Created_at: 2018-10-23 19:18:48 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432383178  

Ah, it worked because I use a b2 loop and inline the `find_dependency` calls so there's nothing they can do at this point.

---

## Comment 16

> Username: swatanabe  
> Created_at: 2018-10-23 19:19:16 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432383358  

AMDG  
  
On 10/23/2018 01:15 PM, Peter Dimov wrote:  
> We have to collect the dependencies in a variable in the variant files, instead of using `find_dependency` there, then do the dependency loop outside.  
>   
  
And make sure that we don't clobber the loop variables...  
  
> I wonder how it worked before.  
>   
  
It worked before because the dependencies were handled at the  
very end, outside the loop.  
  
In Christ,  
Steven Watanabe

---

## Comment 17

> Username: pdimov  
> Created_at: 2018-10-23 19:22:01 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432384289  

> And make sure that we don't clobber the loop variables...  
  
Yes, we have to scope everything manually with BOOST_$LIB_, both the dependency list and the loop variable. :-/

---

## Comment 18

> Username: swatanabe  
> Created_at: 2018-10-23 19:36:03 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432388988  

AMDG  
  
On 10/23/2018 01:22 PM, Peter Dimov wrote:  
>> And make sure that we don't clobber the loop variables...  
>   
> Yes, we have to scope everything manually with BOOST_$LIB_, both the dependency list and the loop variable. :-/  
>   
  
Can we get away with wrapping find_dependency  
in a function.  We don't need to export any  
variables, right?  
  
In Christ,  
Steven Watanabe

---

## Comment 19

> Username: pdimov  
> Created_at: 2018-10-23 19:43:52 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432391553  

Good idea, perhaps we could. With CMake, you never know until you try it. (On all possible versions.) It should in principle work according to the docs, but I'm not positive that we won't hit bugs in CMake because of the recursive call.  
  
We could name the function `boost_$lib_find_dependency` though, that would avoid the recursive function redefinition.

---

## Review 20 [Commented]

> Username: pdimov  
> Created_at: 2018-10-23 20:36:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost_install/pull/1#pullrequestreview-167624483  

📁 boost-install.jam

```diff
 478 |-             "include(CMakeFindDependencyMacro)"
 494 |+             "foreach(dep IN LISTS BOOST_$(lname:U)_DEPS")
 495 |+             "  find_dependency(${dep})"
```

> Username: pdimov  
> Created_at: 2018-10-23 20:36:54 UTC  
> Updated_at: 2018-10-24 00:51:07 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#discussion_r227554358  

Should be `boost_${dep}` I think.


---

## Comment 21

> Username: pdimov  
> Created_at: 2018-10-23 23:48:04 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432461333  

This almost worked, which is rather an indication of the inadequacy of my tests, considering that it doesn't seem to pass any dependent libraries to the linker. :-)

---

## Comment 22

> Username: pdimov  
> Created_at: 2018-10-23 23:50:39 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432461881  

(Since I made System header-only, it works without it being linked.)

---

## Comment 23

> Username: swatanabe  
> Created_at: 2018-10-24 00:14:48 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432466490  

AMDG  
  
It looks like I got confused by LINK_INTERFACE_LIBRARIES_${CONFIG}  
vs INTERFACE_LINK_LIBRARIES.  
  
In Christ,  
Steven Watanabe

---

## Review 24 [Commented]

> Username: pdimov  
> Created_at: 2018-10-24 00:24:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost_install/pull/1#pullrequestreview-167689691  

📁 boost-install.jam

```diff
 299 |+         print.text
 300 |+ 
 301 |+             "set_property(TARGET $(target) APPEND PROPERTY INTERFACE_LINK_LIBRARIES_$(variant:U) Boost::$(deps))"
```

> Username: pdimov  
> Created_at: 2018-10-24 00:24:13 UTC  
> Updated_at: 2018-10-24 00:51:07 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#discussion_r227608738  

Yes, I didn't catch this one. There are indeed no separate INTERFACE_LINK_LIBRARIES for DEBUG and RELEASE in CMake. So it might be better to move this into the BOOST_..._DEPS loop, where it will be deduplicated.


---

## Comment 25

> Username: pdimov  
> Created_at: 2018-10-24 09:58:32 UTC  
> Url: https://github.com/boostorg/boost_install/pull/1#issuecomment-432593699  

Nice work, thanks.  
  
It seems that we have a problem at the moment with buildable -> headers -> buildable dependencies, which I knew we had in theory, but it seems that we also have it in practice, in the form of serialization -> spirit -> thread. But that's to be fixed in Serialization's Jamfile I suppose, and you'll pick it up from there.

---
