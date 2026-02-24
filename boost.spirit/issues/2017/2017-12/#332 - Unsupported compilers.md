# #332 - Unsupported compilers [Closed]

> Username: Kojoley  
> Created at: 2017-12-17 20:20:05 UTC  
> Updated at: 2018-02-18 14:50:55 UTC  
> Closed at: 2017-12-18 15:58:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/332  

Compiler    | Module | Problem  
----------- | ----------- | --------  
MSVC 7.1 | fusion::fold | [log](http://www.boost.org/development/tests/develop/developer/output/igaztanaga-develop-msvc-7-1-boost-bin-v2-libs-fusion-test-vector_misc-test-msvc-7-1-dbg-dbg-symbl-off-thrdp-wn32-thrd-mlt.html)  
clang 3.1 c++11 | spirit::lex | [log](http://www.boost.org/development/tests/develop/developer/output/teeks99-02-dc3-1-11-Docker-64on64-boost-bin-v2-libs-spirit-test-lex-lex_token_omit-test-clang-gnu-linux-3-1~c++11-debug-threadapi-pthread.html)  
MinGW GCC 4.3 | proto | [log](http://www.boost.org/development/tests/develop/developer/output/igaztanaga-develop-gcc-4-3c++03-boost-bin-v2-libs-spirit-test-karma-karma_actions-test-gcc-mngw-gnu-4-3c+-dbg-dbg-symbl-off-thrdp-wn32.html) ICE  
  
It is even hard to obtain an environment with these compilers to try to fix the problems.  
I going to mark them unusable in `explicit-failures-markup.xml`. Any objections?  
  
P.S.: Is there a planned date for removing Spirit.Classic?

---

## Comment 1

> Username: djowel  
> Created at: 2017-12-17 22:19:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/332#issuecomment-352290423  

No objections here.  
  
Re: PS: The problem is that there are Boost libraries (wave and serialization) that relies on classic.

---

## Comment 2

> Username: Kojoley  
> Created at: 2017-12-22 20:15:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/332#issuecomment-353668068  

Also: Graph uses `classic_multi_pass`. Log and Math uses classic in tests.  
And, OMG, [LibreOffice uses classic](https://github.com/LibreOffice/core/search?utf8=%E2%9C%93&q=spirit+classic&type=).

---

## Comment 3

> Username: djowel  
> Created at: 2017-12-23 00:01:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/332#issuecomment-353692785  

LibreOffice! Gee, they should switch to at least Qi.

---

## Comment 4

> Username: Kojoley  
> Created at: 2017-12-23 00:05:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/332#issuecomment-353693202  

> Gee, they should switch to at least Qi.  
  
They have added this note 7 years ago https://github.com/LibreOffice/core/blob/master/filter/source/svg/units.cxx#L92 :laughing:

---

## Comment 5

> Username: djowel  
> Created at: 2017-12-23 00:28:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/332#issuecomment-353695050  

Interesting. That code should be very clean when implemented with Qi or X3.

---

## Comment 6

> Username: djowel  
> Created at: 2017-12-23 00:29:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/332#issuecomment-353695131  

But yeah, there are a LOT of "classic" users:  
  
http://boost-spirit.com/home/info/who-is-using-spirit/

---

## Comment 7

> Username: Kojoley  
> Created at: 2017-12-23 00:35:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/332#issuecomment-353695619  

You can remove Epoch from the list, they replaced Spirit with hand written parser a long time ago AFAIK.

---

## Comment 8

> Username: Kojoley  
> Created at: 2017-12-23 22:16:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/332#issuecomment-353751293  

Update: Added MinGW GCC 4.3 to the list

---

## Comment 9

> Username: Kojoley  
> Created at: 2017-12-25 15:30:26 UTC  
> Updated at: 2018-02-18 14:50:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/332#issuecomment-353876023  

Update: Removed Clang 3.0 C++11 from the list. Should be fixed as of boostorg/fusion#166.
