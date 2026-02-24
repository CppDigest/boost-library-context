# #115 - Error in documentation (*it).prefix().last [Closed]

> Username: Necktschnagge  
> Created at: 2020-11-23 11:59:45 UTC  
> Updated at: 2020-11-23 18:00:24 UTC  
> Closed at: 2020-11-23 17:55:25 UTC  
> Url: https://github.com/boostorg/regex/issues/115  

I'm not yet 100% sure that there is no mistake that I did, but using [regex_iterator as of 1.71.0](https://www.boost.org/doc/libs/1_71_0/libs/regex/doc/html/boost_regex/ref/regex_iterator.html) the documentation says there is a member _last_ ` (*it).prefix().last`.  
This should be the corresponding line:  
https://github.com/boostorg/regex/blob/c3ab6405ad1bfbf372b8b35b6ff62f735e641020/doc/regex_iterator.qbk#L133  
  
But my compiler complains that there is no member `last`.  
Instead I could find some member `end()` which seems to what was described for last.  
This issue also appears for `(*it).suffix().last`  
However, `~.first` works as in the documentation.  
But there is also a `.begin()` doing apparently the same.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-11-23 12:25:29 UTC  
> Url: https://github.com/boostorg/regex/issues/115#issuecomment-732130747  

The type of (*it).prefix() and (*it).suffix() is a `std::pair<>`, as such it has members `.first` and `.second` and no `.begin()` or `.end()`.  Can you post a test case that demonstrates the issue?

---

## Comment 2

> Username: Necktschnagge  
> Created at: 2020-11-23 15:44:35 UTC  
> Updated at: 2020-11-23 15:45:02 UTC  
> Url: https://github.com/boostorg/regex/issues/115#issuecomment-732242420  

As I just tested, you are right when you say, there is a `.first` and a `.second`.  
Then the documentation is just wrong which states existence of `first` and `last`.  
  
This works for me, so there is indeed also some `begin()` and `end()`:  
```cpp  
		using regex_iterator = boost::regex_iterator<std::string::const_iterator>;  
		using pos = std::string::const_iterator;  
  
  
		std::string example = "one two three four five";  
		auto two = boost::regex("two");  
		auto it = regex_iterator(example.cbegin(), example.cend(), two);  
		if (it != regex_iterator()) {  
			const pos two_begin{ it->prefix().end() };  
			const pos two_end{ it->suffix().begin() };  
			const pos two_begin2{ it->prefix().second };  
			const pos two_end2{ it->suffix().first };  
			for (auto i = two_begin; i != two_end; ++i) std::cout << (*i);  
			for (auto i = two_begin2; i != two_end2; ++i) std::cout << (*i);  
		}  
```  
Also, my compiler says that `it->suffix()` is of type `boost::sub_match<...>` and not `std::pair<...>`.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-11-23 17:57:20 UTC  
> Url: https://github.com/boostorg/regex/issues/115#issuecomment-732326502  

>As I just tested, you are right when you say, there is a .first and a .second.  
Then the documentation is just wrong which states existence of first and last.  
  
Oh, somehow my brain automatically changed .last to .second without my noticing :(  
  
So yes you are quite right, and this mistake crops up all over.  Fixed in develop now.  
  
>Also, my compiler says that it->suffix() is of type boost::sub_match<...> and not std::pair<...>.  
  
Yes that's also quite correct.

---

## Comment 4

> Username: Necktschnagge  
> Created at: 2020-11-23 18:00:24 UTC  
> Url: https://github.com/boostorg/regex/issues/115#issuecomment-732328290  

OK, thank you!
