# #435 X3: fix unused parameter warnings in position_tagged [Merged]

> Username: Xeverous  
> Created at: 2019-01-08 19:45:29 UTC  
> Updated at: 2019-01-13 18:38:08 UTC  
> Merged at: 2019-01-13 14:53:39 UTC  
> Closed at: 2019-01-13 14:53:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/435  



---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-01-13 14:54:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/435#issuecomment-453836201  

Thanks. However this is not the only unused parameter warning.

---

## Comment 2

> Username: Xeverous  
> Created_at: 2019-01-13 18:00:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/435#issuecomment-453851555  

@Kojoley got any list of them? I fixed the stuff that just happened to trigger on my own projects  
  
Also, do you care about fallthrough warnings? I don't like any warnings popping out not of my code when building but I'm not sure how to approach them. Do you have any convention how to remove "may fallthrough" warnings?

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-01-13 18:08:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/435#issuecomment-453852181  

> @Kojoley got any list of them?  
  
I have a patch in my backlog but did not merged it yet.  
  
> Do you have any convention how to remove "may fallthrough" warnings?  
  
There is `BOOST_FALLTHROUGH` macro for this purpose. In C++17+ code you can use `[[fallthrough]]`.  
  
The problem with fixing warnings is that you need to fix bunch of libraries and there is no point in fixing it in Spirit until they are not fixed in underlying libraries. Some time ago I did a bunch of such fixes, for some libraries it took more than a year to get the fix merged.

---

## Comment 4

> Username: Xeverous  
> Created_at: 2019-01-13 18:27:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/435#issuecomment-453853639  

boost/locale/utf.hpp: In static member function 'static boost::locale::utf::code_point boost::locale::utf::utf_traits<CharType, 1>::decode(Iterator&, Iterator) [with Iterator = const char*; CharType = char]':  
	boost/locale/utf.hpp:224:17: warning: this statement may fall through [-Wimplicit-fallthrough=]  
					 c = (c << 6) | ( tmp & 0x3F);  
					 ^  
	boost/locale/utf.hpp:225:13: note: here  
				 case 2:  
				 ^~~~  
	boost/locale/utf.hpp:231:17: warning: this statement may fall through [-Wimplicit-fallthrough=]  
					 c = (c << 6) | ( tmp & 0x3F);  
					 ^  
	boost/locale/utf.hpp:232:13: note: here  
				 case 1:  
				 ^~~~  
  
Not spirit indeed, would need to contact locale maintainers

---

## Comment 5

> Username: Kojoley  
> Created_at: 2019-01-13 18:38:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/435#issuecomment-453854439  

I even remember that warning, though I might not filed a PR :) Good luck!

---
