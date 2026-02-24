# #798 - b2 libs/beast/doc : error: doxygen must be initialized before it can be used. [Closed]

> Username: SSE4  
> Created at: 2017-09-27 06:26:42 UTC  
> Updated at: 2018-12-14 06:05:18 UTC  
> Closed at: 2018-05-31 23:53:34 UTC  
> Url: https://github.com/boostorg/beast/issues/798  

on OSX Sierra, I am receiving the following error when running `b2 libs/beast/doc`:  
  
```  
error: at libs/beast/doc/Jamfile:29  
error: doxygen must be initialized before it can be used.  
```  
  
how exactly doxygen shall be initialized?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-27 13:58:12 UTC  
> Url: https://github.com/boostorg/beast/issues/798#issuecomment-332529774  

I have no idea. But you have to have a properly configured toolchain to be able to build the docs.

---

## Comment 2

> Username: SSE4  
> Created at: 2017-09-28 09:24:33 UTC  
> Url: https://github.com/boostorg/beast/issues/798#issuecomment-332780549  

I was able to go further by executing the following command:  
`tools/boostbook/setup_boostbook.sh `  
as docummented [here](http://www.boost.org/doc/libs/1_65_1/doc/html/boostbook/getting/started.html#boostbook.setup.autounix)   
as result it has produced the following **~/user-config.jam** file for me:  
```  
using boostbook  
  : /Users/SSE4/Git/boost/docbook-xsl-1.75.2  
  : /Users/SSE4/Git/boost/docbook-dtd-4.2  
  ;  
using xsltproc : /usr/bin/xsltproc ;  
using doxygen : /usr/local/bin/doxygen ;  
using fop : /Users/SSE4/Git/boost/fop-0.94/fop : : /usr/bin/java ;  
```  
after that, I am receiving the following error:  
```  
Performing configuration checks  
  
    - 32-bit                   : no  (cached)  
    - 64-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
    - symlinks supported       : yes (cached)  
    - C++11 mutex              : no  (cached)  
    - lockfree boost::atomic_flag : yes (cached)  
...patience...  
...patience...  
...found 3651 targets...  
...updating 6 targets...  
Jamfile</Users/SSE4/Git/boost/libs/beast/doc>.make_transform bin.v2/libs/beast/doc/darwin-darwin-4.2.1/debug/threading-multi/transform.xsl  
sed: 1: "/<!-- CONFIG_TEMPLATE - ...": unexpected EOF (pending }'s)  
sed: 1: "/<!-- CLASS_DETAIL_TEMP ...": unexpected EOF (pending }'s)  
sed: 1: "/<!-- INCLUDES_TEMPLATE ...": unexpected EOF (pending }'s)  
sed: 1: "/<!-- INCLUDES_FOOT_TEM ...": unexpected EOF (pending }'s)  
  
    cp libs/beast/doc/docca/include/docca/doxygen.xsl bin.v2/libs/beast/doc/darwin-darwin-4.2.1/debug/threading-multi/transform.xsl  
    sed -i -e "/<!-- CONFIG_TEMPLATE -->/{r libs/beast/doc/xsl/config.xsl" -e "d}" bin.v2/libs/beast/doc/darwin-darwin-4.2.1/debug/threading-multi/transform.xsl  
    sed -i -e "/<!-- CLASS_DETAIL_TEMPLATE -->/{r libs/beast/doc/xsl/class_detail.xsl" -e "d}" bin.v2/libs/beast/doc/darwin-darwin-4.2.1/debug/threading-multi/transform.xsl  
    sed -i -e "/<!-- INCLUDES_TEMPLATE -->/{r libs/beast/doc/xsl/includes.xsl" -e "d}" bin.v2/libs/beast/doc/darwin-darwin-4.2.1/debug/threading-multi/transform.xsl  
    sed -i -e "/<!-- INCLUDES_FOOT_TEMPLATE -->/{r libs/beast/doc/xsl/includes_foot.xsl" -e "d}" bin.v2/libs/beast/doc/darwin-darwin-4.2.1/debug/threading-multi/transform.xsl  
  
...failed Jamfile</Users/SSE4/Git/boost/libs/beast/doc>.make_transform bin.v2/libs/beast/doc/darwin-darwin-4.2.1/debug/threading-multi/transform.xsl...  
...removing bin.v2/libs/beast/doc/darwin-darwin-4.2.1/debug/threading-multi/transform.xsl  
...skipped <pbin.v2/libs/beast/doc/darwin-darwin-4.2.1/debug/threading-multi>reference.qbk for lack of <pbin.v2/libs/beast/doc/darwin-darwin-4.2.1/debug/threading-multi>transform.xsl...  
...skipped <plibs/beast/doc/qbk>reference.qbk for lack of <pbin.v2/libs/beast/doc/darwin-darwin-4.2.1/debug/threading-multi>reference.qbk...  
...skipped <pbin.v2/libs/beast/doc/darwin-darwin-4.2.1/debug/threading-multi>beast_doc.xml for lack of <plibs/beast/doc/qbk-object(qbk-scanner)@5656>reference.qbk...  
...skipped <pbin.v2/libs/beast/doc/darwin-darwin-4.2.1/debug/threading-multi>beast_doc.docbook for lack of <pbin.v2/libs/beast/doc/darwin-darwin-4.2.1/debug/threading-multi-object(xinclude-scanner)@5653>beast_doc.xml...  
...skipped <plibs/beast/doc/html>beast_HTML.manifest for lack of <pbin.v2/libs/beast/doc/darwin-darwin-4.2.1/debug/threading-multi>beast_doc.docbook...  
...failed updating 1 target...  
...skipped 5 targets...  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-09-28 12:44:29 UTC  
> Url: https://github.com/boostorg/beast/issues/798#issuecomment-332824671  

No idea about that

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-11-18 21:11:27 UTC  
> Url: https://github.com/boostorg/beast/issues/798#issuecomment-345471541  

Is this still an issue?

---

## Comment 5

> Username: SSE4  
> Created at: 2017-12-18 06:31:07 UTC  
> Url: https://github.com/boostorg/beast/issues/798#issuecomment-352337473  

yep, still an issue with latest beast

---

## Comment 6

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:37 UTC  
> Url: https://github.com/boostorg/beast/issues/798#issuecomment-384003581  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-04-24 22:25:14 UTC  
> Url: https://github.com/boostorg/beast/issues/798#issuecomment-384099393  

Looks like a problem with `sed`?

---

## Comment 8

> Username: stale[bot]  
> Created at: 2018-05-24 22:56:32 UTC  
> Url: https://github.com/boostorg/beast/issues/798#issuecomment-391890871  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: stale[bot]  
> Created at: 2018-05-31 23:53:32 UTC  
> Url: https://github.com/boostorg/beast/issues/798#issuecomment-393717858  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 10

> Username: evanlenz  
> Created at: 2018-12-14 06:05:18 UTC  
> Url: https://github.com/boostorg/beast/issues/798#issuecomment-447224120  

For the benefit of anyone reading this, I ran into the exact same error message and resolved it by creating ~/user-config.jam with the following line:  
  
    using doxygen : /usr/bin/doxygen ;  
  
The path may vary on your system.
