# #4 Enable x3 tests [Merged]

> Username: redbaron  
> Created at: 2014-03-19 15:51:10 UTC  
> Updated at: 2014-04-01 07:35:08 UTC  
> Merged at: 2014-04-01 02:25:52 UTC  
> Closed at: 2014-04-01 02:25:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/4  



---

## Comment 1

> Username: djowel  
> Created_at: 2014-03-19 23:48:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/4#issuecomment-38121407  

redbarron (what's your name BTW?), my plan is to break the jamfile into separate files. anyway, just curious, what does this mean: <cxxflags>-std=c++11 #<<<< Doesn't work ;( ?

---

## Comment 2

> Username: redbaron  
> Created_at: 2014-03-19 23:55:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/4#issuecomment-38121824  

Maxim Ivanov.  
  
well, from what I've read in Boost.Build docs that line should add <cxxflags> property  specifically for test-suite spirit_x3/qi, but it doesn't happen, so I am invoking tests as "b2 toolset=clang cxxflags=-std=c++11 spirit_x3/qi"

---

## Comment 3

> Username: djowel  
> Created_at: 2014-03-20 00:10:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/4#issuecomment-38122766  

hello Maxim Ivanov. you seem to know X3 quite well!    
odd. all the more reason why i want individual jamfiles for each sub module. i'll work on it.

---

## Comment 4

> Username: redbaron  
> Created_at: 2014-03-20 00:11:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/4#issuecomment-38122861  

I had to hack it quite a bit, to use it in my project. It is about the time to wrap things up, so I started to extract hacks into something meaningfull :)

---

## Comment 5

> Username: djowel  
> Created_at: 2014-03-27 23:05:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/4#issuecomment-38871964  

Maxim, can you have the Bjam such that you do not have to invoke "b2 toolset=clang cxxflags=-std=c++11 spirit_x3/qi" ? The tests should be runnable as is because they are automated by the Boost test facility. I can proceed with the pull requests after this is ironed out.

---

## Comment 6

> Username: redbaron  
> Created_at: 2014-03-28 06:33:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/4#issuecomment-38892252  

For that to work all is needed to override cxxflags for test suite, which I attempted but it didn't work :( Do you have other ideas how it can be done?

---

## Comment 7

> Username: djowel  
> Created_at: 2014-04-01 01:07:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/4#issuecomment-39160828  

I added a jamfile inside the x3 tests. i think that's the way to go --make all jamfile modular. i'll integrate that test later.

---
