# #70 - MSVC segfaults in debug mode tests [Closed]

> Username: Flamefire  
> Created at: 2025-10-04 10:14:28 UTC  
> Updated at: 2025-10-06 15:22:48 UTC  
> Closed at: 2025-10-06 15:22:48 UTC  
> Url: https://github.com/boostorg/static_string/issues/70  

See https://github.com/boostorg/static_string/actions/runs/18228276832/job/51905429213  
  
>  EXIT STATUS: -1073741571   
  
I can reproduce this locally: It fails when calling `test_replace` and it seems to be caused by 830 tests, creating 2 400-len static_string instances (409 bytes each) which MSVC doesn't seem to like.  
  
I guess constructing the `basic_string` inside `testR` will be enough

---

## Comment 1

> Username: gennaroprota  
> Created at: 2025-10-06 10:58:15 UTC  
> Url: https://github.com/boostorg/static_string/issues/70#issuecomment-3371037459  

You mean changing `testR()` to:  
  
```  
template <class S>  
bool  
testR(const char * cstr, typename S::size_type pos1, typename S::size_type n1, const typename S::value_type* str, const char * expected)  
{  
  S s(cstr);  
  typename S::const_iterator first = s.begin() + pos1;  
  typename S::const_iterator last = s.begin() + pos1 + n1;  
  s.replace(first, last, str);  
  return s == expected;  
}  
```  
  
?  
  
If so, can you please test this change locally? I can't reproduce the issue.

---

## Comment 2

> Username: Flamefire  
> Created at: 2025-10-06 12:58:35 UTC  
> Url: https://github.com/boostorg/static_string/issues/70#issuecomment-3371512647  

CI shows that. Fixing and testing in https://github.com/boostorg/static_string/pull/71

---

## Comment 3

> Username: gennaroprota  
> Created at: 2025-10-06 14:12:49 UTC  
> Url: https://github.com/boostorg/static_string/issues/70#issuecomment-3371895880  

Thanks!
