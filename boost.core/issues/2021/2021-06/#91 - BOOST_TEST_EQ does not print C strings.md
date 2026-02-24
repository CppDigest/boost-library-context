# #91 - BOOST_TEST_EQ does not print C strings [Open]

> Username: Lastique  
> Created at: 2021-06-03 21:49:33 UTC  
> Updated at: 2021-06-04 01:54:29 UTC  
> Url: https://github.com/boostorg/core/issues/91  

`BOOST_TEST_EQ` that involve C-style strings do not output string contents. For example, `BOOST_TEST_EQ(*++itr, "foo");` shows this on failure:  
  
```  
libs\filesystem\test\path_test.cpp(341): test '*++itr == "foo"' ('"c:"' == '00007FF79AE87414') failed in function 'void __cdecl `anonymous-namespace'::iterator_tests(void)'  
```  
  
This seems to be caused by this `test_output_impl` overload:  
  
https://github.com/boostorg/core/blob/e53393357f32078f55cdb291a536bb567895bc62/include/boost/core/lightweight_test.hpp#L167  
  
Is this intended to return `const void*` instead of `const char*`?  
  
If yes, then could we make this behavior optional?

---

## Comment 1

> Username: Lastique  
> Created at: 2021-06-03 21:51:15 UTC  
> Url: https://github.com/boostorg/core/issues/91#issuecomment-854204613  

There's also a similar overload for `char*`.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-06-03 22:14:14 UTC  
> Url: https://github.com/boostorg/core/issues/91#issuecomment-854220429  

We have a separate macro for C strings, which is `BOOST_TEST_CSTR_EQ`. `BOOST_TEST_EQ` tests for equality using `==`, which for `char*` compares addresses, so the output also prints addresses to match that. `BOOST_TEST_CSTR_EQ` test for equality with `strcmp`, and prints the C strings to match.

---

## Comment 3

> Username: Lastique  
> Created at: 2021-06-03 23:33:36 UTC  
> Url: https://github.com/boostorg/core/issues/91#issuecomment-854251944  

> `BOOST_TEST_EQ` tests for equality using ==, which for char* compares addresses, so the output also prints addresses to match that.  
  
In Boost.Filesystem case we are comparing `path` with string literals using `operator==`, which is intended. `strcmp` wouldn't work there.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-06-03 23:53:08 UTC  
> Url: https://github.com/boostorg/core/issues/91#issuecomment-854257938  

Yes, I understand that, but how are we supposed to distinguish the two cases? In general we have no idea what `==` does. We could special-case `std::string`, I suppose, and then it won't work for `string_view`, so we'll have to special case that too?

---

## Comment 5

> Username: Lastique  
> Created at: 2021-06-04 00:17:30 UTC  
> Url: https://github.com/boostorg/core/issues/91#issuecomment-854266324  

I think, the special case is comparing two pointers and the rest can be assumed to operate as intended (i.e. there must be a user-defined comparison or conversion operator for the comparison to compile).

---

## Comment 6

> Username: Lastique  
> Created at: 2021-06-04 00:20:19 UTC  
> Url: https://github.com/boostorg/core/issues/91#issuecomment-854267322  

Also, the comparison logic is unrelated to output formatting, which is what this issue about.

---

## Comment 7

> Username: pdimov  
> Created at: 2021-06-04 00:42:34 UTC  
> Url: https://github.com/boostorg/core/issues/91#issuecomment-854274589  

Not entirely unrelated; when the test fails the values need to tell us why.  
  
> the special case is comparing two pointers and the rest can be assumed to operate as intended  
  
This might be a good heuristic, yes (we also need to take arrays into account). Still a bit dangerous for my taste because misinterpreting a `char*` that doesn't point to a C string would be bad... but it might be acceptable if we take care to quote the unprintable characters, which is a good thing to have anyway and something I've been thinking we need to do.

---

## Comment 8

> Username: glenfe  
> Created at: 2021-06-04 01:54:29 UTC  
> Url: https://github.com/boostorg/core/issues/91#issuecomment-854298169  

I'd prefer not assuming (by default) that `char*` means C string. If someone wants to test some `operator==(const X&, const char*)` with `BOOST_TEST_EQ(x, "y")` then maybe we could provide some alternative macro that allows supplying a mechanism to control how the LHS and RHS arguments are formatted.
