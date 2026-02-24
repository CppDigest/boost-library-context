# #30 Add Boost Software License [Closed]

> Username: tusharb86  
> Created at: 2021-11-18 16:45:19 UTC  
> Updated at: 2021-11-22 20:47:25 UTC  
> Closed at: 2021-11-19 01:50:23 UTC  
> Url: https://github.com/boostorg/multi_array/pull/30  

Add missing license information. The license file was copied from beast: https://github.com/boostorg/beast/blob/develop/LICENSE_1_0.txt.

---

## Comment 1

> Username: glenfe  
> Created_at: 2021-11-19 01:50:23 UTC  
> Url: https://github.com/boostorg/multi_array/pull/30#issuecomment-973653598  

Not necessary. The copyright comments in the library source code mentions the location of the license on the Boost website, and the Boost distribution archive always contains the license file.

---

## Comment 2

> Username: tusharb86  
> Created_at: 2021-11-22 20:42:17 UTC  
> Url: https://github.com/boostorg/multi_array/pull/30#issuecomment-975901717  

Thanks for the feedback @glenfe. I do believe this patch is necessary for a couple of reasons:  
1. The [documentation page for multi_array](https://www.boost.org/doc/libs/1_75_0/doc/html/multi_array.html) does *not* include the Boost Software License. Other documentation pages do include a mention of the BSL with a link, e.g. https://www.boost.org/doc/libs/1_75_0/doc/html/align.html  
2. Consistency - Other boostorg repos do include a license in the root of the repo (examples: https://github.com/boostorg/assign/blob/develop/LICENSE, https://github.com/boostorg/beast/blob/develop/LICENSE_1_0.txt)  
3. The ClearlyDefined tooling harvests license information from repos using the license file in the repo. Without the file, ClearlyDefined does not have good data on the provenance of the license, e.g. https://clearlydefined.io/definitions/git/github/boostorg/multi_array/3b405720559ff9acc968e1a40102ca6245a64e6c  
  
Please let me know if you need any other information or context. I have attempted to contact owners@boost.org about these licensing issues, but have not heard back at all.

---

## Comment 3

> Username: glenfe  
> Created_at: 2021-11-22 20:47:24 UTC  
> Url: https://github.com/boostorg/multi_array/pull/30#issuecomment-975905114  

These are not licensing issues. For the record, I'm one of the users at owners@boost.org.   
  
* The Boost documentation page for a Boost library does not need to contain the license text.  
* Boost library repositories do not need to contain the license text.  
  
Instead:  
  
* The Boost release needs to contain the license text. It always ships with LICENSE_1_0.txt  
* The copyright and license comments in each Boost header and source file need to contain a link to the license text. They do this:  
  
```  
// Use, modification and distribution is subject to the Boost Software   
// License, Version 1.0. (See accompanying file LICENSE_1_0.txt or copy at  
// http://www.boost.org/LICENSE_1_0.txt)  
```

---
