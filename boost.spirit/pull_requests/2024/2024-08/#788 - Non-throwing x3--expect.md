# #788 Non-throwing x3::expect [Merged]

> Username: saki7  
> Created at: 2024-08-11 16:13:55 UTC  
> Updated at: 2025-05-10 00:01:27 UTC  
> Merged at: 2024-08-17 22:10:39 UTC  
> Closed at: 2024-08-17 22:10:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/788  

This PR is a fresh implementation of the non-throwing `x3::expect`, which was originally abandoned in 2017.  
  
supersedes and closes #242  
cc: @djowel @wanghan02   
  
## Summary  
Expectations like `lit('a') > 'b'` will be parsed **90 times faster** 🚀  than the current implementation.  
Non-throwing mode is only enabled when the macro is explicitly specified by user, so this PR has 100% backward compatibility.  
  
![image](https://github.com/user-attachments/assets/c7daeff5-0969-4929-a25d-b0c8c1dbc9be)  
  
<details>  
<summary>Show benchmark C++ code</summary>  
  
Stolen from https://github.com/boostorg/spirit/pull/242#issuecomment-327425667, modified by saki7  
  
- `cd test/x3`  
- Save the code in a file  
- Add `run your_file.cpp ;` to `Jamfile`  
- `../../../../b2 toolset=msvc cxxstd=17 cxxflags=/utf-8 release -j24`  
  
```cpp  
#define BOOST_SPIRIT_X3_THROW_EXPECTATION_FAILURE 0  
  
#include <boost/spirit/home/x3.hpp>  
#include <boost/core/lightweight_test.hpp>  
  
#include <iostream>  
#include <iomanip>  
#include <chrono>  
#include <type_traits>  
  
namespace x3 = boost::spirit::x3;  
  
constexpr int N = 100000;  
  
template <typename Iterator, typename Parser>  
bool do_parse(Iterator& first, Iterator const& last, Parser const& parser)  
{  
#if BOOST_SPIRIT_X3_THROW_EXPECTATION_FAILURE  
    try  
    {  
        return x3::parse(first, last, parser);  
    }  
    catch (x3::expectation_failure<Iterator> const&)  
    {  
        // ...  
    }  
    return false;  
  
#else  
    x3::expectation_failure_optional<Iterator> failure;  
    return x3::parse(  
        first, last,  
        x3::with<x3::expectation_failure_tag>(std::ref(failure))[  
            parser  
        ]);  
#endif  
}  
  
template <typename Parser>  
int benchmark_main(std::string const& input, Parser const& parser)  
{  
    int n = 0;  
    auto const last = input.end();  
  
    for (int i = 0; i < N; ++i)  
    {  
        auto first = std::next(input.begin(), i);  
  
        if (do_parse(first, last, parser))  
        {  
            ++n;  
        }  
    }  
    return n;  
}  
  
  
int main(int argc, char* argv[])  
{  
    // use argc to avoid whole benchmark getting optimized away  
    std::string const input(N + argc - 1, 'a');  
  
    auto t0 = std::chrono::high_resolution_clock::now();  
    int n1 = benchmark_main(input, x3::lit('a') >> 'b');  
    auto t1 = std::chrono::high_resolution_clock::now();  
    int n2 = benchmark_main(input, x3::lit('a') > 'b');  
    auto t2 = std::chrono::high_resolution_clock::now();  
  
    using duration_type = std::chrono::duration<double, std::milli>;  
    auto const dur0 = std::chrono::duration_cast<duration_type>(t1 - t0);  
    auto const dur1 = std::chrono::duration_cast<duration_type>(t2 - t1);  
  
    std::cerr << "Test with sequence:    " << std::fixed << std::setprecision(3) << dur0.count() <<"ms\n";  
    std::cerr << "Test with expectation: " << std::fixed << std::setprecision(3) << dur1.count() <<"ms\n";  
    std::cerr << std::flush;  
    BOOST_TEST(n1 == n2);  
    BOOST_TEST(false); // for b2 to output messages  
    return boost::report_errors();  
}  
```  
</details>  
  
## Features  
- `#define BOOST_SPIRIT_X3_THROW_EXPECTATION_FAILURE`  
  - `1`:  **Default value.** This is the traditional behavior, throws `x3::expectation_failure` when `x3::expect[p]` fails.  
  - `0`: Non-throwing mode. The error will be stored into a `boost/std::optional` variable and can be retrieved by `x3::get<x3::expectation_failure_tag>(context)`.  
  
## Usage  
Documented as per [`doc/x3/tutorial/non_throwing_expectations.qbk`](https://github.com/boostorg/spirit/pull/788/files#diff-1564a5d3bde382db03cfebd1a3c42cf4eee11eca38fcdcabb04a9ee72eb07d17).  
  
### Throwing mode (aka traditional behavior)  
```cpp  
#include <boost/spirit/home/x3.hpp>  
  
// ...  
  
try {  
    bool const ok = x3::parse(first, last, parser);  
    if (!ok) {  
        // error handling  
    }  
  
} catch (x3::expectation_failure<Iterator> const& failure) {  
   // error handling  
}  
```  
  
### Non-throwing mode  
```cpp  
#define BOOST_SPIRIT_X3_THROW_EXPECTATION_FAILURE 0  
#include <boost/spirit/home/x3.hpp>  
  
// ...  
  
x3::expectation_failure_optional<Iterator> failure;  
bool const ok = x3::parse(first, last, x3::with<x3::expectation_failure_tag>(failure)[parser]);  
if (!ok) {  
    if (failure.has_value()) {  
        // error handling  
    }  
}  
```  
  
That's all. I believe that potentially all X3 users can experience a performance boost by just changing few lines of code in their application.  
  
## Implementation notes (for X3 developers)  
The idea of passing around `x3::expectation_failure` by `optional` comes from the original PR back in 2017. Therefore I have added the original author's name as well as mine to the license attribution, great thanks to the original author.  
  
That said, my new implementation is different than the reference, both in terms of semantics and code quality. My implementation is mimicking the internal behavior of throwing version, whereas the original PR was disposing `expectation_failure` context incorrectly in various locations. On my implementation, all iterators (i.e. `e.where()`) and source info (i.e. `e.which()`) should be identical to the values in throwing version.  
  
Quoting from original PR (https://github.com/boostorg/spirit/pull/242#issue-255275856):  
> 3. If expect operator is used and fails in skipper parser, the main parser should not fail (The skipper parser is a totally different parser with a different context. We should use it only to skip. If the skipper parser fails, it should mean no more skipping and should return to the main parser). We use nothrow expect as the default way to call skipper parser.  
  
This should not be the expected behavior.  
  
Usage of `x3::expect[p]` in skippers is legit X3 code and it should be supported. In such cases the library user must provide a context which can store the `x3::expectation_failure` value so that X3 can store the error in it. So then I carefully investigated all possible combinations of parser primitives and skippers; I have included my discoveries in unit tests.  
  
## Tests  
```console  
$ cd test/x3  
$ ..\..\..\..\b2 toolset=msvc cxxstd=17 cxxflags=/utf-8 -j24  
```  
All tests are passing, however, you should expect **Internal Compiler Errors** as usual. 🤓  
It's not the test case's issue so I can do nothing about it. Just do rebuild several times and you should be getting proper results.  
  
My environment is  
- Windows 11, 10.0.22631  
- Visual Studio 2022, 17.10.5  
- Core i9-14900KF  
- Antivirus turned off  
- Decent amount of memory  
- Decent amount of disk space  
  
I'd really appreciate it if you could test this PR on your environment.  
  
I've added ~100 testcases to make sure that the throwing/non-throwing versions both yield same result when `x3::expect` is involved. For this purpose, I have redesigned the whole test suite `test/x3/expect.ipp` so that b2 automatically runs all expect-related tests for both versions.  
  
  
## Documentation  
Documentation was initially marked as TODO but was later added to this PR.  
  
## Bonus  
Originally the runtime failures in `x3::expect[p]` were leading to tremendous amount of error logs on debuggers attached. It was sadly making my output window useless since it literally washes away every useful information including non-X3 errors. This PR solves this issue too.  
  
I have also confirmed that non-throwing mode performs 10% to 70% faster when debugger is attached.  
  
![X3-throwing-expect](https://github.com/user-attachments/assets/6cc42b43-f9d1-4ae9-865d-cfc392d4fccb)

---

## Comment 1

> Username: saki7  
> Created_at: 2024-08-11 16:19:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2282810483  

@djowel Could I ask you to review this PR?  
FYI, tests are failing since I used C++17 features (which I presume is valid since the README says so). It would be very nice if you could fix CI.

---

## Comment 2

> Username: djowel  
> Created_at: 2024-08-11 22:25:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2282908559  

> @djowel Could I ask you to review this PR? FYI, tests are failing since I used C++17 features (which I presume is valid since the README says so). It would be very nice if you could fix CI.  
  
Will do! Thanks so much for doing this! It seems to be very comprehensive and well thought!

---

## Comment 3

> Username: djowel  
> Created_at: 2024-08-11 22:40:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2282912328  

> While I'm used to writing comments in codes, I'm still experiencing a big language barrier on writing lengthy documentation. Although I'm thinking that I should be responsible for writing the docs, it would be very nice if a maintainer could support me 🙇🙇🙇  
  
I think your English is very good for a non-native speaker. Please note that I too am not a native English speaker, and neither is the current maintainer. I'd say just go ahead, and I'll do the review and copy editing. This PR will not be complete with proper documentation, since this is a major change.

---

## Review 4 [Commented]

> Username: djowel  
> Created_at: 2024-08-11 22:44:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/788#pullrequestreview-2231903838  

📁 include/boost/spirit/home/x3/operator/kleene.hpp

```diff
  35 |             while (detail::parse_into_container(
  36 |                 this->subject, first, last, context, rcontext, attr))
```

> Username: djowel  
> Created_at: 2024-08-11 22:42:41 UTC  
> Updated_at: 2024-08-11 22:44:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#discussion_r1713071907  

Shouldn't `parse_into_container` short circuit the loop on` !has_expectation_failure(context);` ? Or is it already done in this PR somewhere?

> Username: saki7  
> Created_at: 2024-08-12 03:52:21 UTC  
> Updated_at: 2024-08-12 09:28:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#discussion_r1713146654  

That's a good question; it does short circuit. There are 3 patterns here:  
  
1. subject parser succeeded (-> returned `true`)  
2. subject parser failed normally (-> returned `false`)  
3. subject parser failed with expectation failure (-> returned `false`)  
  
So the `while` statement does break when expectation failure is raised. All we need to do in `kleene` is to distinguish the difference between case 2 and 3, then return false if it was the latter.


---

## Comment 5

> Username: saki7  
> Created_at: 2024-08-12 04:37:33 UTC  
> Updated_at: 2024-08-12 08:39:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2283099312  

https://github.com/boostorg/spirit/actions/runs/10345812327/job/28633394485?pr=788#step:4:1243  
  
```  
gcc.compile.c++ ../../../../bin.v2/libs/spirit/test/x3/x3_expect_nothrow.test/gcc-11/release/x86_64/cxxstd-17-iso/threading-multi/visibility-hidden/expect_nothrow.o  
In file included from ../../../../boost/spirit/home/x3/directive.hpp:23,  
                 from ../../../../boost/spirit/home/x3.hpp:64,  
                 from expect.ipp:9,  
                 from expect_nothrow.cpp:8:  
../../../../boost/spirit/home/x3/directive/skip.hpp: In member function ‘bool boost::spirit::x3::skip_directive<Subject, Skipper>::parse(Iterator&, const Iterator&, const boost::spirit::x3::unused_type&, RContext&, Attribute&) const’:  
../../../../boost/spirit/home/x3/directive/skip.hpp:83:17: error: static assertion failed: In `skip(subject).parse(...)`: subject might raise an expectation failure, but there's no place to store the variable since the parent context type is unused_type. Perhaps you forgot: `x3::with<x3::expectation_failure_tag>(failure)[p]`  
   83 |                 false,  
      |                 ^~~~~  
../../../../boost/spirit/home/x3/directive/skip.hpp:89:9: error: no return statement in function returning non-void [-Werror=return-type]  
   89 |         }  
      |         ^  
```  
  
I have no idea why this error is occurring. This line is not even instantiated on Visual Studio 2022, both in C++17 and C++20 mode. I can't think of any other reason except that MSVC or GCC/Clang is being non standard conformant, because the compilation result should be consistent among any compilers even if my code is wrong.  
  
@djowel Could you do me a favor and bump GCC/Clang version on CI? I lost my old Linux installation since I just built a new PC and I also have other personal reasons that I can't spend much time on building Linux environment from scratch.

---

## Comment 6

> Username: djowel  
> Created_at: 2024-08-12 05:51:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2283156898  

> @djowel Could you do me a favor and bump GCC/Clang version on CI? I lost my old Linux installation since I just built a new PC and I also have other personal reasons that I can't spend much time on building Linux environment from scratch.  
  
Which version?

---

## Comment 7

> Username: saki7  
> Created_at: 2024-08-12 06:15:02 UTC  
> Updated_at: 2024-08-12 08:39:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2283180249  

@djowel The latest GCC/Clang versions available in GitHub Actions would be nice.  
  
I presume that the actual solution would not require the most recent compiler, but I just want to do sanity checks against most modern compilers for now. (I mean, even if it happens that we would need to write nasty compiler detection macros, I need an information whether it works on the most decent compiler in the first place.)  
  
BTW I just started writing docs.

---

## Comment 8

> Username: saki7  
> Created_at: 2024-08-12 06:28:02 UTC  
> Updated_at: 2024-08-12 08:39:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2283195241  

If there's an option to pull any version from external package repository, GCC 14 and Clang 18 is the best choice.

---

## Review 9 [Commented]

> Username: Kojoley  
> Created_at: 2024-08-12 06:31:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/788#pullrequestreview-2232117413  

📁 include/boost/spirit/home/x3/support/expectation.hpp

```diff
  82 |+         Iterator where_;
  83 |+         std::string which_;
  84 |+     };
```

> Username: Kojoley  
> Created_at: 2024-08-12 06:19:19 UTC  
> Updated_at: 2024-08-12 06:31:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#discussion_r1713213740  

This would obviously lead to ODR violations. Be careful with functions too. Better to use separate namespaces to be safe to not create any ODR violations.

> Username: saki7  
> Created_at: 2024-08-12 06:58:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#discussion_r1713252006  

While I understand the basics of ODR violation, I'm quite not sure what's the proper way to implement workaround in a production quality library code. Could you elaborate on how I should separate the namespaces?

> Username: djowel  
> Created_at: 2024-08-12 22:19:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#discussion_r1714425825  

> While I understand the basics of ODR violation, I'm quite not sure what's the proper way to implement workaround in a production quality library code. Could you elaborate on how I should separate the namespaces?  
  
Without sufficient context, I'm not sure what @Kojoley exactly is referring to here (also with functions). I'm guessing though that this can happen when both  branches of BOOST_SPIRIT_X3_THROW_EXPECTATION_FAILURE are potentially possible in different translation units. Could you please elaborate, @Kojoley ?

> Username: saki7  
> Created_at: 2024-08-13 11:14:09 UTC  
> Updated_at: 2024-08-13 11:14:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#discussion_r1715113444  

Fixed in https://github.com/boostorg/spirit/pull/788/commits/d9b53fedb0722e52be650576c20ddaf806273298


📁 include/boost/spirit/home/x3/directive/skip.hpp

```diff
  70 |-         template <typename Iterator, typename Context
  71 |-           , typename RContext, typename Attribute>
  74 |+         template <typename Iterator, typename RContext, typename Attribute>
```

> Username: Kojoley  
> Created_at: 2024-08-12 06:22:30 UTC  
> Updated_at: 2024-08-12 06:31:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#discussion_r1713215893  

You just removing `skip[]` directive for noexcept mode? I don't understand why.

> Username: saki7  
> Created_at: 2024-08-12 06:50:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#discussion_r1713244180  

My understanding is that `skip[]` is implemented in `reskip_directive::parse()`, not in this specialization.  
Instead this one is `skip_directive::parse()`, and this specialization is very specifically called when the parent context was `unused_type`. We need to treat it as a logic error when it's non-throwing mode, thus `static_assert(false)`.

> Username: saki7  
> Created_at: 2024-08-12 08:25:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#discussion_r1713349626  

This issue has been resolved in https://github.com/boostorg/spirit/compare/d63d3a4fbce11e1fb8d8c91a8b874e983000d437..88d9987167799e009427b8ec48154809d8db37dc


📁 include/boost/spirit/home/x3/core/skip_over.hpp

```diff
 151 | 
 152 |     template <typename Iterator, typename Context>
 153 |     inline void skip_over(
```

> Username: Kojoley  
> Created_at: 2024-08-12 06:27:21 UTC  
> Updated_at: 2024-08-12 06:31:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#discussion_r1713219586  

I guess throwing expectation failure inside a skipper and catching it in a rule does work but I don't think there any use for it. That's probably an omission that it's not catched here. Is it really need to propagate expectation failures outside from skippers?

> Username: saki7  
> Created_at: 2024-08-12 06:38:18 UTC  
> Updated_at: 2024-08-12 06:55:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#discussion_r1713231568  

Consider the case like below:  
  
<https://github.com/boostorg/spirit/pull/788/files#diff-098440cf78f4367f618d21a85375e53988bc19c9d193a26876e4fb40d81ff5f1R448-R451>  
  
```cpp  
TEST_FAILURE("a..b.z", lit('a') >> 'b', lit('.') > '.', {  
    BOOST_TEST(x.which() == "'.'"sv);  
    BOOST_TEST(x.where() == "z"sv);  
});  
```  
  
Traditionally in throwing mode, this kind of grammar led to an expectation_failure being thrown. To get the same `x.where()` and `x.which()` values in non-throwing mode, we need to propagate the failures.


---

## Comment 10

> Username: djowel  
> Created_at: 2024-08-12 06:35:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2283203436  

> @djowel The latest GCC/Clang versions available in GitHub Actions would be nice.  
  
I don't know what that is. I'll go with Clang 18 and GCC 13. One less the latest.  
   
> I presume that the actual solution would not require the most recent compiler, but I just want to do sanity checks against most modern compilers for now. (I mean, even if it happens that we would need to write nasty compiler detection macros, I need an information whether it works on the most decent compiler in the first place.)  
>   
> BTW I just started writing docs.  
  
Wonderful!

---

## Comment 11

> Username: djowel  
> Created_at: 2024-08-12 06:42:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2283211196  

> If there's an option to pull any version from external package repository, GCC 14 and Clang 18 is the best choice.  
  
Let's try GCC 13 and Clang 18 first.

---

## Comment 12

> Username: saki7  
> Created_at: 2024-08-12 07:30:31 UTC  
> Updated_at: 2024-08-12 14:19:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2283280076  

OK so it turns out that the error was [CWG2518](https://cplusplus.github.io/CWG/issues/2518.html). Historically `static_assert(false);` was ill-formed but resolution of CWG2518 allowed writing it inside an uninstantiated template. It has been published as a DR for C++23, only MSVC and newer GCC/Clang seems to apply it in C++17 mode. I guess I can find a workaround for this.

---

## Comment 13

> Username: saki7  
> Created_at: 2024-08-12 08:36:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2283396738  

https://github.com/boostorg/spirit/compare/d63d3a4fbce11e1fb8d8c91a8b874e983000d437..88d9987167799e009427b8ec48154809d8db37dc  
  
CWG2518 problem and some semantic issue has been resolved in the recent diff, and I see the tests are passing, Since the source of error is gone, this PR should compile on older GCC/Clang now.  
  
As far as I understand it, the only remaining to-dos regarding implementation is the ODR issue raised by Kojoley. I'm still not sure what exactly is the right code that I should write.

---

## Comment 14

> Username: saki7  
> Created_at: 2024-08-12 14:24:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2284130284  

Added documentation. I have configured my environment to build & preview quickbook locally, there are no syntax errors. (BTW, quickbook setup was so painful)

---

## Comment 15

> Username: saki7  
> Created_at: 2024-08-13 11:21:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2286003765  

@Kojoley @djowel I've fixed the ODR violation issue in the latest commit. What I've done is just wrapping throwing/non-throwing implementations in separate namespaces. I think inline namespace is the proper solution as described in [Stack Overflow](https://stackoverflow.com/a/11018418).  
  
I've also added a test case (previously failing) for mixing throwing/non-throwing modes in a single executable.  
  
Documentation is done in yesterday's commit.  
I believe all issues has been resolved, waiting for final review.

---

## Comment 16

> Username: djowel  
> Created_at: 2024-08-15 22:18:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2292369483  

It's looking good as far as I can tell. @Kojoley do you have any further thoughts and review?

---

## Comment 17

> Username: djowel  
> Created_at: 2024-08-15 22:21:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2292377078  

Thank you for working on the docs too! It's hard to review the docs, out of context though, without generating the html, which I haven't done in a long time already.

---

## Comment 18

> Username: djowel  
> Created_at: 2024-08-15 22:25:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2292393771  

Another quick thought, while reading your doc: should we [[deprecate...]] the throwing code with a message pointing to the docs?

---

## Comment 19

> Username: saki7  
> Created_at: 2024-08-17 08:47:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2294797272  

Thanks for your review!  
  
> Another quick thought, while reading your doc: should we [[deprecate...]] the throwing code with a message pointing to the docs?  
  
That's great. I was thinking of how to encourage people to use this feature, and that's going to be a good way to effectively informing the existing users. I think we can handle it in a separate PR.

---

## Comment 20

> Username: saki7  
> Created_at: 2024-08-17 13:58:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2294867748  

> It's hard to review the docs, out of context though, without generating the html, which I haven't done in a long time already.  
  
Here's the rendered result of the documentation:  
  
![_D__repos_boost_libs_spirit_doc_x3_html_spirit_x3_tutorials_non_throwing_expectations html_clip](https://github.com/user-attachments/assets/687d232e-290f-4dc7-b34d-4b741e8a84ba)

---

## Comment 21

> Username: djowel  
> Created_at: 2024-08-17 22:09:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2294999202  

> > It's hard to review the docs, out of context though, without generating the html, which I haven't done in a long time already.  
>   
> Here's the rendered result of the documentation:  
>   
[snip]  
  
Wonderful!

---

## Comment 22

> Username: djowel  
> Created_at: 2024-08-17 22:12:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2295000058  

Merged! Thank you so much for contributing this code! It is well thought and well implemented.

---

## Comment 23

> Username: saki7  
> Created_at: 2024-08-18 09:52:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/788#issuecomment-2295200437  

Thanks!

---
