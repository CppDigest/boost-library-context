# #48 - Non-standard license header in utility/detail/minstd_rand.hpp [Closed]

> Username: rdoeffinger  
> Created at: 2018-07-30 17:20:51 UTC  
> Updated at: 2018-09-11 13:45:28 UTC  
> Closed at: 2018-09-11 13:45:28 UTC  
> Url: https://github.com/boostorg/utility/issues/48  

@pdimov Could you please consider copy-pasting one of the standard format license headers from e.g. boost/algorithm/cxx17/reduce.hpp (they also refer to the name of the license file for example).  
This makes it easier to do compliance scanning, which is of particular concern for boost due to the proliferation of boostinspect:nolicense files (and files with no copyright information at all).  
Would be much appreciated!

---

## Comment 1

> Username: pdimov  
> Created at: 2018-07-30 19:57:07 UTC  
> Url: https://github.com/boostorg/utility/issues/48#issuecomment-408989971  

The "standard" license header in `boost/algorithm/cxx17/reduce.hpp` refers to the nonexistent file `http://www.boost.org/LICENSE10.txt` but point taken.

---

## Comment 2

> Username: mclow  
> Created at: 2018-07-30 20:08:46 UTC  
> Url: https://github.com/boostorg/utility/issues/48#issuecomment-408993400  

Fixed the broken links in Boost.Algorithm (8x). Thanks for the heads-up!

---

## Comment 3

> Username: pdimov  
> Created at: 2018-07-30 21:25:12 UTC  
> Url: https://github.com/boostorg/utility/issues/48#issuecomment-409016244  

There we go: https://github.com/boostorg/utility/commit/bdf55e0b6f52f5d4bf40c381fbb8ae7060510c15  
  
Should I cherry-pick this into master for the upcoming release, @mclow?

---

## Comment 4

> Username: mclow  
> Created at: 2018-07-30 21:32:47 UTC  
> Url: https://github.com/boostorg/utility/issues/48#issuecomment-409018838  

I'm being paranoid and will merge my changes in Boost.Algorithm Wednesday morning (after the bots cycle).  But you can go ahead.

---

## Comment 5

> Username: rdoeffinger  
> Created at: 2018-07-31 08:20:24 UTC  
> Url: https://github.com/boostorg/utility/issues/48#issuecomment-409137374  

Thanks all for the quick response! I knew that reduce.hpp referred to a different file, but I just assumed that was going to be the new name and it was an intentional change!  
Great that you were more attentive than me :)
