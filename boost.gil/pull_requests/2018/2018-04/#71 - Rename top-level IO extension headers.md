# #71 Rename top-level IO extension headers. [Merged]

> Username: stefanseefeld  
> Created at: 2018-04-03 20:53:36 UTC  
> Updated at: 2018-04-04 15:42:38 UTC  
> Merged at: 2018-04-04 15:42:38 UTC  
> Closed at: 2018-04-04 15:42:38 UTC  
> Url: https://github.com/boostorg/gil/pull/71  

Refactor IO extension files.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2018-04-04 03:49:19 UTC  
> Url: https://github.com/boostorg/gil/pull/71#issuecomment-378471815  

Please note that the only intended changes in this PR are:  
  
* file names  
* associated include statements  
* header guards to match the new file names

---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2018-04-04 10:15:26 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/71#pullrequestreview-109264096  

📁 include/boost/gil/extension/io/bmp.hpp

```diff
  10 |- #ifndef BOOST_GIL_EXTENSION_IO_BMP_ALL_HPP
  11 |- #define BOOST_GIL_EXTENSION_IO_BMP_ALL_HPP
  10 |+ #ifndef boost_gil_extension_io_bmp_hpp_
```

> Username: mloskot  
> Created_at: 2018-04-04 10:08:07 UTC  
> Updated_at: 2018-04-04 15:21:14 UTC  
> Url: https://github.com/boostorg/gil/pull/71#discussion_r179088372  

Is this necessary to change the style of include guards to lowercase? (The trailing underscore is not necessary either)  
  
The Boost convention is that all symbols related to preprocessor are underscore-delimited `UPPERCASE` words. That has a purpose and it's a transitive knowledge across Boost codebase.

> Username: stefanseefeld  
> Created_at: 2018-04-04 11:44:56 UTC  
> Updated_at: 2018-04-04 15:21:14 UTC  
> Url: https://github.com/boostorg/gil/pull/71#discussion_r179110554  

I wasn't aware of that convention, and I have seen plenty of exceptions to that rule. Is it written somewhere, or just folklore ? (The important part is that the full filename be represented to avoid clashes. Doing my review I have seen one or two cases where a wrong guard macro was used, apparently copy&pasted from a different header. Such issues generate funny symptoms...)  
So, as I had to adjust all header guards, I just used the convention I was most familiar with. Do you really think I need to change it again ?

> Username: mloskot  
> Created_at: 2018-04-04 13:41:43 UTC  
> Updated_at: 2018-04-04 15:21:14 UTC  
> Url: https://github.com/boostorg/gil/pull/71#discussion_r179143323  

> Is it written somewhere, or just folklore ?   
  
  
Yes, it is here https://www.boost.org/development/header.html : *Boost convention of all uppercase letters, with the header name prefixed by the namespace name, and suffixed with HPP, separated by underscores.*  
  
>  I have seen plenty of exceptions to that rule  
  
grepping through the whole of Boost headers for `ifndef\s+[a-z]+_.+` shows just one in `phoenix/core/detail/index_sequence.hpp` and, interestingly, handful of exceptions in Boost.Python headers, but I suspect it was a recent change :-)  
  
Apart from the widely accepted convention, this `boost_gil_extension_io_bmp_hpp_` is simply confusing with `typedef`-s or class names,  
  
I can re-store upper-case, no problem, just let me know and as soon as you merge, I'll do it.  
  
I understand the role of personal preference, but please let's


📁 include/boost/gil/extension/io/bmp/detail/is_allowed.hpp

```diff
  91 | 
  92 |- #endif // BOOST_GIL_EXTENSION_IO_BMP_IO_IS_ALLOWED_HPP
  92 |+ #endif
```

> Username: mloskot  
> Created_at: 2018-04-04 10:10:43 UTC  
> Updated_at: 2018-04-04 15:21:14 UTC  
> Url: https://github.com/boostorg/gil/pull/71#discussion_r179089093  

A side comment: do we care about EOL at the EOF ?

> Username: stefanseefeld  
> Created_at: 2018-04-04 11:45:44 UTC  
> Updated_at: 2018-04-04 15:21:14 UTC  
> Url: https://github.com/boostorg/gil/pull/71#discussion_r179110767  

I think so. Why do you ask ? (I'm typically editing using emacs, and that editor automatically inserts missing EOL at the end.)

> Username: mloskot  
> Created_at: 2018-04-04 13:44:15 UTC  
> Updated_at: 2018-04-04 15:21:14 UTC  
> Url: https://github.com/boostorg/gil/pull/71#discussion_r179144212  

OK. I just noticed this and (some) other headers are missing the EOL at the end.

> Username: stefanseefeld  
> Created_at: 2018-04-04 15:24:04 UTC  
> Url: https://github.com/boostorg/gil/pull/71#discussion_r179181906  

Hmm, emacs tells me otherwise. I just pushed a commit with the header guard fix. Didn't see any missing EOL endings.

> Username: mloskot  
> Created_at: 2018-04-04 15:26:50 UTC  
> Url: https://github.com/boostorg/gil/pull/71#discussion_r179182999  

I'm sorry, I must be too eager trusting how GitHub renders file.


📁 include/boost/gil/extension/io/raw/read.hpp

```diff
  14 |+ /// \file
  15 |+ /// \brief
  16 |+ /// \author Olivier Tournaire \n
```

> Username: mloskot  
> Created_at: 2018-04-04 10:12:22 UTC  
> Updated_at: 2018-04-04 15:21:14 UTC  
> Url: https://github.com/boostorg/gil/pull/71#discussion_r179089468  

Side comment: Doesn't this `author` clash with the copyright header?

> Username: stefanseefeld  
> Created_at: 2018-04-04 11:46:14 UTC  
> Updated_at: 2018-04-04 15:21:14 UTC  
> Url: https://github.com/boostorg/gil/pull/71#discussion_r179110895  

I haven't looked at any file content.


---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2018-04-04 15:27:20 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/71#pullrequestreview-109378951  

@stefanseefeld thanks for your effort!

---
