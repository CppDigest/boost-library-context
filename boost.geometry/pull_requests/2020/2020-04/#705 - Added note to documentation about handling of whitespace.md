# #705 Added note to documentation about handling of whitespace [Closed]

> Username: dkondor  
> Created at: 2020-04-28 14:33:04 UTC  
> Updated at: 2022-12-27 13:10:02 UTC  
> Closed at: 2022-12-27 13:10:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/705  

Explicitly note that only space is allowed. Should clarify issue #703

---

## Comment 1

> Username: awulkiew  
> Created_at: 2020-04-28 16:44:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/705#issuecomment-620724856  

Thanks for this PR @dkondor ! There are problems with it though.  
  
Your comment is probably not going to be visible in the documentation. Have you [built the docs](https://github.com/boostorg/geometry/wiki/Generating-and-Improving-Documentation) and verified that it works as intended?  
  
I think you have 4 options here:  
  
1. Add it in the qbk file which is included below.  
https://github.com/boostorg/geometry/blob/develop/doc/reference/io/read_wkt.qbk  
It is included under *Parameters* section in the reference:  
https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/io/wkt/read_wkt.html  
with the below  
```  
\qbk{[include reference/io/read_wkt.qbk]}  
```  
  
2. Add your comment in the `\qbk{...}` section which currently only includes the additional qbk file  
```  
\qbk{  
Note that the wkt string ...  
  
[include reference/io/read_wkt.qbk]  
}  
```  
  
3. Add `\details` doxygen section and put the information there, like it is the case e.g. in `area`:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/area.hpp#L300  
which is used to build this:  
https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/algorithms/area/area_1.html  
Though in this case I'm not sure what to do with the link (see below). It is possible that enclosing it with `\qbk{...}` could work but I'm not sure. You should test it.  
  
4. Add it in the documentation directory above  
https://github.com/boostorg/geometry/blob/develop/doc/reference/io/wkt_format.qbk  
which is used to build this page with general info:  
https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/io/wkt.html  
  
----  
  
Furthermore we use QuickBook so the markup language is different than GitHub's. Links are defined as follows:  
```  
[@https://www.ogc.org/standards/sfa standard]  
```  
  
see [QuickBook documentation](https://www.boost.org/doc/libs/1_73_0/doc/html/quickbook/syntax/phrase.html#quickbook.syntax.phrase.links).  
  
----  
  
Last but not least, I'm not sure where is the best place for this info. Without giving it much thought I'd say that either the `\details` section (3.) or the directory above with the general info about the standard (4.). What is your opinion @dkondor @mloskot @vissarion @barendgehrels?

---

## Comment 2

> Username: vissarion  
> Created_at: 2020-04-29 08:56:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/705#issuecomment-621075675  

>Last but not least, I'm not sure where is the best place for this info. Without giving it much thought I'd say that either the \details section (3.) or the directory above with the general info about the standard (4.). What is your opinion @dkondor @mloskot @vissarion @barendgehrels?  
  
Adding this to  the general info about the standard (4.) makes more sense to me.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2022-12-27 13:10:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/705#issuecomment-1365890688  

Replaced by #1106

---
