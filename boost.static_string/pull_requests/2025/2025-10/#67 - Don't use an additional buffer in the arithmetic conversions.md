# #67 Don't use an additional buffer in the arithmetic conversions [Merged]

> Username: gennaroprota  
> Created at: 2025-10-02 17:08:39 UTC  
> Updated at: 2025-10-09 15:49:30 UTC  
> Merged at: 2025-10-09 15:49:30 UTC  
> Closed at: 2025-10-09 15:49:30 UTC  
> Url: https://github.com/boostorg/static_string/pull/67  

In the case of the floating point conversions, this effectively avoids a copy of the buffer contents. In the case of the integer conversions, it doesn't eliminate the copy, but still removes the extra buffer.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2025-10-03 10:06:29 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#issuecomment-3365123970  

Maybe merge #64 first to let the full CI run, I disabled C++26 testing just now to let it pass until #66 is resolved

---

## Review 2 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-10-03 17:29:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/67#pullrequestreview-3300099251  

## Pull Request Overview  
  
This PR optimizes arithmetic conversion functions by eliminating the use of intermediate character buffers. The changes directly write formatted values into the result string objects instead of using temporary buffers and then copying the data.  
  
- Replaced temporary char/wchar_t arrays with direct manipulation of static_string objects  
- Added friend function declarations to enable access to private members  
- Modified floating-point conversions to write directly to result string data  
  
  
  
  
  
---  
  
<sub>**Tip:** Customize your code reviews with copilot-instructions.md. <a href="/boostorg/static_string/new/develop/.github?filename=copilot-instructions.md" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Create the file</a> or <a href="https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">learn how to get started</a>.</sub>

📁 include/boost/static_string/static_string.hpp

```diff
 556 |-   return static_string<N>(digits_begin, std::distance(digits_begin, digits_end));
 557 |+   result.set_size(std::distance(digits_begin, digits_end));
 558 |+   std::char_traits<char>::move(result.data(), digits_begin, result.size());
```

> Username: Copilot  
> Created_at: 2025-10-03 17:29:52 UTC  
> Updated_at: 2025-10-03 17:29:53 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2402749643  

Setting the size to N initially and then immediately changing it creates an inefficient pattern. The string is first sized to N, then resized based on the actual digits needed. This could lead to unnecessary initialization of the buffer contents.

---

📁 include/boost/static_string/static_string.hpp

```diff
 608 |   // we assume that the result is always positive
 602 |-   if (std::size_t(std::snprintf(buffer, N + 1, "%f", value)) > N)
 609 |+   std::size_t length = std::snprintf(result.data(), N + 1, "%f", value);
```

> Username: Copilot  
> Created_at: 2025-10-03 17:29:52 UTC  
> Updated_at: 2025-10-03 17:29:53 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2402749652  

Writing N + 1 characters to result.data() when the result string has capacity N is a buffer overflow. The snprintf call should use N + 1 for the buffer size only if result has been allocated with that capacity.

---

📁 include/boost/static_string/static_string.hpp

```diff
 639 |   // we assume that the result is always positive
 633 |-   if (std::size_t(std::snprintf(buffer, N + 1, "%Lf", value)) > N)
 640 |+   std::size_t length = std::snprintf(result.data(), N + 1, "%Lf", value);
```

> Username: Copilot  
> Created_at: 2025-10-03 17:29:52 UTC  
> Updated_at: 2025-10-03 17:29:53 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2402749657  

Same buffer overflow issue as with the double version - writing N + 1 characters to a buffer with capacity N.

---

📁 include/boost/static_string/static_string.hpp

```diff
 669 |-     std::swprintf(buffer, N + 1, L"%f", value);
 675 |+   long long num_written =
 676 |+     std::swprintf(result.data(), N + 1, L"%f", value);
```

> Username: Copilot  
> Created_at: 2025-10-03 17:29:52 UTC  
> Updated_at: 2025-10-03 17:29:53 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2402749664  

Buffer overflow issue - attempting to write N + 1 wide characters to a buffer with capacity N.

---

📁 include/boost/static_string/static_string.hpp

```diff
 706 |-     std::swprintf(buffer, N + 1, L"%Lf", value);
 711 |+   long long num_written =
 712 |+     std::swprintf(result.data(), N + 1, L"%Lf", value);
```

> Username: Copilot  
> Created_at: 2025-10-03 17:29:53 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2402749670  

Buffer overflow issue - attempting to write N + 1 wide characters to a buffer with capacity N.


---

## Review 3 [Commented]

> Username: Flamefire  
> Created_at: 2025-10-04 08:57:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/67#pullrequestreview-3301693255  

📁 include/boost/static_string/static_string.hpp

```diff
 794 | }
 795 | 
 796 |+ template<std::size_t N, typename Integer>
```

