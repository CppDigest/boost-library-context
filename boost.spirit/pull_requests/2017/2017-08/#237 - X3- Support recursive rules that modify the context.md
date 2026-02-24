# #237 X3: Support recursive rules that modify the context [Closed]

> Username: togermer  
> Created at: 2017-08-01 11:58:06 UTC  
> Updated at: 2025-05-09 14:37:37 UTC  
> Closed at: 2025-05-09 14:33:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/237  

make_context() always pushed a new node to the context list when the context is modified (e.g., for "with", "skip", or "no_skip" directives). If such directives are used in recursive rules, this leads to an infinite loop of template instantiations, effectively limiting the use of recursive rules in x3.  
  
To fix this, we first check if the tag for the new node is already contained in the context. If we find the tag, we remove the corresponding existing node from the context before pushing the new node with the requested tag.  
  
In order to remove a context entry, we have to rebuild all context nodes up to this entry. We can reuse (i.e. link to) the tail of the old context after this entry.  
  
In order to resolve life-time issues of newly created context nodes we added an aggregating implementation of struct context.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-08-02 02:30:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/237#issuecomment-319550700  

Nice catch! Seems you have good familiarity with X3!

---

## Comment 2

> Username: djowel  
> Created_at: 2017-08-02 02:32:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/237#issuecomment-319550992  

I assume all tests pass?

---

## Comment 3

> Username: togermer  
> Created_at: 2017-08-02 09:44:52 UTC  
> Updated_at: 2017-08-02 09:46:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/237#issuecomment-319623624  

Yes, all X3 tests pass (at least using msvc-14.1).

---

## Comment 4

> Username: cppljevans  
> Created_at: 2017-08-09 10:57:27 UTC  
> Updated_at: 2017-09-03 06:12:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/237#issuecomment-321222575  

What is the compile-time cost?  The paragraph in your 1st comment which starts with:  
  
   To fix this,   
  
and the following paragraph worry me that compile times will suffer.  Are there any  
benchmarks to measure compile times?  
  
Also, a test case demonstrating the problem, but simpler, if possible, than  
the one here:  
  
https://github.com/cppljevans/spirit/blob/get_rhs/workbench/x3/HanWang_make_context/main.orig.cpp  
  
would be helpful in guarding against the problem occurring again, and would give  
a concrete illustration of the problem.  
  
An even better test is:  
  
https://github.com/cppljevans/spirit/blob/get_rhs/workbench/x3/HanWang_make_context/main.altcomment.cpp  
  
It's better because it detects that the solution in the get_rhs fork when:  
  
  -DBOOST_SPIRIT_X3_EXPERIMENTAL_SKIP_MAKE_UNIQUE=1  
  
fails but with this pull_request, it succeeds.  
  
An even better test is:  
  
```  
namespace z3=boost::spirit::x3;  
namespace blockn_parser  
{  
    z3::rule<struct blk0_id> const blk0_ru;  
    z3::rule<struct blk1_id> const blk1_ru;  
    z3::rule<struct blk2_id> const blk2_ru;  
      
    auto const com0_def = z3::space;  
    auto const com1_def = z3::lit("/*1*/") | z3::space;  
    auto const com2_def = z3::lit("/*2*/") | z3::space;  
      
    auto const blk0_ru_def   
      = z3::lit("{0")   
      >> *( blk0_ru  
          |    z3::lit("skip1")  
            >> z3::skip(com1_def)[blk1_ru]  
          )   
      >> z3::lit("0}");  
    auto const blk1_ru_def   
      = z3::lit("{1")   
      >> *( blk1_ru  
          |    z3::lit("skip2")   
            >> z3::skip(com2_def)[blk2_ru]  
          )   
      >> z3::lit("1}");  
    auto const blk2_ru_def   
      = z3::lit("{2")   
      >> *( blk2_ru  
          |    z3::lit("skip0")   
            >> z3::skip(com0_def)[blk0_ru]  
          )   
      >> z3::lit("2}");  
        
    BOOST_SPIRIT_DEFINE  
    ( blk0_ru  
    , blk1_ru  
    , blk2_ru  
    )  
}//namespace blockn_parser  
```  
  
With this test, the get_rhs fork fails at compile time instead  
of at runtime; thus, this test could detect a flaw in a  
some future proposed alternative solution to this  
problem earlier.  
  
An even better (because it's shorter) test is:  
  
```c++  
    auto const com0 = z3::lit(":0") | z3::space;  
    z3::rule< lsti<0> > const lst0;  
    auto const lst0_def  
      =     z3::lit("a")   
         >>   
            (    z3::lit(",")  
              >> lst0  
            |    z3::lit(";0")   
              >> z3::skip(com0)[lst0]  
            | z3::lit(".")  
            )  
      ;  
    BOOST_SPIRIT_DEFINE  
    ( lst0  
    )  
```  
  
The following location for the test looks good to me:  
  
[https://github.com/boostorg/spirit/blob/develop/test/x3/skip.cpp#L36](url)  
  
What about that, Joel?

---

## Review 5 [Changes requested]

> Username: cppljevans  
> Created_at: 2017-09-06 09:00:16 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/spirit/pull/237#pullrequestreview-60845176  

Could you elaborate on those life-time issues?

---

## Comment 6

> Username: saki7  
> Created_at: 2025-05-09 14:33:21 UTC  
> Updated_at: 2025-05-09 14:37:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/237#issuecomment-2866781418  

This long-standing issue was effectively resolved sometime between 2020 and 2024. I actually encountered the recursive instantiation scenario while working on #788 and confirmed that the recursive rules are now correctly instantiated; without requiring any workarounds on user code.  
  
I carefully examined current behavior and documented the specification on the comments.  
  
https://github.com/boostorg/spirit/blob/328d940c372367a8d76a48ed956e870b56738ce9/include/boost/spirit/home/x3/core/skip_over.hpp#L74-L125

---
