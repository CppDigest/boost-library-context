# #89 - do_ leads to incosistency [Open]

> Username: xR3b0rn  
> Created at: 2019-12-15 14:19:06 UTC  
> Updated at: 2019-12-15 14:39:41 UTC  
> Url: https://github.com/boostorg/phoenix/issues/89  

Any unary lazy statement in `boost::phoenix` can be used like:  
```C++  
template <class I, class F>  
void call_lzay_func(const I& i, const F& f)  
{  
    std::vector<int> vec;  
    f[push_back(ref(vec), i)]();  
}  
int i;  
f(ref(i), for_(ref(i) = 0, ref(i) < 5, ref(i)++));  
```  
The only exception is the `do_` statement.  
  
When I have a function taking a unary `boost::phonenix` lazy statement (like you can see in the example), I have to make a falling distinctive to handle the `do_` specially.  
  
I can't see any benefit in this. In which this simply change would solve the problem, without changing the semantic:  
```C++  
do_  
[  
    cout << arg1 << ", "  
]  
.while_(arg1--),  
```  
to  
```C++  
do_(arg1--)  
[  
    cout << arg1 << ", "  
]  
```  
Isn't the current implementation incosistent in the manner of my example?

---

## Comment 1

> Username: xR3b0rn  
> Created at: 2019-12-15 14:36:08 UTC  
> Updated at: 2019-12-15 14:39:41 UTC  
> Url: https://github.com/boostorg/phoenix/issues/89#issuecomment-565815153  

A quick fix:  
```C++  
#pragma once  
  
#include <boost/phoenix/core/limits.hpp>  
#include <boost/phoenix/core/call.hpp>  
#include <boost/phoenix/core/expression.hpp>  
#include <boost/phoenix/core/meta_grammar.hpp>  
  
BOOST_PHOENIX_DEFINE_EXPRESSION(  
    (boost)(phoenix)(do__)  
  , (meta_grammar) // Cond  
    (meta_grammar) // Do  
)  
  
namespace boost { namespace phoenix  
{  
    struct do__eval  
    {  
        typedef void result_type;  
        template <typename Cond, typename Do, typename Context>  
        result_type operator()(Cond const& cond, Do const& do_it, Context const & ctx) const  
        {  
            do  
            {  
                boost::phoenix::eval(do_it, ctx);  
            } while (boost::phoenix::eval(cond, ctx));  
        }  
    };  
      
    template <typename Dummy>  
    struct default_actions::when<rule::do__, Dummy>  
        : call<do__eval, Dummy>  
    {};  
  
    template <typename Cond>  
    struct do__gen  
    {  
        do__gen(Cond const& cond_)  
			: cond(cond_)  
		{}  
  
        template <typename Do>  
        typename expression::do__<Cond, Do>::type const operator[](Do const& do_it) const  
        {  
            return expression::do__<Cond, Do>::make(cond, do_it);  
        }  
  
        const Cond& cond;  
    };  
  
    template <typename Cond>  
    inline const do__gen<Cond> do__(Cond const& cond)  
    {  
        return do__gen<Cond>(cond);  
    }  
}}  
```  
This code is introducing a new statment: `do__`  
  
The syntax for the statement is the same as for `while_` but the behaviour is like the behaviour of a normal `do`.
