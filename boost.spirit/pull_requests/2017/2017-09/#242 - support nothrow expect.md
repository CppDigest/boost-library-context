# #242 support nothrow expect [Closed]

> Username: wanghan02  
> Created at: 2017-09-05 13:23:06 UTC  
> Updated at: 2024-08-17 22:10:41 UTC  
> Closed at: 2024-08-17 22:10:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/242  

Throwing expectation_failure is so much slower (around 1000 times with clang -O2 on my mac) than the equivalent parser with sequence operator. It will increase the parsing performance if we are giving the option to use expect operator without throwing exceptions yet still keep its semantics. It has the same speed in a simple straightforward parser with the equivalent parser using sequence operator. It becomes faster when the parser has alternatives.    
  
1. Allow user to use expect operator more aggressively without any performance loss brought by throwing expectation_failure. When nothrow expect is used, an expectation failure simply fails the whole parser without throwing expectation_failure.  
2. Expect operator throwing expectation_failure is still the default way to handle expectation failures. Users can inject a false as default value to your parser's context to enable nothrow expect.  
    `x3::parse(input.begin(), input.end(), x3::with<x3::expectation_failure_tag>(false)[...]);`  
3. If expect operator is used and fails in skipper parser, the main parser should not fail (The skipper parser is a totally different parser with a different context. We should use it only to skip. If the skipper parser fails, it should mean no more skipping and should return to the main parser). We use nothrow expect as the default way to call skipper parser.  
4. The default implementation injects a bool into the context and it carries the expectation failure information. Users can also overload handle_expectation_failure_impl and has_expectation_failure_impl functions with e.g. boost::optional to carry more information.  
5. Add unit test for nothrow expect to test all existing parsers.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-09-05 23:23:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-327330547  

What's the reason for using -O2 instead of -O3? Can you provide a benchmark test program that we can try that shows these numbers? How about other compilers, e.g. g++?

---

## Comment 2

> Username: djowel  
> Created_at: 2017-09-05 23:32:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-327331938  

Also, if you are going to just fail the parse anyway, what's the advantage of using a > b instead of just a >> b ?

---

## Comment 3

> Username: wanghan02  
> Created_at: 2017-09-06 09:17:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-327425667  

```  
#include <iostream>  
#include <cassert>  
#include <chrono>  
#include <boost/spirit/home/x3.hpp>  
  
namespace x3 = boost::spirit::x3;  
  
constexpr int len = 10;  
  
template<typename Iterator, typename Expr>  
bool parse_(Iterator& first, Iterator const& last, Expr const& expr) {  
    try {  
        return x3::parse(first, last, expr);  
    } catch (x3::expectation_failure<Iterator> const&) {}  
    return false;  
}  
  
template<typename Expr>  
int Test(std::string const& input, Expr const& expr) {  
    int n = 0;  
    auto const last = boost::end(input);  
    for(int i = 0; i < len; ++i) {  
        auto first = boost::begin(input) + i;  
        if(parse_(first, last, expr))  
            ++n;  
    }  
    return n;  
}  
  
  
int main() {  
    std::string const input(len, 'a');  
    auto t0 = std::chrono::high_resolution_clock::now();  
    int n1 = Test(input, x3::lit('a') >> 'b');  
    auto t1 = std::chrono::high_resolution_clock::now();  
    int n2 = Test(input, x3::lit('a') > 'b');  
    auto t2 = std::chrono::high_resolution_clock::now();  
      
    std::chrono::duration<int64_t,std::nano> dur0 = t1 - t0;  
    std::chrono::duration<int64_t,std::nano> dur1 = t2 - t1;  
	  
    std::cout<<"Test with sequence    takes "<<dur0.count()<<" nano seconds"<<std::endl;  
    std::cout<<"Test with expectation takes "<<dur1.count()<<" nano seconds"<<std::endl;  
    assert(n1 == n2);  
}  
```

---

## Comment 4

> Username: wanghan02  
> Created_at: 2017-09-06 09:29:13 UTC  
> Updated_at: 2017-09-06 19:23:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-327428807  

The example above is a simple case that will fail `len` times at the second part `lit('b')`. I tried it with different compilers and platforms.   
  
1. VC2017 with release mode: expect is around 200-300 times slower on my pc.  
2. clang with -O3: expect is around 500 times slower on my mac (1000 times slower if `len` is 100), 80-100 times slower on coliru.   
3. g++ with -O3: expect is around 80-100 times slower on coliru.   
  
