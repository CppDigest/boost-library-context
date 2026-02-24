# #232 - assertion failure with crafted regex [Closed]

> Username: cmazakas  
> Created at: 2024-11-22 22:34:43 UTC  
> Updated at: 2024-12-05 13:08:53 UTC  
> Closed at: 2024-12-05 13:08:53 UTC  
> Url: https://github.com/boostorg/regex/issues/232  

```cpp  
#include <boost/regex.hpp>  
#include <cstddef>  
  
template<std::size_t N0, std::size_t N = N0 - 1>  
void tester( char const (&str)[ N0 ] )  
{  
   std::string s(N, '\0');  
   std::memcpy(s.data(), str, N);  
   boost::regex rx(s);  
   boost::match_results<std::string::const_iterator> what;  
   std::string where(15, 'H');  
   bool match = boost::regex_match(where, what, rx, boost::match_default | boost::match_partial | boost::match_perl | boost::match_posix | boost::match_any);  
   (void) match;  
}  
  
int main()  
{  
   char const str2[] = "(Y(*COMMIT)|\\K\\D|.)+";  
   tester( str2 );  
}  
```  
This regex causes an assertion to be tripped (https://godbolt.org/z/7hYfKx96n):  
```  
output.s: /app/boost/include/boost/regex/v5/match_results.hpp:625: void boost::match_results<BidiIterator, Allocator>::maybe_assign(const boost::match_results<BidiIterator, Allocator>&) [with BidiIterator = __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> >; Allocator = std::allocator<boost::sub_match<__gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > >]: Assertion `base2 >= 0' failed.  
Program terminated with signal: SIGSEGV  
```  
  
If you examine the code, this is the offending line: https://github.com/boostorg/regex/blob/boost-1.86.0/include/boost/regex/v5/match_results.hpp#L625  
  
However, don't be so easily fooled here! The actual bug here is quite decoupled from this, this assertion just happens to catch the UB.  
  
The input is: `"HHHHHHHHHHHHHHH"` and the regex is `/(Y(*COMMIT)|\\K\\D|.)+/`  
  
I double-checked the implementation against Perl and we're actually going the correct thing here, but we fail to unwind properly.  
  
The correct regex behavior here is that we match in the middle, on the `\\K\\D` which we actually do. The problem is that we don't stop processing the input when we reach the end of the string (even though we should).  
  
This regex only fails because of the `(*COMMIT)` we introduce. `(*COMMIT)` turns off the `match_any` flag which means this branch: https://github.com/boostorg/regex/blob/boost-1.86.0/include/boost/regex/v5/perl_matcher_non_recursive.hpp#L1100  
  
gets hit, and when that happens, we invoke `unwind(false)` which is incorrect. We unwind with "match not found" which keeps the parse going and then we match on the wildcard at the end and then from there, all hell breaks loose.  
  
I've tried a few things to fix this behavior but every attempt that fixes this issue breaks a million others in the regression tests.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-11-23 12:24:02 UTC  
> Url: https://github.com/boostorg/regex/issues/232#issuecomment-2495462090  

Very minor nitpick on the test code - there's no need for a memcpy there, either match the string directly, or construct the std::string from the char array directly.  
  
With regard to the failure, there is a fundamental mismatch between the semantics of `match_posix` and a Perl regular expression containing `\\K`, so we should flat out disallow that.  I also note the match flags have `match_perl|match_posix` and I'm 99.999% sure these should be mutually exclusive.  
  
So.... how to fix?  
  
The cop-out would be to simply unset match_posix if match_perl is also set.  But this looks like a flat-out usage error to me - we can only match according to ONE set of rules - so I would be inclined to throw a logic error inside this error checker: https://github.com/boostorg/regex/blob/f0afa5d9b870f2211a937c437357a5e75cb534d2/include/boost/regex/v5/perl_matcher.hpp#L48.  Unfortunately, that mean we need to get the fuzzer test case fixed as well.  My bad, should have spotted that was an impossible combination.  
  
Also unfortunately, this is only half a fix, we need to protect against match_posix being specified when the expression contains something unmatchable under those rules.  I think we can do this here: https://github.com/boostorg/regex/blob/f0afa5d9b870f2211a937c437357a5e75cb534d2/include/boost/regex/v5/perl_matcher_common.hpp#L100 where m_disable_match_any is set for all of the Perl `(*SOMETHING)` extensions (might need to extend that setting for \\K as well), none of which really make sense to be matched by the leftmost-longest POSIX rules.  The question is what to do: we can switch the match flags over to match_perl, or we can throw as the user has requested something "impossible".  I tend to lean towards the latter, but the former might be easier?

---

## Comment 2

> Username: cmazakas  
> Created at: 2024-11-25 20:42:19 UTC  
> Updated at: 2024-11-25 20:57:20 UTC  
> Url: https://github.com/boostorg/regex/issues/232#issuecomment-2498992333  

> or we can throw as the user has requested something "impossible".  
  
I think we should just do this. I say this because, I actually assumed the library would already be throwing if I passed it an invalid combination of flags, which is partially why I never considered changing the input from the fuzzer.  
  
I'll update the test suite to ensure that provided the correct flags, that regex still doesn't break the code.  
  
And I'll also add some tests that verify the options passed into the regex creator throw as we'd expect.  
  
Edit: btw, I've confirmed locally that removing the `match_posix` flag makes the assertion go away and we're iterating the state machine as we should be.
