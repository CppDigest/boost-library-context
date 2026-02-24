# #223 Emit line directives for includes with directive on first line [Merged]

> Username: njnobles  
> Created at: 2024-09-20 02:05:01 UTC  
> Updated at: 2024-10-03 19:50:11 UTC  
> Merged at: 2024-10-03 19:49:37 UTC  
> Closed at: 2024-10-03 19:49:37 UTC  
> Url: https://github.com/boostorg/wave/pull/223  

Attempts to address https://github.com/boostorg/wave/issues/222  
  
Include files that begin with an `#if`/`#ifdef`/`#define` directive now properly emit the line directive that was previously missing.   
  
Because the test suite is using the `eat_whitespace` context policy, the issue only shows if the directive in the include file occurs on the very first line of the file (hence why the copyright comments have been moved to the end of the test files). If the `default_preprocessing_hooks` was used, the copyright comment could be at the beginning and still show the issue.  
  
From my debugging, it seems like the line directive was failing to be emitted because it would errantly fall into this portion of the if-block in cpp_iterator: https://github.com/boostorg/wave/blob/develop/include/boost/wave/util/cpp_iterator.hpp#L786  
  
Here's the output for the `t_2_032.cpp` test that I added prior to the change:  
```  
  
t_2_032_001a  
#line 6 "t_2_032_001.hpp"  
t_2_032_001b  
  
t_2_032_001c  
  
t_2_032_002  
  
t_2_032_004  
#line 2 "t_2_032_003.hpp"  
t_2_032_003  
#line 16 "t_2_032.cpp"  
t_2_032_a  
  
t_2_032_b  
```  
And here's the output after this change:  
```  
#line 2 "t_2_032_001.hpp"  
t_2_032_001a  
#line 6 "t_2_032_001.hpp"  
t_2_032_001b  
  
t_2_032_001c  
#line 2 "t_2_032_002.hpp"  
t_2_032_002  
#line 2 "t_2_032_004.hpp"  
t_2_032_004  
#line 2 "t_2_032_003.hpp"  
t_2_032_003  
#line 16 "t_2_032.cpp"  
t_2_032_a  
  
t_2_032_b  
```

---

## Comment 1

> Username: njnobles  
> Created_at: 2024-09-23 22:27:16 UTC  
> Url: https://github.com/boostorg/wave/pull/223#issuecomment-2369636756  

Hi @jefftrull,  
  
Apologies for reaching out directly, but you mind reviewing this PR when you have some available time? It seems like one of the GitHub Actions failed, but it looks like it was a container setup issue and I'm not sure if there's anyway I could try to trigger a re-run.  
  
Thank you!

---

## Comment 2

> Username: jefftrull  
> Created_at: 2024-09-24 21:51:59 UTC  
> Url: https://github.com/boostorg/wave/pull/223#issuecomment-2372453010  

Hi! Sorry I've been traveling for the last week and a half :) I saw this PR and hope to get on it soon. Thanks in advance for your contribution (and for including a test!).  
  
I agree that the one check failure looks like some infrastructure problem and not due to your code changes.

---

## Comment 3

> Username: njnobles  
> Created_at: 2024-09-25 17:06:51 UTC  
> Url: https://github.com/boostorg/wave/pull/223#issuecomment-2374664191  

Hi! Not a problem at all. I hope you enjoyed your travel :) Thanks in advance for taking a look at it!

---

## Comment 4

> Username: jefftrull  
> Created_at: 2024-10-03 17:48:39 UTC  
> Url: https://github.com/boostorg/wave/pull/223#issuecomment-2391987675  

Please review my PR to your PR when you have time :)

---

## Comment 5

> Username: njnobles  
> Created_at: 2024-10-03 18:17:34 UTC  
> Url: https://github.com/boostorg/wave/pull/223#issuecomment-2392038276  

Reviewed and merged :)

---

## Comment 6

> Username: jefftrull  
> Created_at: 2024-10-03 19:50:09 UTC  
> Url: https://github.com/boostorg/wave/pull/223#issuecomment-2392208385  

Thank you for the helpful contribution!

---