http://coliru.stacked-crooked.com/a/e87a4d5490ee949e

---

## Comment 5

> Username: wanghan02  
> Created_at: 2017-09-06 10:12:51 UTC  
> Updated_at: 2017-09-06 17:47:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-327439621  

The advantage of expectation over sequence is when we have many alternatives. For example in a simple parser below,  
  
```  
x3::char_("afkp") > '1'  
|  
x3::char_("bglq") > '2'  
|  
x3::char_("chmr") > '3'  
|  
x3::char_("dins") > '4'  
|  
x3::char_("ejot") > '5';  
```  
if the first `'1'` fails, we don't have to try the other 4 branches at all. If the expectation failure doesn't throw, this parser will be faster than the one with sequence operator.   
  
We also found that in our practice, most of the sequence operators are actually expectations.   
  
Why are there so many failures in the code? We use spirit x3 to implement erase_all/replace_all and it performs better than the boost regex equivalents.

---

## Comment 6

> Username: djowel  
> Created_at: 2017-09-08 07:41:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-328026790  

Looks good to me. I'll need some time to try and study the example code.

---

## Comment 7

> Username: djowel  
> Created_at: 2017-09-08 07:59:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-328031225  

OK, the speed issue is really disconcerting. On one hand this fix is a step in the right direction. On the other hand, previous code using the throw can't take advantage of the "fix". For example, I rely on the exceptions for error handling, by catching the expectation errors in the grammar via error handlers. Is there a way to also change the X3 error handling scheme to not require any code changes with this fix?

---

## Comment 8

> Username: wanghan02  
> Created_at: 2017-09-18 16:37:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-330280538  

@djowel Sorry for the late reply.  
The error handling scheme in `guard` and `rule_parser::parse_rhs_main` can also been done using the proposed method (should inject a `boost::optional<expectation_failure<Iterator>>` instead of a `bool`). But before going any further maybe I will list the pros and cons of the proposals first.   
  
**1. throwing `expectation_failure`**  
- **pros:**  
- [ ] nothing to be changed.  
- [ ] easier to write user defined parsers  
- **cons:**  
- [ ] becomes slow when expectation_failure is thrown.   
  