> Username: Flamefire  
> Created_at: 2025-10-04 08:57:28 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2403861862  

Why are those declarations required **after** the definitions above?

> Username: gennaroprota  
> Created_at: 2025-10-06 10:41:32 UTC  
> Updated_at: 2025-10-06 10:41:33 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2405665074  

Yes, those are not needed, thanks.


---

## Review 4 [Commented]

> Username: Flamefire  
> Created_at: 2025-10-04 08:58:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/67#pullrequestreview-3301693471  

📁 include/boost/static_string/static_string.hpp

```diff
 959 |   friend class basic_static_string;
 960 |+ 
 961 |+   template<std::size_t P, typename Integer>
```

> Username: Flamefire  
> Created_at: 2025-10-04 08:58:25 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2403862089  

They don't use any private properties, do they? So no need for friends

> Username: gennaroprota  
> Created_at: 2025-10-06 10:42:50 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2405667883  

They use `set_size()`, which is private in `basic_static_string` due to private inheritance.

> Username: ashtum  
> Created_at: 2025-10-07 06:03:05 UTC  
> Updated_at: 2025-10-07 06:03:32 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2409473740  

Adding all these friend functions for to_string implementations feels odd. Ideally, users should be able to create their own similar utility functions with comparable performance, but they can't make their functions friends of `static_string`. Would adding a `resize_and_overwrite` member function remove the need for those friends?

> Username: gennaroprota  
> Created_at: 2025-10-07 15:07:45 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2410974467  

OK. I've added `resize_and_overwrite()` and implemented the arithmetic conversions in terms of it. Please have a look.

> Username: ashtum  
> Created_at: 2025-10-07 15:44:47 UTC  
> Updated_at: 2025-10-07 15:44:48 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2411088972  

Ah, it was just a suggestion, but you've already implemented it, and it seems to work.  
@pdimov, does adding `resize_and_overwrite()` to `static_string` make sense? Context: Gennaro wants to rewrite the `to_static_string` functions and eliminate the use of a temporary buffer:  
https://github.com/boostorg/static_string/blob/0d255f74386a895df08acda33333b17e806fdc6d/include/boost/static_string/static_string.hpp#L552-L556  
This enables RVO and produces more efficient assembly: https://godbolt.org/z/bbesGEPj8

> Username: pdimov  
> Created_at: 2025-10-07 16:23:37 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2411194266  

I suppose it does.


---

## Review 5 [Commented]

> Username: Flamefire  
> Created_at: 2025-10-06 11:52:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/67#pullrequestreview-3304140553  

📁 include/boost/static_string/static_string.hpp

```diff
 553 |-   const auto digits_end = std::end(buffer);
 552 |+   static_string<N> result;
 553 |+   result.set_size(N);
```

> Username: Flamefire  
> Created_at: 2025-10-06 11:51:21 UTC  
> Updated_at: 2025-10-06 11:52:48 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2405907309  

This is redundant here, isn't it?  
  
```suggestion  
```

> Username: gennaroprota  
> Created_at: 2025-10-06 14:39:00 UTC  
> Updated_at: 2025-10-06 14:39:01 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2406723399  

The first `set_size()` is not redundant with the constructor, because the constructor creates a string with size zero. I could avoid it by writing:  
  
```  
const auto digits_end = result.begin() + N;  
```  
  
but I don't see any way around the second `set_size()`.

> Username: Flamefire  
> Created_at: 2025-10-06 15:00:20 UTC  
> Updated_at: 2025-10-06 15:00:47 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2406847062  

Ah yes, I missed the use of `result.end()`, so it is fine then although a bit confusing to some (like me)  
  
To me the most readable approach would be `const auto digits_end = result.data() + N;` as it highlights the buffer use and mirrors the use in the `move` below.

---

📁 include/boost/static_string/static_string.hpp

```diff
 566 |-   const auto digits_end = std::end(buffer);
 569 |+   static_wstring<N> result;
 570 |+   result.set_size(N);
```

> Username: Flamefire  
> Created_at: 2025-10-06 11:51:39 UTC  
> Updated_at: 2025-10-06 11:52:48 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2405908176  

See above  
```suggestion  
```


---

## Review 6 [Commented]

> Username: Flamefire  
> Created_at: 2025-10-06 15:01:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/67#pullrequestreview-3305285442  

📁 include/boost/static_string/static_string.hpp

```diff
 569 |+   static_wstring<N> result;
 570 |+   result.set_size(N);
 571 |+   const auto digits_end = result.end();
```

> Username: Flamefire  
> Created_at: 2025-10-06 15:01:27 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2406853275  

```diff  
-  const auto digits_end = result.end();  
+  const auto digits_end = result.data() + N;  
```


---

## Comment 7

> Username: ashtum  
> Created_at: 2025-10-06 19:43:47 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#issuecomment-3373667961  

