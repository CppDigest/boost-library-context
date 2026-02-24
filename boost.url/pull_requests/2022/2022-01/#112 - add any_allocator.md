# #112 add any_allocator [Merged]

> Username: alandefreitas  
> Created at: 2022-01-15 00:11:44 UTC  
> Updated at: 2022-01-19 18:38:05 UTC  
> Merged at: 2022-01-19 18:25:18 UTC  
> Closed at: 2022-01-19 18:25:18 UTC  
> Url: https://github.com/boostorg/url/pull/112  

A type-erased allocator with shared ownership that satisfies the requirements for Allocator.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-15 02:30:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-853499292  

📁 include/boost/url/any_allocator.hpp

```diff
  12 |+ 
  13 |+ #include <boost/url/detail/config.hpp>
  14 |+ #include <boost/url/detail/any_allocator_base.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-01-15 02:30:41 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785257913  

I use the name `any_allocator.hpp` since it is part of the overall feature


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-15 02:31:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-853499328  

📁 include/boost/url/any_allocator.hpp

```diff
  31 |+ #ifdef BOOST_URL_DOCS
  32 |+ template< class T >
  33 |+ using any_allocator = __see_below __;
```

> Username: vinniefalco  
> Created_at: 2022-01-15 02:31:03 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785257934  

did you add this to quickref.xml ?


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-01-16 04:24:33 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1013808506  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-01-16 05:09:27 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1013812530  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-16 05:56:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-853632820  

📁 include/boost/url/any_allocator.hpp

```diff
  31 |+ #ifdef BOOST_URL_DOCS
  32 |+ template< class T >
  33 |+ using any_allocator = __see_below __;
```

> Username: vinniefalco  
> Created_at: 2022-01-16 05:56:10 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785394512  

There's an extra space that shouldn't be here, it should read `__see_below__`. The documentation is rendered incorrectly as a result:   
![image](https://user-images.githubusercontent.com/1503976/149649047-4e5ef5cc-1dd2-404a-a7b0-d322cfbec66a.png)


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-16 05:56:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-853632850  

📁 include/boost/url/any_allocator.hpp

```diff
  61 |+ 
  62 |+     any_allocator&
  63 |+     operator=(any_allocator const&) noexcept = default;
```

> Username: vinniefalco  
> Created_at: 2022-01-16 05:56:43 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785394527  

Does this need to say `noexcept`?


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-16 05:56:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-853632861  

📁 include/boost/url/any_allocator.hpp

```diff
  55 |+     any_allocator() noexcept;
  56 |+ 
  57 |+     any_allocator(any_allocator const&) noexcept = default;
```

> Username: vinniefalco  
> Created_at: 2022-01-16 05:56:50 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785394533  

Does this need to say `noexcept`?

> Username: alandefreitas  
> Created_at: 2022-01-16 06:10:10 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785395381  

The constructor needs it.  
  
![image](https://user-images.githubusercontent.com/5369819/149649390-45cff300-df4d-4a03-ac5a-c8cdeae9a0ee.png)

> Username: vinniefalco  
> Created_at: 2022-01-16 19:01:12 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785482091  

Yes but the default copy constructor for `any_allocator` is implicitly noexcept


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-16 05:57:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-853632882  

📁 include/boost/url/any_allocator.hpp

```diff
  64 |+ 
  65 |+     template<class Allocator>
  66 |+     explicit any_allocator(Allocator const& a) noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-01-16 05:57:06 UTC  
> Updated_at: 2022-01-16 05:57:07 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785394555  

Well, this can't be `noexcept` now, can it?

> Username: alandefreitas  
> Created_at: 2022-01-16 06:12:29 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785395504  

Why not? Isn't that what we are doing in `impl`:  
  