**2. inject a `bool` to the context**  
- **pros:**  
- [ ] when expectation fails, it's as fast as sequence operator or even faster (when alternatives exist).  
- **cons:**  
- [ ] it dose not carry detailed failure information.   
- [ ] users have to be careful when writing user defined parser structs (when another parser's parse function is called inside the current parse function, the user should know how the expectation failure will change the parse result, e.g. optional operator)  
  
**3. inject a `boost::optional<expectation_failure<Iterator>>` to the context**  
- **pros:**  
- [ ] when expectation fails, it's a bit slower than sequence operator (due to filling info string to expectation_failure) but faster than throwing exceptions.   
- [ ] it carries detailed failure information for error handling.  
- **cons:**  
- [ ] users have to be careful when writing user defined parser structs (when another parser's parse function is called inside the current parse function, the user should know how the expectation failure will change the parse result, e.g. optional operator)  
  
I will put **option 2** (inject a `bool` to the context) into the **nice to have** category. It should be given as an option to the users but not the default one. Because in many places we care more about the processing speed, not the reason why the parser fails (e.g. replace_all/erase_all). We can still use the error handling scheme based on whether the expectation failure exists (without detailed information such as iterator and info string). The users who write user defined parser structs should review their parse function if they decide to use this option (it doesn't matter if they don't because this is not the default option given to them).   
  
Functionality-wise **option 1** and **option 3** are the same and **one of them should be the default option given to the users**. Both of them have full error handling capability. I will assume error handling based on full `expectation_failure` information is the reason to use these options (otherwise option 2 is a better choice). It then depends on what do we do in the error handling code. Option 3's advantage becomes nothing if we always print out the error information (because normally printing is even slower than throwing exceptions). But if some users don't print out the error information in error handling code, they can still benefit from option 3. All users who write user defined parser struct should review their parse function if option 3 is used as the default one given to users.   
  
The code change for option 2 and 3 in error handling (guard and parse_rhs_main) and parser structs (operators, directives) are mostly the same except for some overloaded functions for option 3.   
  
**To sum up**, option 2 is something nice to have. whether or not using option 3 instead of option 1 depends on what do we normally do in the error handling code and how many users need to review their user defined parser structs.

---

## Comment 9

> Username: djowel  
> Created_at: 2017-09-18 18:28:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-330314381  

Option 3 sounds good. I'm not sure I understand "users have to be careful when writing user defined parser structs"... could you elaborate a bit with an example, like the optional operator?

---

## Comment 10

> Username: wanghan02  
> Created_at: 2017-09-18 19:37:33 UTC  
> Updated_at: 2017-09-18 19:49:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-330332944  

For example we have a user defined ascii_no_case parser struct (in this case a user defined directive). In it's parse function it calls subject's parse function and simply return its result. It's safe for nothrow expect because the parse function will return false when expectation failure happens.   
  
But optional operator is different (take it as an example). The parse(_subject) function tries to match subject's parse function but will always return true even if the subject's parse function fails. Then we have to be careful and change the return statement to `return !has_expectation_failure(context);` instead of simply `return true;` because the parser should fail when expectation failure happens.   
  
So if we choose option 3 as the default option, the users who write their own parser structs (like ascii_no_case or more complex ones like optional operator) should carefully review these structs.   
  
```  
	template <typename Subject>  
	struct ascii_no_case_directive : unary_parser<Subject, ascii_no_case_directive<Subject>>  
	{  
		typedef unary_parser<Subject, ascii_no_case_directive<Subject> > base_type;  
		static bool const is_pass_through_unary = true;  
		static bool const handles_container = Subject::handles_container;  
		  
		ascii_no_case_directive(Subject const& subject)  
		  : base_type(subject) {}  
		  
		template <typename Iterator, typename Context  
		  , typename RContext, typename Attribute>  
		bool parse(Iterator& first, Iterator const& last  
		  , Context const& context, RContext& rcontext, Attribute& attr) const  
		{  
			return this->subject.parse(  
				first, last  
			  , make_context<no_case_tag>(ascii_no_case_compare_, context)  
			  , rcontext  
			  , attr);  
		}  
	};  
```

---

## Comment 11

> Username: djowel  
> Created_at: 2017-09-20 02:49:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-330730087  

OK, I like option 3. This one needs documentation though. Are you willing to go all the way and include documentation updates? Many thanks!

---

## Comment 12

> Username: wanghan02  
> Created_at: 2017-09-20 08:28:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-330782451  

Sure. I will change the code and do some profiling first.

---

## Comment 13

> Username: wanghan02  
> Created_at: 2017-10-22 17:23:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-338494129  

@djowel   
  
x3::expect doesn't throw expectation_failure anymore  
  
1. x3::expect directive and operator have the same semantics as before.  
2. When expect failure happens, the whole parser will not try any alternative paths just like before. It will simply fail without throwing any exceptions.  
3. x3::guard and ID::on_error can still get the expectation_failure struct when expect failure happens. The error handling scheme is also not changed. 'rethrow' means return false and keep expect failure information visible to the parent parser. 'fail' means return false but remove the expect failure information.  
4. Allow user to use expect operator more aggressively without any performance loss brought by throwing exceptions.  
5. If x3::expect is used and fails in skipper parser, the main parser should not fail (The skipper parser is a totally different parser with a different context. We should use it only to skip).  
6. Add unit test cases to test all existing parsers.  
7. Users who care more about the processing speed and always ignore the detailed expectation_failure information can accelerate your parser by injecting a 'false' into your parser/skipper using x3::with<x3::expectation_failure_tag>(false)[parser/skipper].  
8. Document has been updated.

---

## Comment 14

> Username: djowel  
> Created_at: 2017-10-22 21:19:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-338509958  

I might be missing something here. This is a major change and can definitely affect current users' code. I think the code should therefore be optional, using a preprocessor macro, with the default having the original exception behavior. We should not force this change on the users. With this option on, some parts of the API, such as 'rethrow', do not make sense any more, and perhaps should have a different behavior. We need to be very careful. I think we also need to discuss this over at the Spirit list.

---

## Comment 15

> Username: saki7  
> Created_at: 2024-08-09 04:26:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-2277129500  

I have observed huge performance loss (especially in Debug builds, up to x100 increased time) for my application too.  
  
Is there any chance that this solution would be merged into current codebase? If a maintainer could guarantee that this non-throwing approach is still valid today, then I can perhaps be confident to spend certain amount of time to submitting a follow-up PR which resolves conflicts.

---

## Comment 16

> Username: djowel  
> Created_at: 2024-08-09 05:30:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-2277180151  

> I have observed huge performance loss (especially in Debug builds, up to x100 increased time) for my application too.  
>   
> Is there any chance that this solution would be merged into current codebase? If a maintainer could guarantee that this non-throwing approach is still valid today, then I can perhaps be confident to spend certain amount of time to submitting a follow-up PR which resolves conflicts.  
  
I support this, as long as it follows my recommendations above (making code optional, and enabled using a preprocessor switch). We do not want to break any current user code.

---

## Comment 17

> Username: saki7  
> Created_at: 2024-08-09 06:10:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-2277217521  

@djowel I have one question regarding design decision for `rethrow`:  
  
> wanghan02 said:   
> 'rethrow' means return false and keep expect failure information visible to the parent parser.  
> 'fail' means return false but remove the expect failure information.  
  
> djowel said:  
> With this option on, some parts of the API, such as 'rethrow', do not make sense any more, and perhaps should have a different behavior. We need to be very careful. I think we also need to discuss this over at the Spirit list.  
  
I agree that `rethrow` will have totally different behavior. I can think of several approaches:  
  
- (a): In case of expectation failure, always return `false`. X3 does nothing else, and assume that the user will explicitly investigate the content of `x3::get<expectation_failure_tag>(context)`.   
  - This is the approach taken in the original PR.  
- (b): In case of expectation failure, just throw a brand new exception right there. That is , `throw *x3::get<expectation_failure_tag>(context);`  
  - Users can still *preliminarily* investigate the content of expectation failure object within  `ID().on_error(...)` then return `error_handler_result::fail`.  In that case, it is possible to completely eliminate runtime exceptions while still being able to investigate the expectation failure content.  
- (c): In addition to (b), add a macro to fallback to the behavior of (a). When this macro is on, compiler may (or may not) assume the function `parse_rhs_main` is implicitly noexcept, thus additional optimization could kick in.  
  
I'm afraid that (a) is semantically confusing, because the user won't get actual exception thrown despite the fact that user have already specified to 'rethrow'. Therefore I suggest (b) or (c) for the new PR. Do you have any thoughts on the interface?

---

## Comment 18

> Username: djowel  
> Created_at: 2024-08-09 10:52:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-2277682605  

> I have observed huge performance loss (especially in Debug builds, up to x100 increased time) for my application too.  
  
BTW, have you tried again with release builds?

---

## Comment 19

> Username: djowel  
> Created_at: 2024-08-09 11:10:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-2277710260  

> I'm afraid that (a) is semantically confusing, because the user won't get actual exception thrown despite the fact that user have already specified to 'rethrow'. Therefore I suggest (b) or (c) for the new PR. Do you have any thoughts on the interface?  
  
It's been a while. I have no further thoughts at the moment. Since this PR is so outdated now (2017!), I think it's best to flesh out the details in another PR. Thoughts? It's easier for me to think in terms of code.  
  
Again, my biggest concern is that any major code change can and will break existing code in surprising ways. Again, while I value the solutions provided here, I see no way to guarantee that backward compatibility will be preserved other than having a "Legacy" mode as default, and explicitly switch to this "No-Throw" mode via a preprocessor define, such as:  
  
```  
#define BOOST_SPIRIT_NO_THROW_EXPECT  
```  
  
And if this is not defined, the code should be exactly the same as it was before.

---

## Comment 20

> Username: saki7  
> Created_at: 2024-08-09 12:05:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/242#issuecomment-2277796461  

>> I have observed huge performance loss (especially in Debug builds, up to x100 increased time) for my application too.  
>  
> BTW, have you tried again with release builds?  
  
Yes. I'm building X3 everyday, both Debug and Release builds.  
In my application, Release build is always fast. When I switch to Debug build, it obviously gets super slow.   
  
I'm pretty much sure that the performance penalty comes from X3's expect directive, since it's throwing exceptions 100,000 times in my parser. I've also profiled my parser with Visual Studio 2022's debugger, and it clearly states that x3::expect is the 'hot path'.  
  
  
> I think it's best to flesh out the details in another PR. Thoughts? It's easier for me to think in terms of code.  
  
Indeed. I will post concrete code later.  
  
> #define BOOST_SPIRIT_NO_THROW_EXPECT  
  
Don't worry, I have same thoughts like you said. I take backward compatibility very seriously.

---
