# #279 Fix more typos [Merged]

> Username: bwignall  
> Created at: 2019-12-16 18:41:13 UTC  
> Updated at: 2019-12-18 12:10:32 UTC  
> Merged at: 2019-12-18 11:59:23 UTC  
> Closed at: 2019-12-18 11:59:23 UTC  
> Url: https://github.com/boostorg/math/pull/279  

Like https://github.com/boostorg/math/pull/275 , tries to semi-automatically find typos (uses an updated, in-progress data set).

---

## Comment 1

> Username: pabristow  
> Created_at: 2019-12-17 10:41:22 UTC  
> Url: https://github.com/boostorg/math/pull/279#issuecomment-566486179  

Shockingly more effective than my repeated proof reading!    
  
I didn't spot any 'pessimizations'.  
  
Thanks.  
  
Dyslexia Rules KO :-(

---

## Comment 2

> Username: bwignall  
> Created_at: 2019-12-17 12:16:14 UTC  
> Url: https://github.com/boostorg/math/pull/279#issuecomment-566517269  

@pabristow , here are two more that I think you missed:  
  
    git grep Britow  
    config/has_mpfr_class.cpp://  Copyright Paul A. Britow 2009  
    config/has_mpreal.cpp://  Copyright Paul A. Britow 2009

---

## Comment 3

> Username: pabristow  
> Created_at: 2019-12-17 12:24:30 UTC  
> Url: https://github.com/boostorg/math/pull/279#issuecomment-566519840  

Drawing my attention to those is 'below-the-belt' adding insult to injury!

---

## Comment 4

> Username: bwignall  
> Created_at: 2019-12-17 13:38:22 UTC  
> Url: https://github.com/boostorg/math/pull/279#issuecomment-566544805  

I think it shows just how easy it is to miss typos, even when they are "obvious". I have had plenty of times where my tool has highlighted the typo and shown a proposed fix, and I have had to look multiple times to see what the difference is.  
  
https://github.com/bwignall/typochecker has the latest data, if you want to rerun (e.g., on multiprecision), @pabristow .

---

## Comment 5

> Username: pabristow  
> Created_at: 2019-12-17 14:21:19 UTC  
> Url: https://github.com/boostorg/math/pull/279#issuecomment-566561280  

Thanks.

---

## Comment 6

> Username: bwignall  
> Created_at: 2019-12-17 14:54:04 UTC  
> Url: https://github.com/boostorg/math/pull/279#issuecomment-566575550  

@pabristow : did you mean to merge this before closing it?

---

## Comment 7

> Username: pabristow  
> Created_at: 2019-12-17 14:57:44 UTC  
> Url: https://github.com/boostorg/math/pull/279#issuecomment-566577275  

I've cloned your new version and run it (on windows 10)  both using a DOS box  
  
I:\boost\libs\multiprecision\doc>git ls-files | python I:/Cpp/typochecker/corrector.py  
  
and a GIT bash window  
  
and get  
  
Paul@hetpD MINGW64 /i/boost/libs/multiprecision/doc (docs_update)  
$ git ls-files | python I:/Cpp/typochecker/corrector.py                                                                 Getting list of typos  
Information from https://en.wikipedia.org/wiki/Wikipedia:Lists_of_common_misspellings/For_machines  
opening I:/Cpp/typochecker\data\wikipedia_common_misspellings.txt  
opening I:/Cpp/typochecker\data\extra_endings.txt  
Suggestions follow for file html/boost_multiprecision/tut/floats/float128.html  
          There are 15 bits of (biased) binary exponent and 113-bits of significand  
  
                                                                       ^^^^^^^^^^^^^^  
Suggestion: significant  
Correction ("!h" for help), default to significant: Suggestions follow for file html/boost_multiprecision/tut/import_export.html  
Suggestions follow for file html/boost_multiprecision/tut/limits/constants.html  
              in the significand.  
  
                    ^^^^^^^^^^^^^^  
Suggestion: significant  
Correction ("!h" for help), default to significant: Suggestions follow for file html/boost_multiprecision/tut/limits/functions.html  
          encoded as zero and leading zeros appear in the significand, thereby losing  
  
                                                         ^^^^^^^^^^^^^^  
Suggestion: significant  
Correction ("!h" for help), default to significant: Suggestions follow for file html4_symbols.qbk  
Suggestions follow for file multiprecision.qbk  
* for integer types, the number of [*non-sign bits] in the significand.  
  
                                                          ^^^^^^^^^^^^^^  
Suggestion: significant  
Correction ("!h" for help), default to significant:  
Paul@hetpD MINGW64 /i/boost/libs/multiprecision/doc (docs_update)  
  
but I'm doing something silly as I do not get an interactive option to keep the current (correct) spelling.  
  
Nor are any changes/'corrections' made to my files.  
  
Since 'significand' is a popular word in the floating-point world, it would be good to have 'significand' (aka 'mantissa' previously) in the dictionary, wherever that is?  
  
Any ideas what am I doing wrong?

---

## Comment 8

> Username: bwignall  
> Created_at: 2019-12-17 15:05:18 UTC  
> Url: https://github.com/boostorg/math/pull/279#issuecomment-566580604  

`!` and `/` (that is, either single character, followed by <Enter>) should leave the text as-is. (These are the third command under the `!h` help.)   
  
Having an extra domain-specific dictionary available seems like a good idea. I have created https://github.com/bwignall/typochecker/issues/11 for this.  
  
If you keep the current spelling, it should not modify the file. As you modifying spelling and still not seeing changes in the files?

---

## Comment 9

> Username: pabristow  
> Created_at: 2019-12-17 15:05:37 UTC  
> Url: https://github.com/boostorg/math/pull/279#issuecomment-566580744  

I've also found another example of a word that needs 'white-listing'.   
  
 'Mathematica' is correct - name of important Math package.  
  
and I can see this being a popular need.  
  
Is there an easy way to do this?  
  
Add an entry to the \typochecker\data\extra_endings.txt file?  
  
Mathematica ->Mathematica  
...

---

## Comment 10

> Username: bwignall  
> Created_at: 2019-12-17 15:07:29 UTC  
> Url: https://github.com/boostorg/math/pull/279#issuecomment-566581595  

Yes, if you want to add something quickly and locally, add `typo->correction` to data/extra_endings.txt.

---

## Comment 11

> Username: pabristow  
> Created_at: 2019-12-17 16:25:02 UTC  
> Url: https://github.com/boostorg/math/pull/279#issuecomment-566638528  

My problem is not getting a prompt when I run the python line. It just runs straight to the end and exits. I am studying "Python for Microsofty Newbies", but perhaps you have some suggestions why?

---

## Comment 12

> Username: pabristow  
> Created_at: 2019-12-17 16:46:47 UTC  
> Url: https://github.com/boostorg/math/pull/279#issuecomment-566647661  

@bwignall   
  
1  I've just come to 'white-list' significand, but I see that  file extra_endings.txt contains  
  
significand->significant  
  
which isn't what I (and any C++ user) want, so I've just deleted this entry completely.  
  
I now get NO suggestions from the /doc/*.qbk  etc files.  
  
and I have the following two for mathy items  
  
mathemetical->mathematical  
Mathemetica->Mathematica  
  
2  There is also the potential issue of upper case items, like Mathematica.  
  
Is the list case sensitive?  and is the processing case sensitive?

---

## Comment 13

> Username: pabristow  
> Created_at: 2019-12-18 09:49:58 UTC  
> Url: https://github.com/boostorg/math/pull/279#issuecomment-566958022  

@bwignall :I thought I should probably try to run all the .Boost.Math tests using these changes locally first (as it involves changes to .*pp files as well as the harmless .qbk docs files).  But on closer inspection, I conclude it is safe to merge.  I hope that squash and merge is the right option.

---

## Comment 14

> Username: bwignall  
> Created_at: 2019-12-18 12:10:31 UTC  
> Url: https://github.com/boostorg/math/pull/279#issuecomment-567006372  

Thanks, @pabristow . Shall we take your outstanding questions and discuss on an issue at the typochecker repo?

---
