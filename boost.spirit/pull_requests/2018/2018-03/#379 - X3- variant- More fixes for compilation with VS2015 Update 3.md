# #379 X3: variant: More fixes for compilation with VS2015 Update 3 [Merged]

> Username: RandomInEqualities  
> Created at: 2018-03-18 11:08:47 UTC  
> Updated at: 2018-06-30 22:31:13 UTC  
> Merged at: 2018-06-30 22:31:12 UTC  
> Closed at: 2018-06-30 22:31:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/379  

This applies the fix in 6d6f40c3e5 to all BOOST_NOEXCEPT_IF statements in variant.hpp. I have trouble compiling a toy project with VS2015, as these noexcept if statements fails to compile.  
  
I saw the support for VS2015 in 6d6f40c3e5, and got really execited so I checked out the develop branch to test it, unfortunately it fails for me to compile a small project that I have here:  
  
https://gitlab.com/RandomInEqualities/Calculator  
  
It compiles with these changes. Is the changes ok to do?

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-03-18 11:28:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/379#issuecomment-373990752  

Hm, when I was fixing it I had come to conclusion that VS2015 has problem with rvalue references in template parameters of constexpr expressions in `noexcept` .  
  
> I this change ok?  
  
Yes, but it need tests since our tests suite did not catch the problem. It looks like `forward_ast` needs a fix too.

---

## Comment 2

> Username: RandomInEqualities  
> Created_at: 2018-03-18 11:45:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/379#issuecomment-373991812  

Year, I'll try to create a minimal example.

---

## Comment 3

> Username: RandomInEqualities  
> Created_at: 2018-03-18 12:25:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/379#issuecomment-373994460  

This doesn't compile:  
  
`namespace x3 = boost::spirit::x3;`  
`using A = x3::variant<x3::forward_ast<struct B>>;`  
`struct B : x3::variant<A> {};`  
  
It fails with this error: https://gist.github.com/RandomInEqualities/d45cc483cf359f3f28e9dec797b45f0a  
  
Thinking about it i'm wondering if this even makes sense? It's trying to inherit from a variant to itself.. In the small project it's a bit more indirect that this is happening. But forward_ast<struct B> is just a pointer to B, so I guess it should work even though it might not be the best code..  
  
Would it make sense to add a test with the above? Or insert it into the existing x3_variant.cpp test?

---

## Comment 4

> Username: Kojoley  
> Created_at: 2018-03-18 13:09:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/379#issuecomment-373997602  

> This doesn't compile:  
>  
> `namespace x3 = boost::spirit::x3;`  
> `using A = x3::variant<x3::forward_ast<struct B>>;`  
> `struct B : x3::variant<A> {};`  
  
I do not understand what are your trying to do here. Why you would want a variant of a single type? Nested variants are also a very strange thing,

---

## Comment 5

> Username: RandomInEqualities  
> Created_at: 2018-03-18 14:10:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/379#issuecomment-374002210  

I don't want a single type, it is the minimal example that does not compile.  
  
This is a more real minimal example:  
https://gist.github.com/RandomInEqualities/d0536f4f3ebaa383f29a5d5abed78f31  
  
In that the UnaryExpression that derives from x3::variant, is a way to parse either a double or  
somthing with a +/- in front of a name. I'm not so familiar with boost spirit, so maybe that   
is a bad way to do it.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2018-03-19 00:48:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/379#issuecomment-374072274  

Superseded by #380

---
