# #41 Wrong null character stripping for FormatMessage [Closed]

> Username: Kojoley  
> Created at: 2019-03-23 19:17:41 UTC  
> Updated at: 2019-03-24 03:41:10 UTC  
> Closed at: 2019-03-24 03:41:10 UTC  
> Url: https://github.com/boostorg/system/pull/41  

FormatMessage  
=============  
> If the function succeeds, the return value is the number of TCHARs  
> stored in the output buffer, excluding the terminating null character.  
  
WideCharToMultiByte  
===================  
> cchWideChar  
>  
> If this parameter is -1, the function processes the entire input string,  
> including the terminating null character. Therefore, the resulting  
> character string has a terminating null character, and the length  
> returned by the function includes this character.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2019-03-23 19:26:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/system/pull/41#pullrequestreview-218052493  

📁 test/system_category_test.cpp

```diff
 119 |- 
 120 |-     for( int i = 10000; i < 11032; ++i )
 114 |+     for( int i = -2; i <= 15999; ++i )
```

> Username: pdimov  
> Created_at: 2019-03-23 19:26:25 UTC  
> Updated_at: 2019-03-23 21:00:39 UTC  
> Url: https://github.com/boostorg/system/pull/41#discussion_r268406393  

What's the purpose of this and the preceding change?

> Username: Kojoley  
> Created_at: 2019-03-23 19:30:54 UTC  
> Updated_at: 2019-03-23 21:00:39 UTC  
> Url: https://github.com/boostorg/system/pull/41#discussion_r268406709  

To cover the bug on more locales. Here https://www.boost.org/development/tests/develop/developer/output/igaztanaga-develop-gcc-5-4c++03-boost-bin-v2-libs-system-test-system_category_test-test-gcc-5-4c+-dbg-dbg-symbl-off-thrd-mlt-vsblt-hdn.html the bug is triggered with previous range, with the change it catches on my machine too.


---

## Comment 2

> Username: pdimov  
> Created_at: 2019-03-23 19:36:29 UTC  
> Url: https://github.com/boostorg/system/pull/41#issuecomment-475898303  

Thanks, could you please split the test changes from the .hpp changes into two separate commits, I want to apply the test one first and run the tests while still keeping you as the commit author.

---

## Review 3 [Commented]

> Username: Kojoley  
> Created_at: 2019-03-23 19:37:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/system/pull/41#pullrequestreview-218052953  

📁 test/system_category_test.cpp

```diff
 103 |     char buffer[ 4096 ]; // yes, really
 104 |     BOOST_TEST_CSTR_EQ( cat.message( ev, buffer, sizeof( buffer ) ), sys_strerror( ev ).c_str() );
 105 |+     BOOST_TEST_CSTR_EQ( cat.message( ev ).c_str(), buffer );
```

> Username: Kojoley  
> Created_at: 2019-03-23 19:37:10 UTC  
> Updated_at: 2019-03-23 21:00:39 UTC  
> Url: https://github.com/boostorg/system/pull/41#discussion_r268406921  

I can revert this if you see it is unneeded, but I find this untested.

> Username: pdimov  
> Created_at: 2019-03-23 19:37:54 UTC  
> Updated_at: 2019-03-23 21:00:39 UTC  
> Url: https://github.com/boostorg/system/pull/41#discussion_r268406944  

It doesn't hurt I suppose.

> Username: pdimov  
> Created_at: 2019-03-23 20:13:39 UTC  
> Updated_at: 2019-03-23 21:00:39 UTC  
> Url: https://github.com/boostorg/system/pull/41#discussion_r268407900  

Yeah, it does hurt. :-)

> Username: pdimov  
> Created_at: 2019-03-23 20:17:02 UTC  
> Updated_at: 2019-03-23 21:00:39 UTC  
> Url: https://github.com/boostorg/system/pull/41#discussion_r268408010  

`.message()` doesn't necessarily fill `buffer`, it's allowed to return a pointer to the message elsewhere.

> Username: Kojoley  
> Created_at: 2019-03-23 20:19:16 UTC  
> Updated_at: 2019-03-23 21:00:39 UTC  
> Url: https://github.com/boostorg/system/pull/41#discussion_r268408083  

Funny enough I have 'optimized' it before opening the PR from `BOOST_TEST_EQ( cat.message( ev ), sys_strerror( ev ) );`. How do I proceed?

> Username: pdimov  
> Created_at: 2019-03-23 20:34:44 UTC  
> Updated_at: 2019-03-23 21:00:39 UTC  
> Url: https://github.com/boostorg/system/pull/41#discussion_r268408617  

Well this addition (line 105) needs to go, it causes all Travis tests to fail.


---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-03-23 19:38:06 UTC  
> Url: https://github.com/boostorg/system/pull/41#issuecomment-475898438  

Commits or PRs? If commits, in what order?

---

## Comment 5

> Username: pdimov  
> Created_at: 2019-03-23 19:39:33 UTC  
> Url: https://github.com/boostorg/system/pull/41#issuecomment-475898536  

Commits, test first, fix second.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2019-03-23 19:53:22 UTC  
> Url: https://github.com/boostorg/system/pull/41#issuecomment-475899513  

Please restart the build on the first commit. Travis seems to auto-cancel it because of settings.

---

## Comment 7

> Username: pdimov  
> Created_at: 2019-03-24 03:41:10 UTC  
> Url: https://github.com/boostorg/system/pull/41#issuecomment-475925444  

Applied manually, thanks.

---
