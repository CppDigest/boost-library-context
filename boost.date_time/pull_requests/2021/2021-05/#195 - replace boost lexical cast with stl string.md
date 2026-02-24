# #195 replace boost lexical cast with stl string [Open]

> Username: wellsmt  
> Created at: 2021-05-05 17:31:01 UTC  
> Updated at: 2022-02-12 02:15:07 UTC  
> Url: https://github.com/boostorg/date_time/pull/195  

The purpose of this request to replace the dependency on boost::lexical cast with std string conversions for ```c++ >= c++11``` and leave ```boost::lexical_cast``` for ```< c++11```.  
  
Signed-off-by: Michael T. Wells <michaelthomaswells@gmail.com>

---

## Comment 1

> Username: wellsmt  
> Created_at: 2021-05-05 17:57:50 UTC  
> Updated_at: 2021-05-05 19:20:54 UTC  
> Url: https://github.com/boostorg/date_time/pull/195#issuecomment-832892988  

per @JeffGarland recommendation, std::stoul() is a c++11 feature and will need to use ```BOOST``` macros to check compiler version.  @JeffGarland what did you also mention about ```constexpr``` or did you mean my references to c++11 ```nullptr``` need to be guarded with macros as well?

---

## Review 2 [Commented]

> Username: JeffGarland  
> Created_at: 2021-05-05 23:13:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/195#pullrequestreview-652840640  

📁 include/boost/date_time/date_parsing.hpp

```diff
  16 | #include <algorithm>
  17 | #include <boost/tokenizer.hpp>
  18 |- #include <boost/lexical_cast.hpp>
```

> Username: JeffGarland  
> Created_at: 2021-05-05 23:07:14 UTC  
> Updated_at: 2021-05-07 21:41:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/195#discussion_r626974635  

So here we should ifdef the include so that if we're supporting pre-11 c++ we will just keep using lexical cast probably the cleanest solution is to move it to the compiler_config.hpp to do the macro check.  I looked and can't specific feature macro for these functions so I think I'd go with  
  
BOOST_NO_CXX11  
  
boost.config is already included so you should have direct access to this.

> Username: wellsmt  
> Created_at: 2021-05-06 12:59:21 UTC  
> Updated_at: 2021-05-07 21:41:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/195#discussion_r627395757  

Excellent!  
...clarifying question...  
Is the goal to totally eliminate the dependency on boost/lexical_cast?  If so then we could use ```cstdlib``` for ```< c++11``` and ```string``` for ```>= c++11```.  
Or is the goal simply to modernize the code?  If so we can use the macro approach you mentioned for ```< c++11``` and leave the dependency on ```boost::lexical_cast```.

> Username: JeffGarland  
> Created_at: 2021-05-06 13:15:37 UTC  
> Updated_at: 2021-05-07 21:41:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/195#discussion_r627408130  

I'm fine sticking with lexical_cast for 03 compiles and just modernizing for 11 and beyond.  It seems likely I'll eventually drop 03 support.

---

📁 include/boost/date_time/date_parsing.hpp

```diff
  15 | #include <iterator>
  16 | #include <algorithm>
  17 | #include <boost/tokenizer.hpp>
```

> Username: JeffGarland  
> Created_at: 2021-05-05 23:07:50 UTC  
> Updated_at: 2021-05-07 21:41:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/195#discussion_r626974862  

I forgot about this one as well -- probably this will be next on my radar.

---

📁 include/boost/date_time/date_parsing.hpp

```diff
  61 |     month_str_to_ushort(std::string const& s) {
  62 |       if((s.at(0) >= '0') && (s.at(0) <= '9')) {
  64 |-         return boost::lexical_cast<unsigned short>(s);
```

> Username: JeffGarland  
> Created_at: 2021-05-05 23:12:52 UTC  
> Updated_at: 2021-05-07 21:41:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/195#discussion_r626976716  

so my suggestion is we write the function   
```  
#ifdef BOOST_NO_CXX11  
inline unsigned short string_to_ushort(...)  
{  
  --> current lexical cast  
}  
#else       
inline unsigned short string_to_ushort(...)  
{  
  --> current stoul code  
}  
#endif  
```  
  
This can live here or if it's used elsewhere I'd move it to compiler_config.hpp since its a work around


---

## Comment 3

> Username: JeffGarland  
> Created_at: 2021-05-05 23:15:46 UTC  
> Url: https://github.com/boostorg/date_time/pull/195#issuecomment-833108427  

I was able to kick the ci

---

## Comment 4

> Username: JeffGarland  
> Created_at: 2021-05-06 00:10:18 UTC  
> Url: https://github.com/boostorg/date_time/pull/195#issuecomment-833129065  

And sorta obviously the at least the 03 compiles aren't happy with those functions.

---

## Comment 5

> Username: JeffGarland  
> Created_at: 2021-05-07 00:05:06 UTC  
> Url: https://github.com/boostorg/date_time/pull/195#issuecomment-833958134  

Looking good -- let's see what the CI sez.

---

## Review 6 [Commented]

> Username: wellsmt  
> Created_at: 2021-05-07 21:45:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/195#pullrequestreview-654890685  

📁 include/boost/date_time/date_parsing.hpp

```diff
  38 |+ #else
  39 |+   // TODO: lexical cast has some side effect on <s>  that makes the std::stoul work here
  40 |+   boost::lexical_cast<unsigned short>(s);
```

> Username: wellsmt  
> Created_at: 2021-05-07 21:45:26 UTC  
> Url: https://github.com/boostorg/date_time/pull/195#discussion_r628552109  

@JeffGarland This line should be removed, but i was curious how it would run in CI.  In my local environment, the tests pass when the ```lexical_cast``` is called on ```s```, but fail when it is not called.  There is some side effect of ```boost::lexical_cast``` on ```s``` that I don't understand in the current date_time uninttests.  In particular this test i have been looking at [here](https://github.com/boostorg/date_time/blob/develop/test/gregorian/testgreg_serialize.cpp) fails without doing this cast.


---

## Comment 7

> Username: jeking3  
> Created_at: 2022-02-12 02:15:07 UTC  
> Url: https://github.com/boostorg/date_time/pull/195#issuecomment-1036948189  

@wellsmt rebase on develop for a complete CI scan

---
