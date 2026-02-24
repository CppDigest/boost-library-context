# #77 - @see in enum element javadoc not rendering [Open]

> Username: vinniefalco  
> Created at: 2021-09-08 17:15:55 UTC  
> Updated at: 2021-09-16 00:49:00 UTC  
> Url: https://github.com/boostorg/docca/issues/77  

![image](https://user-images.githubusercontent.com/1503976/132554643-b615ae33-7f7c-4986-b4db-77ed7054cd26.png)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-09-08 17:16:05 UTC  
> Url: https://github.com/boostorg/docca/issues/77#issuecomment-915422191  

`ipv4_address` should be linked

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-09-09 16:51:44 UTC  
> Url: https://github.com/boostorg/docca/issues/77#issuecomment-916269441  

Actually it looks like no styles or markups render:  
  
![image](https://user-images.githubusercontent.com/1503976/132728853-abf65288-fb98-4b6d-ba37-59be663f3d5e.png)

---

## Comment 3

> Username: evanlenz  
> Created at: 2021-09-13 03:41:08 UTC  
> Url: https://github.com/boostorg/docca/issues/77#issuecomment-917812153  

Here we are running afoul of a couple of Doxygen issues and a Quickbook limitation.  
  
One of the Doxygen issues has a workaround. In enum tables, markup is not rendered without it (use one less space in the indent). See [this comment](https://github.com/boostorg/docca/issues/63#issuecomment-809104764) for details.  
  
The other apparent Doxygen issue (new to me) is that, once the abovementioned workaround is in place, `@see` creates a "See Also" simple section, but it fails to create the `<ref>` element. I can't explain why this is happening, because we have an analogous example that works perfectly fine in the test case for issue #63.  
  
An obvious workaround for *this* is to additionally use `@ref`. (I know this is lame.) But I still don't know why Doxygen is doing it for [example::issue63](https://github.com/evanlenz/docca/commit/9d3644a0b239779e6725dbd66c7cf632af53deb3#diff-29ef6fd89a4b65fcbe08b2f12ca85cbcb33c5f81e8e78859681fc8fdddbe62c6R24) but not urls::host_type.  
  
Finally, the Quickbook limitation (I think): sections apparently do not render well in tables, hence this dumbing down: https://github.com/evanlenz/docca/commit/9d3644a0b239779e6725dbd66c7cf632af53deb3#diff-d3ad2d9e6ee6aa89bed9e77741df133b5c92645d2906cf232ceeff35159d750aR44  
  
When you remove one space from the indent and add `@ref`, you get all the aspects of the intended rendering, albeit dumbed-down (put on the same line):  
  
This worked for me:  
  
```cpp  
    /**  
     * A host is specified by IPv6address.  
  
       @see @ref ipv6_address  
    */  
    ipv6,  
```  
  
Any ideas as to what's different in the C++ between host_type and [issue_63](https://github.com/evanlenz/docca/commit/9d3644a0b239779e6725dbd66c7cf632af53deb3#diff-29ef6fd89a4b65fcbe08b2f12ca85cbcb33c5f81e8e78859681fc8fdddbe62c6R24) that would make Doxygen fail to insert the reference (without an explicit `@ref` directive) in the former case but succeed in the latter case?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-09-13 16:18:10 UTC  
> Url: https://github.com/boostorg/docca/issues/77#issuecomment-918356099  

You seem to be formatting the javadocs differently. I put the brief on the first line and I use 4 spaces for the indent:  
```  
/** A host is specified by IPv6address.  
  
    @see @ref ipv6_address  
*/  
```

---

## Comment 5

> Username: evanlenz  
> Created at: 2021-09-13 16:35:30 UTC  
> Updated at: 2021-09-13 16:35:59 UTC  
> Url: https://github.com/boostorg/docca/issues/77#issuecomment-918370449  

Yeah, I understand. The workaround is to use fewer than four spaces on the indent for enums. I know this isn't ideal. See #63 for more examples with screenshots. Doxygen's HTML output had the same problem, so it's not unique to the XML output.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-09-13 17:18:36 UTC  
> Updated at: 2021-09-13 17:18:54 UTC  
> Url: https://github.com/boostorg/docca/issues/77#issuecomment-918405066  

well, if using fewer spaces solves the problem then I'm on board !!! is this possibly fixed in a newer doxygen? is there an open issue for it in their repo?

---

## Comment 7

> Username: evanlenz  
> Created at: 2021-09-13 22:58:52 UTC  
> Url: https://github.com/boostorg/docca/issues/77#issuecomment-918643957  

I installed Doxygen 1.9.2 to test this. It looks like the issue is still there (interpreting the output as `<verbatim>` when preceded by those four spaces), at least in the XML output. I don't know if they fixed it in the HTML output.  
  
One (minor) drawback of the centralized docca.jam is that it isn't as immediately clear to me how you would run an HTML-output build (using otherwise the same Doxygen settings). @grisumbras Do you know of an easy way to do that? I used to just change the value from "XML" to "HTML" or something like that in the local Doxgyen settings file.  
  
Anyway, this would make it easy to determine if they've fixed this issue in the HTML output.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-09-13 23:27:14 UTC  
> Url: https://github.com/boostorg/docca/issues/77#issuecomment-918659250  

I think you can change the library's Jamfile to do it?

---

## Comment 9

> Username: grisumbras  
> Created at: 2021-09-14 10:07:02 UTC  
> Url: https://github.com/boostorg/docca/issues/77#issuecomment-919007936  

If you replace  
```  
docca.reference reference.qbk  
    :  
        xsl/custom-overrides.xsl  
        [ glob-tree-ex $(here)/../include/boost/json : *.hpp *.ipp : detail impl ]  
  
```  
with  
```  
import doxygen ;  
path-constant here : . ;  
local srcs = [ glob-ex $(here)/../include/boost/json : *.hpp *.ipp : detail impl ] ;  
srcs = $(here)/$(srcs) ;  
doxygen.doxygen reference.html  
    : $(srcs)  
```  
and comment out all further targets that refer to reference.qbk, it should create doxygen HTML output.  
Note: the output directory will be in the build tree, not in the source tree (so, something like `boost/bin.v2/libs/json/doc/html/reference.html`).

---

## Comment 10

> Username: evanlenz  
> Created at: 2021-09-14 22:15:04 UTC  
> Updated at: 2021-09-14 22:18:30 UTC  
> Url: https://github.com/boostorg/docca/issues/77#issuecomment-919550756  

Excellent, thank you @grisumbras for that leg work! I think I might have done something wrong though.  
  
I'm getting:  
  
```  
$ b2  
Jamfile:60: in modules.load  
*** argument error  
* rule doxygen.doxygen ( target : sources + : requirements * : default-build * : usage-requirements * )  
* called with: ( reference.html :  : <doxygen:param>PROJECT_NAME=URL <doxygen:param>PROJECT_BRIEF=URL Library <doxygen:param>ALIASES=esafe="@par Exception Safety" <doxygen:param>FILE_PATTERNS= <doxygen:param>EXAMPLE_PATTERNS= <doxygen:param>DISTRIBUTE_GROUP_DOC=YES <doxy  
gen:param>MACRO_EXPANSION=YES <doxygen:param>EXPAND_ONLY_PREDEF=YES <doxygen:param>PREDEFINED=\  
            BOOST_SYMBOL_VISIBLE \  
            BOOST_URL_DOCS \  
            BOOST_URL_DECL \  
            BOOST_URL_PUBLIC \  
            BOOST_URL_FORCEINLINE \  
            "BOOST_URL_NODISCARD=[[nodiscard]]" \  
            "BOOST_STATIC_ASSERT(x)=" \  
            "BOOST_URL_INLINE_VARIABLE(v, t)=constexpr t v;" <doxygen:param>ABBREVIATE_BRIEF= <doxygen:param>INLINE_INHERITED_MEMB=YES <doxygen:param>JAVADOC_AUTOBRIEF=YES <doxygen:param>AUTOLINK_SUPPORT=NO <doxygen:param>EXTRACT_ALL=YES <doxygen:param>EXTRACT_PRIVATE=YE  
S <doxygen:param>EXTRACT_LOCAL_CLASSES=NO <doxygen:param>SHOW_INCLUDE_FILES=NO <doxygen:param>INLINE_INFO=NO <doxygen:param>SORT_MEMBER_DOCS=NO <doxygen:param>SORT_MEMBERS_CTORS_1ST=YES <doxygen:param>SHOW_USED_FILES=NO <doxygen:param>SHOW_FILES=NO <doxygen:param>SHOW_NA  
MESPACES=NO <doxygen:param>CLASS_DIAGRAMS=NO )  
* missing argument sources  
C:/cygwin64/home/evanl/boost/tools/build/src/tools\doxygen.jam:627:see definition of rule 'doxygen.doxygen' being called  
C:/cygwin64/home/evanl/boost/tools/build/src/build\project.jam:372: in load-jamfile  
C:/cygwin64/home/evanl/boost/tools/build/src/build\project.jam:64: in load  
C:/cygwin64/home/evanl/boost/tools/build/src/build\project.jam:142: in project.find  
C:/cygwin64/home/evanl/boost/tools/build/src\build-system.jam:618: in load  
C:/cygwin64/home/evanl/boost/tools/build/src/kernel\modules.jam:295: in import  
C:/cygwin64/home/evanl/boost/tools/build/src/kernel/bootstrap.jam:139: in boost-build  
C:/cygwin64/home/evanl/boost/boost-build.jam:17: in module scope  
```  
  
Here are the edits I attempted to make to the beginning of the Jamfile:  
  
```diff  
evanl@THINKPAD-T480 ~/boost/libs/url/doc  
$ git diff .  
diff --git a/doc/Jamfile b/doc/Jamfile  
index 63828f6..3a51a4c 100644  
--- a/doc/Jamfile  
+++ b/doc/Jamfile  
@@ -13,10 +13,12 @@ import boostbook ;  
 import os ;  
 import ../../../tools/docca/docca.jam ;  
  
-docca.reference reference.qbk  
-    :  
-        xsl/custom-overrides.xsl  
-        [ glob-tree-ex ../include/boost/url : *.hpp *.ipp : detail impl ]  
+import doxygen ;  
+path-constant here : . ;  
+local srcs = [ glob-ex $(here)/../include/boost/url : *.hpp *.ipp : detail impl ] ;  
+srcs = $(here)/$(srcs) ;  
+doxygen.doxygen reference.html  
+    : $(srcs)  
     :  
         <doxygen:param>PROJECT_NAME=URL  
         <doxygen:param>PROJECT_BRIEF="URL Library"  
```

---

## Comment 11

> Username: grisumbras  
> Created at: 2021-09-15 10:18:07 UTC  
> Url: https://github.com/boostorg/docca/issues/77#issuecomment-919890696  

Oh, it's my mistake. Should be glob-tree-ex. The glob didn't find any sources, because it didn't consider subdirectories.

---

## Comment 12

> Username: evanlenz  
> Created at: 2021-09-16 00:42:31 UTC  
> Updated at: 2021-09-16 00:42:54 UTC  
> Url: https://github.com/boostorg/docca/issues/77#issuecomment-920487966  

Hmm, I got the same result after changing `glob-ex` to `glob-tree-ex`, but then I tried removing the `here` variable and that worked:  
  
```  
local srcs = [ glob-tree-ex ../include/boost/url : *.hpp *.ipp : detail impl ] ;  
srcs = $(srcs) ;  
```  
  
(I don't know if it's related to me being on Cygwin or not.)  
  
Thanks again!

---

## Comment 13

> Username: evanlenz  
> Created at: 2021-09-16 00:49:00 UTC  
> Url: https://github.com/boostorg/docca/issues/77#issuecomment-920490386  

@vinniefalco It looks like the problem persists in the latest version of Doxygen even in the HTML output:  
  
![image](https://user-images.githubusercontent.com/604974/133531319-7aec5d3a-863f-461b-bc3b-dddd7a2334e1.png)  
  
Also, I didn't find an obvious related issue in the Doxygen project. Should we file one?  
  
For now, it looks like we'll have to go with the smaller-indent workaround.  
  
That leaves open the question as to why Doxygen doesn't generate the reference from `@see` in this case, even with the workaround (but does in our [issue_63 test case](https://github.com/evanlenz/docca/commit/9d3644a0b239779e6725dbd66c7cf632af53deb3#diff-29ef6fd89a4b65fcbe08b2f12ca85cbcb33c5f81e8e78859681fc8fdddbe62c6R24)).