> this effectively avoids a copy of the buffer contents. In the case of the integer conversions, it doesn't eliminate the copy, but still removes the extra buffer.  
  
Nice, it also enables RVO and produces more efficient assembly because it skips the call to the constructor that is not `noexcept`: https://godbolt.org/z/bbesGEPj8

---

## Review 8 [Commented]

> Username: ashtum  
> Created_at: 2025-10-07 16:46:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/67#pullrequestreview-3310946130  

📁 include/boost/static_string/static_string.hpp

```diff
6742 |+ {
6743 |+   if (n > max_size()) {
6744 |+     detail::throw_exception<std::length_error>("n > max_size() in resize_and_overwrite()");
```

> Username: ashtum  
> Created_at: 2025-10-07 16:34:32 UTC  
> Updated_at: 2025-10-07 16:46:17 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2411224996  

I believe the message "n > max_size()" (identical to what's thrown in resize()) is sufficient. there's no need to proliferate string literals.

> Username: gennaroprota  
> Created_at: 2025-10-08 14:00:04 UTC  
> Updated_at: 2025-10-08 14:00:05 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2413966037  

Is it sufficient? It gives no clue to the user about where the problem occurred.

> Username: ashtum  
> Created_at: 2025-10-08 14:08:37 UTC  
> Updated_at: 2025-10-08 14:08:38 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2413992715  

I believe `detail::throw_exception` uses `BOOST_THROW_EXCEPTION` under the hood, which would also print the source location. But generally, the exception message doesn't need to include the current function name, "n > max_size()" is descriptive enough for this kind of error message in the code.

> Username: pdimov  
> Created_at: 2025-10-08 14:11:59 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2414003652  

BOOST_THROW_EXCEPTION, if used inside detail::throw_exception, would emit the source location of detail::throw_exception.  
  
You probably want boost::throw_with_location here, or the two argument overload of boost::throw_exception, so that the source location of the throw is captured, not that of detail::throw_exception.

> Username: ashtum  
> Created_at: 2025-10-08 14:16:51 UTC  
> Updated_at: 2025-10-08 14:16:52 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2414025105  

@gennaroprota, it might be more convenient to create an issue for this and address it in a separate PR once you’re done with the current one.

> Username: gennaroprota  
> Created_at: 2025-10-08 15:18:24 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2414227120  

What if `BOOST_STATIC_STRING_STANDALONE` is defined?

> Username: pdimov  
> Created_at: 2025-10-08 15:36:16 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2414284516  

If this configuration is to still be supported (is it?) you'd probably need to define BOOST_STATIC_STRING_CURRENT_LOCATION and then use `detail::throw_exception( E(...), BOOST_STATIC_STRING_CURRENT_LOCATION )` instead of `boost::throw_exception( E(...), BOOST_CURRENT_LOCATION )`.  
  
Something better might also be possible, I'm not sure.

> Username: gennaroprota  
> Created_at: 2025-10-08 16:16:59 UTC  
> Updated_at: 2025-10-08 16:17:00 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2414395840  

> If this configuration is to still be supported (is it?)  
  
I'd be happy to remove the support. The less conditional compilation, the better.

> Username: pdimov  
> Created_at: 2025-10-08 16:25:33 UTC  
> Updated_at: 2025-10-08 16:25:34 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2414415913  

We should probably ask on the list.


📁 test/static_string.cpp

```diff
7370 | 
7371 |+ void
7372 |+ testResizeAndOverwrite()
```

> Username: ashtum  
> Created_at: 2025-10-07 16:46:04 UTC  
> Updated_at: 2025-10-07 16:46:17 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2411252647  

It might be better to split this PR into two commits: the first adds the `resize_and_overwrite` function, and the second improves the performance of the `to_string` implementations.


---

## Review 9 [Commented]

> Username: Flamefire  
> Created_at: 2025-10-07 17:12:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/67#pullrequestreview-3311079996  

📁 include/boost/static_string/static_string.hpp

```diff
3767 |+       Resize the string and overwrite its contents.
3768 |+ 
3769 |+       Resizes the string to contain `n` characters, and uses the
```

> Username: Flamefire  
> Created_at: 2025-10-07 17:12:55 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2411318940  

I'm not sure that is useful, the size passed here doesn't really matter, does it? Maybe a `write_and_resize(op)` is more useful? Or is there any realistic use case for this param being less than N?

> Username: pdimov  
> Created_at: 2025-10-07 17:24:52 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2411358618  

That's how the function is specified for std::string.  
  
For static_string specifically the only thing the passed size does is throw if it's too big.

> Username: Flamefire  
> Created_at: 2025-10-07 17:44:28 UTC  
> Url: https://github.com/boostorg/static_string/pull/67#discussion_r2411433036  

Ok, makes sense


---