![image](https://user-images.githubusercontent.com/5369819/149649420-b63bf758-7c1a-4d0f-b760-6983dc191bdd.png)

> Username: pdimov  
> Created_at: 2022-01-16 15:18:34 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785456125  

Because it calls `std::allocate_shared`.

> Username: alandefreitas  
> Created_at: 2022-01-16 18:10:37 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785476944  

Oh... that settles it.


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-16 05:57:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-853632917  

📁 include/boost/url/detail/any_allocator.hpp

```diff
  26 |+ struct any_allocator_base
  27 |+ {
  28 |+     struct base {
```

> Username: vinniefalco  
> Created_at: 2022-01-16 05:57:53 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785394605  

Why do these braces keep showing up on the same line as the preceding text?

> Username: alandefreitas  
> Created_at: 2022-01-16 06:20:18 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785396003  

Because I have to do everything manually to replicate the formatting you like and (unlike clang-format) I fail sometimes.  
  
Mimicking the style very often takes much more time than coding. I've been working so hard on style that I even wrote a small program just to try to infer the formatting you like from the source code: https://github.com/alandefreitas/clang-unformat   
  
This helped a little but I still can't apply it to the whole file because some patterns, such as   
  
```cpp  
template <class T>  
any_allocator<T>::  
any_allocator() noexcept  
: any_allocator(std::allocator<char>{})  
{  
}  
```  
  
are very peculiar and have no correspondence in clang-format, which would give me:  
  
```cpp  
template<class T>  
any_allocator<T>::any_allocator() noexcept  
    : any_allocator(std::allocator<char>{})  
{  
}  
```  
  
so I have to keep doing that manually and errors are going to happen eventually.

> Username: vinniefalco  
> Created_at: 2022-01-16 18:04:20 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785476259  

It seems like the problem is that your editor insists on putting the brace on the same line. This is not just a "formatting issue" - in order to set a breakpoint after the arguments are constructed but before a function executes, the brace needs to be on its own line. And this also affects coverage.

> Username: alandefreitas  
> Created_at: 2022-01-16 18:41:55 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785480139  

> It seems like the problem is that your editor insists on putting the brace on the same line.   
  
The editor does nothing. It's all manual. If I let the editor do its thing, it's going to remove the formatting you like in other places, I'm not going to notice, and then you're going to complain about that too, the IDE will remove formatting at some other place, and we are never going to move forward.  
  
> This is not just a "formatting issue" - in order to set a breakpoint after the arguments are constructed but before a function executes, the brace needs to be on its own line. And this also affects coverage.  
  
I agree 100% with the rationale for breaking before braces. You're correct and the IDE is set to do that.    
  
The thing is breaking before braces is not the problem at all. For instance, in  
  
```cpp  
template <class T>  
any_allocator<T>::  
any_allocator() noexcept  
: any_allocator(std::allocator<char>{})  
{  
}  
```  
  
why would you need to set a breakpoint at  
  
```cpp  
any_allocator<T>::  
```  
  
?  
  
The issue is not breaking before braces here. Braking before braces is very simple, and the editor is set to do that even without clang-format or anything. Even notepad can probably do that.   
  
The real problem is I have to turn off any kind of automatic formatting, including breaking before braces, to maintain other conventions that have nothing to with that. Even with clang-format, which has hundreds of options, there is no option that produces:  
  
```cpp  
any_allocator<T>::  
any_allocator() noexcept  
```  
  
At this point, my `.clang-format` matches your style almost perfectly, but I have to keep it off so it doesn't break these small conventions. I only apply it   
  
For instance, if I turn it on and apply it completely to `impl/any_allocator.hpp`, I get [this](https://gist.github.com/alandefreitas/abf0004ca224b38459488f7dedd9c495), which matches the functional components of your style completely with no room for mistakes. But it's going to break the conventions such as   
  
```  
any_allocator<T>::  
```  
  
or   
  
```  
keywords  
return_type  
type::  
```  
  
so I can't keep applying it to everything I commit.   
  
When things are manual, there are going to be mistakes here and there. I can fix them. No problem. But they are going to happen every once in a while.


---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-01-16 06:29:34 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1013819660  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 11

> Username: codecov[bot]  
> Created_at: 2022-01-16 08:44:05 UTC  
> Updated_at: 2022-01-18 19:48:09 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1013834327  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/112?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#112](https://codecov.io/gh/CPPAlliance/url/pull/112?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (c00ae2f) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/cbd0ad64ccc4d0416055a190402ba0e6864eaafa?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (cbd0ad6) will **increase** coverage by `0.05%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head c00ae2f differs from pull request most recent head 432f0e1. Consider uploading reports for the commit 432f0e1 to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/112/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/112?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #112      +/-   ##  
===========================================  
+ Coverage    96.17%   96.22%   +0.05%       
===========================================  
  Files          103      107       +4       
  Lines         5406     5487      +81       
===========================================  
+ Hits          5199     5280      +81       
  Misses         207      207                
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/112?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/any\_allocator.hpp](https://codecov.io/gh/CPPAlliance/url/pull/112/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvYW55X2FsbG9jYXRvci5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/any\_allocator.hpp](https://codecov.io/gh/CPPAlliance/url/pull/112/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9hbGxvY2F0b3IuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/type\_id.hpp](https://codecov.io/gh/CPPAlliance/url/pull/112/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3R5cGVfaWQuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/any\_allocator.hpp](https://codecov.io/gh/CPPAlliance/url/pull/112/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9hbnlfYWxsb2NhdG9yLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/112?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/112?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [cbd0ad6...432f0e1](https://codecov.io/gh/CPPAlliance/url/pull/112?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-01-16 18:19:32 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1013926190  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-16 19:02:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-853709834  

📁 include/boost/url/detail/any_allocator.hpp

```diff
  57 |+             : empty_value<allocator_type, 0>(empty_init_t(), a)
  58 |+         {
  59 |+             any_allocator_base::base::type = get_type_id<allocator_type>();
```

> Username: vinniefalco  
> Created_at: 2022-01-16 19:02:02 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785482194  

can't you write `type = get_type_id<allocator_type>()`?


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-16 19:03:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-853709951  

📁 include/boost/url/detail/type_id.hpp

```diff
  28 |+ }
  29 |+ }
  30 |+ }
```

> Username: vinniefalco  
> Created_at: 2022-01-16 19:03:19 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785482330  

closing namespace comments


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-16 19:03:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-853709973  

📁 test/unit/CMakeLists.txt

```diff
  85 | add_test(NAME boost_url_tests COMMAND boost_url_tests)
  87 |- add_dependencies(tests boost_url_tests)
  86 |+ add_dependencies(tests boost_url_tests)
```

> Username: vinniefalco  
> Created_at: 2022-01-16 19:03:37 UTC  
> Updated_at: 2022-01-16 19:03:38 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r785482349  

missing newline


---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2022-01-16 19:14:30 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1013934911  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2022-01-16 21:04:32 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1013952048  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2022-01-17 01:59:31 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1014068479  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2022-01-17 02:09:30 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1014073247  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2022-01-17 03:09:31 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1014096648  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2022-01-17 03:29:33 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1014107566  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2022-01-17 04:14:31 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1014124326  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2022-01-17 15:14:47 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1014650809  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2022-01-17 17:04:35 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1014741198  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2022-01-18 01:24:33 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1014991710  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2022-01-18 02:29:34 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1015017161  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-18 05:37:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-854962801  

📁 include/boost/url/any_allocator.hpp

```diff
  50 |+         allocator_size_type_t<any_allocator<T>>;
  51 |+ 
  52 |+     using difference_type =
```

> Username: vinniefalco  
> Created_at: 2022-01-18 05:37:59 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r786428352  

shouldn't these two types be `std::size_t` and `std::ptrdiff_t`? Why get boost.core involved?


---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-18 05:39:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-854963513  

📁 include/boost/url/detail/any_allocator.hpp

```diff
 136 |+         try_allocate_aligned(std::size_t n,
 137 |+             std::size_t size,
 138 |+             std::size_t align)
```

> Username: vinniefalco  
> Created_at: 2022-01-18 05:39:31 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r786428930  

this is becoming more readable :)


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-18 05:40:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-854964090  

📁 test/unit/any_allocator.cpp

```diff
 685 |+         testRequirements<int>();
 686 |+         testRequirements<long>();
 687 |+         testRequirements<
```

> Username: vinniefalco  
> Created_at: 2022-01-18 05:40:51 UTC  
> Updated_at: 2022-01-18 05:40:52 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r786429372  

This is a lot more readable now


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-18 05:41:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-854964277  

📁 test/unit/any_allocator.cpp

```diff
 653 |+     }
 654 |+ 
 655 |+     struct large_struct
```

> Username: vinniefalco  
> Created_at: 2022-01-18 05:41:15 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r786429526  

`struct S` lol

> Username: alandefreitas  
> Created_at: 2022-01-18 17:24:49 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r786991048  

LoL  
  
The intention of this large struct is to test something whose size is more than max_align but still needs padding. I couldn't find a short name for that.  
  
Yes. I think I'll just go with `S` then haha


---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-18 05:45:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-854966336  

📁 include/boost/url/any_allocator.hpp

```diff
  28 |+     @li <a
  29 |+      href="https://en.cppreference.com/w/cpp/named_req/Allocator"
  30 |+        >Allocator (cppreference.com)</a>
```

> Username: vinniefalco  
> Created_at: 2022-01-18 05:45:59 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r786431154  

Sometimes you have to break the rules. In this case I would format it thusly:  
```  
    @par Specification  
    @li <a href="https://en.cppreference.com/w/cpp/named_req/Allocator"  
        >Allocator (cppreference.com)</a>  
```

> Username: alandefreitas  
> Created_at: 2022-01-18 17:36:44 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r787000369  

Even if it goes on "forever"? That's more than 100 chars.


---

## Review 32 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-18 05:46:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-854966734  

📁 include/boost/url/any_allocator.hpp

```diff
 120 |+ 
 121 |+ } // namespace urls
 122 |+ } // namespace boost
```

> Username: vinniefalco  
> Created_at: 2022-01-18 05:46:52 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r786431473  

I don't bother putting the word "namespace" because it doesn't add any information, but if you feel better with it there, I won't complain. See:  
https://github.com/boostorg/beast/blob/5bdd1efb8be740bb5425adc054c990818134f3e2/include/boost/beast/http/basic_parser.hpp#L691

> Username: alandefreitas  
> Created_at: 2022-01-18 17:41:36 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r787004060  

So can I leave the word "namespace"? That would be one less thing to get wrong because it's how it's generated automatically.

> Username: vinniefalco  
> Created_at: 2022-01-18 20:17:39 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r787117939  

you can leave it in if you want, but I think you will over time learn that these "automatic" formatting tools are going to cause you more work than they relieve.


---

## Review 33 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-18 05:47:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-854966920  

📁 include/boost/url/detail/any_allocator.hpp

```diff
  51 |+     struct impl
  52 |+         : public base
  53 |+         , private empty_value<
```

> Username: vinniefalco  
> Created_at: 2022-01-18 05:47:21 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r786431594  

empty base optimization is good


---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-18 05:47:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-854967194  

📁 include/boost/url/detail/any_allocator.hpp

```diff
  96 |+         is_equal(
  97 |+             const any_allocator_base::base& other)
  98 |+             override
```

> Username: vinniefalco  
> Created_at: 2022-01-18 05:47:59 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r786431888  

I never break `override`, `const`, or `noexcept` to the next line, even if it would go over 50 cols


---

## Review 35 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-18 05:49:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-854967679  

📁 include/boost/url/detail/any_allocator.hpp

```diff
 151 |+             typename std::enable_if <
 152 |+             N< alignof(max_align_t), void*>::type
 153 |+             try_allocate_aligned(std::size_t n,
```

> Username: vinniefalco  
> Created_at: 2022-01-18 05:49:05 UTC  
> Updated_at: 2022-01-18 05:49:06 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r786432267  

Something weird is going on here with the margin

> Username: alandefreitas  
> Created_at: 2022-01-18 19:39:42 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r787092300  

Should it be larger? That was my fault. It was trying to do that triangle and trapeziums, etc...


---

## Comment 36

> Username: cppalliance-bot  
> Created_at: 2022-01-18 19:54:33 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1015782879  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 37 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-18 20:16:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/112#pullrequestreview-855927982  

📁 include/boost/url/any_allocator.hpp

```diff
  12 |+ 
  13 |+ #include <boost/url/detail/any_allocator.hpp>
  14 |+ #include <boost/url/detail/config.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-01-18 20:16:20 UTC  
> Updated_at: 2022-01-18 20:16:26 UTC  
> Url: https://github.com/boostorg/url/pull/112#discussion_r787117099  

config has to come first for every public header


---

## Comment 38

> Username: cppalliance-bot  
> Created_at: 2022-01-18 21:04:51 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1015832336  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 39

> Username: cppalliance-bot  
> Created_at: 2022-01-19 00:24:31 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1015956057  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 40

> Username: cppalliance-bot  
> Created_at: 2022-01-19 01:39:32 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1015996465  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 41

> Username: cppalliance-bot  
> Created_at: 2022-01-19 16:09:36 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1016622646  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 42

> Username: cppalliance-bot  
> Created_at: 2022-01-19 18:14:36 UTC  
> Url: https://github.com/boostorg/url/pull/112#issuecomment-1016737138  

An automated preview of the documentation is available at [https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://112.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---
