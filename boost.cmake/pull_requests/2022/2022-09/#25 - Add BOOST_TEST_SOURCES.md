# #25 Add BOOST_TEST_SOURCES [Merged]

> Username: Flamefire  
> Created at: 2022-09-12 09:02:43 UTC  
> Updated at: 2023-01-02 11:55:18 UTC  
> Merged at: 2023-01-02 00:50:59 UTC  
> Closed at: 2023-01-02 00:50:59 UTC  
> Url: https://github.com/boostorg/cmake/pull/25  

This appends additional source files to each test. Useful e.g. in combination with BoostTestJamfile to append e.g. a common "main" cpp file or common headers (for IDEs)

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-09-12 11:23:20 UTC  
> Url: https://github.com/boostorg/cmake/pull/25#issuecomment-1243596618  

The general idea is fine, but this should be consistent with all the other variables; called BOOST_TEST_SOURCES and implemented in the same manner.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2022-09-12 13:14:09 UTC  
> Url: https://github.com/boostorg/cmake/pull/25#issuecomment-1243723656  

I disagree: If you use `boost_test` you have to use the `SOURCES` keyword, that is implicitly required (and currently doesn't make sense to not pass)  
  
The somewhat lengthy name should tell that this is somewhat special: It is added (especially: appended) to the sources passed to `boost_test`  
  
Naming it `BOOST_TEST_SOURCES` suggests that this is the way to specify sources. And implementing it in the same manner would mean prepending it to the `SOURCES` which results in wrong auto-deduced NAMEs  
  
So I would prefer to keep the name and handling as proposed as IMO this fits best the use case intended and the name describes best what it does.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2022-12-31 19:05:50 UTC  
> Url: https://github.com/boostorg/cmake/pull/25#issuecomment-1368266506  

Any comment?

---

## Comment 4

> Username: pdimov  
> Created_at: 2023-01-01 01:28:50 UTC  
> Url: https://github.com/boostorg/cmake/pull/25#issuecomment-1368316445  

I already commented, didn't I?

---

## Comment 5

> Username: Flamefire  
> Created_at: 2023-01-01 11:58:41 UTC  
> Url: https://github.com/boostorg/cmake/pull/25#issuecomment-1368424062  

I was looking for some feedback on the thoughts I had mentioned, especially  
  
> Naming it BOOST_TEST_SOURCES suggests that this is the way to specify sources.  
  
But you still must pass at least one source, hence I found the name better suited.  
  
I implemented an approach where it is named `BOOST_TEST_SOURCES` but the name is still gotten from the `SOURCES` argument. Should be OK I guess.

---
