# #51 - how to compile and run the tests [Closed]

> Username: ofloveandhate  
> Created at: 2018-03-21 22:07:26 UTC  
> Updated at: 2018-03-29 10:09:31 UTC  
> Closed at: 2018-03-29 10:09:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/51  

i'd love to compile and run the tests for Boost.Multiprecision, but looking around, I don't see how to compile them.  I see `Jamfile.v2` in folder 📁 `test`, but invoking `b2` complains that it can't find `Jamfile.jam`...  my b2 version is `2015.07`.  looking for info indicated [how to do testing on the entirety of boost](http://www.boost.org/development/running_regression_tests.html), but what about just Multiprecision?

---

## Comment 1

> Username: NAThompson  
> Created at: 2018-03-22 01:34:30 UTC  
> Updated at: 2018-03-22 02:53:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/51#issuecomment-375149580  

I do:  
  
```  
git clone --recursive https://github.com/boostorg/boost.git  
cd boost; ./bootstrap; ./b2 --toolset=clang --compiler=clang++  
cd libs/multiprecision/test  
../../../b2 release --toolset=clang --compiler=clang++ -j4  
```  
  
My b2 breaks all the time, so I have to do a fresh clone of the entire superproject.

---

## Comment 2

> Username: ofloveandhate  
> Created at: 2018-03-22 02:31:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/51#issuecomment-375158963  

thank you very much!  i think this is precisely what i wanted!

---

## Comment 3

> Username: ofloveandhate  
> Created at: 2018-03-22 02:33:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/51#issuecomment-375159211  

would you please take out the dollar signs from your shell commands?

---

## Comment 4

> Username: NAThompson  
> Created at: 2018-03-22 02:51:37 UTC  
> Updated at: 2018-03-22 02:52:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/51#issuecomment-375162108  

Sure, but they are more expressions of intent than working code . . .  
  
Also, I updated the command to parallelize the test; it takes quite a while without parallelization.
