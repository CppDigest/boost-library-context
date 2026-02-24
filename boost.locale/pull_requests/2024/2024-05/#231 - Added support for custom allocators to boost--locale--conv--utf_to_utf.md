# #231 Added support for custom allocators to boost::locale::conv::utf_to_utf [Merged]

> Username: Beosar  
> Created at: 2024-05-27 17:44:16 UTC  
> Updated at: 2024-07-05 23:13:49 UTC  
> Merged at: 2024-07-05 23:13:49 UTC  
> Closed at: 2024-07-05 23:13:48 UTC  
> Url: https://github.com/boostorg/locale/pull/231  

Added new optional template parameter such that the use of custom allocators is supported by boost::locale::conv::utf_to_utf.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2024-05-27 18:11:11 UTC  
> Url: https://github.com/boostorg/locale/pull/231#issuecomment-2133901072  

Hi, thanks for your contribution. Do you have an actual use case for this? Can you given an example how you intend to use this? I'm asking for 3 reasons:  
  
- (Why) is this required?  
- Is this the best solution in terms of usability and correctness?  
- (optionally) add a test verifying this new feature which also acts as an example how to use it  
  
E.g. I can see that the usage currently would be a bit weird as before you could do `auto s = utf_to_utf<wchar_t>("hello world")` but in the current version you'd loose the auto-deduction of the input char type when using an allocator. And improving this would require a few (possible) use cases.

---

## Comment 2

> Username: Beosar  
> Created_at: 2024-05-27 19:47:50 UTC  
> Updated_at: 2024-05-27 19:48:14 UTC  
> Url: https://github.com/boostorg/locale/pull/231#issuecomment-2133984421  

I use strings with custom allocators in my program. Without this change, this requires an additional allocation and copy whenever I want to convert a string - which I do quite often. Luckily, you can just edit your local header file to make it work, but I'll have to redo this whenever I update boost.  
  
> E.g. I can see that the usage currently would be a bit weird as before you could do `auto s = utf_to_utf<wchar_t>("hello world")` but in the current version you'd loose the auto-deduction of the input char type when using an allocator.  
  
While this is true, it can be left to the user to write their own function to achieve this. Using the C++ std::string interface with a custom allocator is equally as bad, you need to specify the char_traits every time.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2024-05-28 09:57:14 UTC  
> Url: https://github.com/boostorg/locale/pull/231#issuecomment-2134815666  

> I use strings with custom allocators in my program. Without this change, this requires an additional allocation and copy whenever I want to convert a string - which I do quite often  
  
I understand that. Can you give an example how you usually create such strings? Especially as often an instance of an allocator is passed, like:  
  
```  
using String = std::basic_string<char, std::char_traits<char>, StackAllocator>;  
StackAllocator alloc (1024*5);  
String s1{"foo", alloc};  
String s2{alloc};  
s2 += "bar";  
s1 += s2;  
```  
  
With your proposed change this won't be supported, i.e. stateful allocators are not supported. So if that is what you (or others) need, we need to improve on that.  
  
Also the `std::string/basic_string` overload doesn't accept such a string, i.e. `utf_to_utf<wchar>(s1)` won't compile. There are also 2 cases how that could be handled if input and output allocator types are the same:  
1. Use the allocator of the passed-in string  
2. Use a new instance of the template param  
  
If they are different or an allocator is passed it is clear (use a new instance or the passed one)  
  
I hope this is clear enough to show where I'm coming from and why I'm requesting an example of the intended use.

---

## Comment 4

> Username: Beosar  
> Created_at: 2024-05-28 12:45:53 UTC  
> Url: https://github.com/boostorg/locale/pull/231#issuecomment-2135131761  

Yeah, you're right, I forgot about that. Well, I suppose a function that accepts a basic_string and uses the allocator would be the best, instead of what I did. You can then just rebind it to the new character type. I don't know about custom char_traits but, if possible, it probably should be supported, too.  
  
Let me figure out how to add code to this pull request and I'll change it. Never done that before.

---

## Comment 5

> Username: Flamefire  
> Created_at: 2024-05-29 14:23:34 UTC  
> Url: https://github.com/boostorg/locale/pull/231#issuecomment-2137546001  

> Let me figure out how to add code to this pull request and I'll change it. Never done that before.  
  
You can add commits to your source branch and push it. Then they'll show up here.    
BTW: Usually it is better to create a new branch for each PR you do with a name that will let you recognize which PR it is for (in case you want to open multiple ones). To late for this, just a hint for later.

---

## Comment 6

> Username: Beosar  
> Created_at: 2024-05-29 21:13:45 UTC  
> Updated_at: 2024-05-30 01:00:33 UTC  
> Url: https://github.com/boostorg/locale/pull/231#issuecomment-2138274680  

I implemented everything that's necessary as far as I can tell. You can now supply an instance of the desired allocator and the function accepting a string automatically rebinds the allocator from the supplied string.  
  
However, I have also found a bug in Visual Studio in this process. The following code does not compile unless you add the other 2 parameters: boost::locale::conv::utf_to_utf<char32_t, char, std::allocator<char32_t>>("hello world"); [I reported it to Microsoft.](https://developercommunity.visualstudio.com/t/C-Compiler-bug-with-default-parameters/10670633)  
  
Edit: It seems this happens because the compiler evaluates both the second and the last function, then uses the second one and discards the last one because it isn't the best fit. But even though it is not used, it still causes the static assert to fail. But only on C++20, not C++14, apparently.  
  
I guess enable_if could fix that? Seems like I have to try, even though this error is stupid and should not happen.

---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2024-05-30 07:38:52 UTC  
> Updated_at: 2024-05-30 12:43:06 UTC  
> Url: https://github.com/boostorg/locale/pull/231#issuecomment-2138875799  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/231?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 78.45%. Comparing base [(`57c3133`)](https://app.codecov.io/gh/boostorg/locale/commit/57c3133015dce056195d0c9a0da6bdf0221edf8b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`308a18b`)](https://app.codecov.io/gh/boostorg/locale/commit/308a18b2152f915513f13cee0b2f215bfd74aef9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/231/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/231?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff              @@  
##           develop     #231       +/-   ##  
============================================  
- Coverage    95.73%   78.45%   -17.29%       
============================================  
  Files          116       93       -23       
  Lines         9966     7361     -2605       
============================================  
- Hits          9541     5775     -3766       
- Misses         425     1586     +1161       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/locale/pull/231?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/encoding\_utf.hpp](https://app.codecov.io/gh/boostorg/locale/pull/231?src=pr&el=tree&filepath=include%2Fboost%2Flocale%2Fencoding_utf.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZW5jb2RpbmdfdXRmLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
... and [103 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/231/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/231?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/231?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [57c3133...308a18b](https://app.codecov.io/gh/boostorg/locale/pull/231?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
