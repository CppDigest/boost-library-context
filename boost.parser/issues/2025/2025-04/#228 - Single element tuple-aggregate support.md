# #228 - Single element tuple/aggregate support [Closed]

> Username: psiha  
> Created at: 2025-04-23 21:35:29 UTC  
> Updated at: 2025-05-05 11:55:29 UTC  
> Closed at: 2025-04-26 22:54:58 UTC  
> Url: https://github.com/boostorg/parser/issues/228  

This has been 'debated' many times for Spirit X3 so I guess that would show it is a recurring use case (wrapping simpler, often vocabulary types into user defined structs) which I hope you would consider supporting.  
  
Say I have something like  
```  
struct RollupCall {  
    using Arg = std::string_view;  
    using Args = std::vector<Arg>;  
    Args arguments;  
};  
using Atomic = std::variant<RollupCall, double>;  
```  
and  
```  
bp::rule<struct RollupRule, RollupCall> const rollup{ "rollup" };  
bp::rule<struct AtomicRule, Atomic> const atomic{ "atomic" };  
  
auto const rollup_def{ bp::lit("sum") >> '(' >> (bp::string_view[ bp::uint ] % ',') >> ')' };  
BOOST_PARSER_DEFINE_RULES( rollup );  
```  
this will fail to compile as it will not recognize RollupCall as a single element tuple into whose sole element/meber it can assign the implicit attribute of rollup_def.  
  
  
ps. in your cppcon talk you mention that a major part of your compile-time (and indeed I do notice bp to significantly, like several times, slower to compile than X3) goes for the automagic machinery that gets rid of the need for the Boost.Fusion macros, likewise [here](https://github.com/boostorg/parser/issues/33) you say that you looked at Boost.PFR that offers similar functionality and rather decided to copy only the necessary bits - so my question is finaly ;D - I also used PFR 'struct as tuple' functionality, including the cpp20 version with field name extraction (albeit in a much simpler context) and didn't notice a compile time hit - can you check if the PFR lib has somehow improved (its approach/implementation of the said trick) in the mean time - for example [it does not have the equivalent of BOOST_PARSER_MAX_AGGREGATE_SIZE](https://www.boost.org/doc/libs/master/doc/html/boost_pfr/limitations_and_configuration.html).

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-04-26 22:54:58 UTC  
> Url: https://github.com/boostorg/parser/issues/228#issuecomment-2832695923  

I think you just need to add a semantic action onto `rollup_def` that does this:  
  
```x++  
auto record_first = [](auto & ctx) { _val(ctx).arguments = std::move(_attr(ctx)); }  
```  
  
One of the reasons for rules is to give you control over situations exactly like this, where the generated attribute is frustratingly close to  -- but not the same as -- the one you want your parser to produce.  
  
> ps. in your cppcon talk you mention that a major part of your compile-time (and indeed I do notice bp to significantly, like several times, slower to compile than X3) goes for the automagic machinery that gets rid of the need for the Boost.Fusion macros, likewise [here](https://github.com/boostorg/parser/issues/33) you say that you looked at Boost.PFR that offers similar functionality and rather decided to copy only the necessary bits - so my question is finaly ;D - I also used PFR 'struct as tuple' functionality, including the cpp20 version with field name extraction (albeit in a much simpler context) and didn't notice a compile time hit - can you check if the PFR lib has somehow improved (its approach/implementation of the said trick) in the mean time - for example [it does not have the equivalent of BOOST_PARSER_MAX_AGGREGATE_SIZE](https://www.boost.org/doc/libs/master/doc/html/boost_pfr/limitations_and_configuration.html).  
  
This is a totally separate issue.  
  
1) I used the latest version of Boost.PFR code, and it has not changed since.  
2) Use of Boost.PFR is just one of the many automagical checks that slows things down.  
3) I can replace the Boost.PFR approach with a much faster direct approach using C++26 reflection when it becomes available.  
4) Boost.PFR does not try to destructure things the way that I am -- asking if the arity of a struct is compatible.  In order to do that, you have to try a lot of arities, hence the limit.

---

## Comment 2

> Username: psiha  
> Created at: 2025-05-05 11:55:28 UTC  
> Url: https://github.com/boostorg/parser/issues/228#issuecomment-2850757855  

> I think you just need to add a semantic action onto `rollup_def` that does this:  
>   
> ```  
> auto record_first = [](auto & ctx) { _val(ctx).arguments = std::move(_attr(ctx)); }  
> ```  
>   
> One of the reasons for rules is to give you control over situations exactly like this, where the generated attribute is frustratingly close to -- but not the same as -- the one you want your parser to produce.  
  
Right, but OTOH semantic actions are rightfully frowned upon so, for this 'recurring theme' ([example of a related X3 discussion](https://github.com/boostorg/spirit/issues/463)), it might make sense to have some sort of support that could eliminate SAs in this case (just 'thinking out loud'/leaving it out there as I realize it would probably increase the complexity of the library and its documentation/'mental footprint').  
  
  
  
> This is a totally separate issue.  
  
Yes, put it in a 'ps' to decrease the 'ticket spam' :)  
We can continue in a dedicated ticket if the discussion picks up...  
  
> 2. Use of Boost.PFR is just one of the many automagical checks that slows things down.  
  
Have you maybe checked w/ a compiletime profiler what are the main culprits ([e.g.](https://devblogs.microsoft.com/cppblog/templates-view-for-build-insights-in-visual-studio-2))?  
  
  
> 5. Boost.PFR does not try to destructure things the way that I am -- asking if the arity of a struct is compatible.  In order to do that, you have to try a lot of arities, hence the limit.  
  
Doesn't PFR offer a ready tuple_size metafunction? [And utilities for comparing 'compatible' structs out of the box](https://www.boost.org/doc/libs/develop/doc/html/reference_section_of_pfr.html#doxygen.reference_section_of_pfr.ops_8hpp)?
