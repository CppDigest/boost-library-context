# #760 - `{qi,karma}::symbols` copy constructor does not make an independent copy [Closed]

> Username: jwwalker  
> Created at: 2023-04-04 16:31:24 UTC  
> Updated at: 2025-09-03 23:29:14 UTC  
> Closed at: 2025-09-03 23:29:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/760  

In Boost 1.82.0 beta 1, the following code  
  
```c++  
#include <boost/spirit/include/qi.hpp>  
#include <iostream>  
  
int main(int argc, const char * argv[])  
{  
	boost::spirit::qi::symbols<char, int>	one;  
	one.at("x") = 1;  
	one.at("y") = 2;  
  
	boost::spirit::qi::symbols<char, int>	two( one );  
	two.at("x") = 3;  
	  
	std::cout << "In one, x = " << one.at("x") <<  
		", y = " << one.at("y") << std::endl;  
	  
	return 0;  
}  
```  
  
outputs "In one, x = 3, y = 2".  I don't think anyone would expect a change to the copy to affect the original.  If I instead default-construct `two` and then do a copy assignment `two = one`, things behave as expected.

---

## Comment 1

> Username: Kojoley  
> Created at: 2023-04-04 21:39:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/760#issuecomment-1496637813  

It is might be in 'fix and find out' area because proto::deep_copy/BOOST_AUTO/qi::auto will start to do an actual copy then.  
  
A workaround:  
```cpp  
    boost::spirit::qi::symbols<char, int>    two;  
    two = one;  
```

---

## Comment 2

> Username: sehe  
> Created at: 2023-10-13 18:30:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/760#issuecomment-1761999927  

@Kojoley The copy assignment also doesn't make a deep copy. I answered a similar question Apr 6 2023 on the mailing list:  
  
> It's by design, likely to make it efficient and easy (without surprises!) to use symbols in parser expressions. In effect, they get "reference semantics" just like `qi::rule<>` does when embedded in other rules.  
>   
> You can easily deep-clone a symbol table if you need:  
>   
> ```c++  
>     template <typename Sym> Sym clone_syms(Sym const& i) {  
>         Sym o;  
>         i.for_each(o.add);  
>         return o;  
>     }  
> ```  
> Here's a live demo driving home the points with more examples: http://coliru.stacked-crooked.com/a/263d22bc47d63375  
  
For completeness here's that demo program with output:  
  
```c++  
#include <boost/spirit/include/qi.hpp>  
#include <iomanip>  
using namespace std::placeholders;  
  
template <typename Sym> Sym clone_syms(Sym const& i) {  
    Sym o;  
    i.for_each(o.add);  
    return o;  
}  
  
void print(std::string_view s, double v) { std::cout << '[' << quoted(s) << '=' << v << ']'; };  
  
int main() {  
    boost::spirit::qi::symbols<char, double> syms1;  
    syms1.add("x", 0.0);  
  
    auto syms2 = syms1;  
    auto syms3 = clone_syms(syms1);  
  
    syms2.at("x") = 99.5;  
    syms2.add("y", 88.5);  
  
    std::cout << "\nsyms1: "; syms1.for_each(print);  
    std::cout << "\nsyms2: "; syms2.for_each(print);  
    std::cout << "\nsyms3: "; syms3.for_each(print);  
}  
```  
  
Printing  
  
```  
syms1: ["x"=99.5]["y"=88.5]  
syms2: ["x"=99.5]["y"=88.5]  
syms3: ["x"=0]  
```

---

## Comment 3

> Username: saki7  
> Created at: 2025-05-09 23:01:13 UTC  
> Updated at: 2025-05-09 23:03:04 UTC  
> Url: https://github.com/boostorg/spirit/issues/760#issuecomment-2868030682  

The copy-assignment semantics `p1 = p2` had never been working as expected on Qi. That's why we always end up searching on StackOverflow and find sehe's post that shows the *correct* way to deep-copy the parsers (thanks for your contribution, btw.). `qi::symbols` is yet another example that requires some unintuitive way to deep-copy, as illustrated in above code.  
  
I don't think this can be *fixed* because it is actually the long-standing characteristics of Qi parsers. A documentation would be nice, but I'm not sure if we have to invest our time on Qi in 2025.   
  
Closing as stale, but feel free to reopen this if you're willing to propose alternative options.

---

## Comment 4

> Username: sehe  
> Created at: 2025-05-10 22:16:13 UTC  
> Updated at: 2025-05-11 01:37:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/760#issuecomment-2869177144  

@saki7   
  
> The copy-assignment semantics `p1 = p2` had never been working as expected on Qi. That's why we always end up searching on StackOverflow and find sehe's post that shows the _correct_ way to deep-copy the parsers (thanks for your contribution, btw.)  
  
You're very welcome :)  
  
I'd say the equivalence is not valid. Since:  
  
 1. The `deep_copy` solution refers to Proto domain expressions which *do* have the ([documented](https://www.boost.org/doc/libs/1_88_0/doc/html/proto/users_guide.html#boost_proto.users_guide.front_end.customizing_expressions_in_your_domain.per_domain_as_child.primer___literal_as_child__literal__vs___literal_as_expr__literal_)) reference semantics on _child nodes_ (but ONLY child proto expression nodes)  
 2. x3::symbols<> has the exact same problem, while it is not **even** a proto-expression in the first place. None of the (non-terminal) x3 parsers have the reference semantics  
  
Note, too, that the purpose of `proto::deep_copy` (aka `qi::copy`) **isn't**  to make an independent copy. Instead it make a *different type* of expression template with references removed. So further copies are naturally independent. Even if you manually clone `symbols<>` they will always stay sharing their implementation on copy.  
  
> I don't think this can be fixed because it is actually the long-standing characteristics of Qi parsers  
  
So, because of the arguments above, that reasoning doesn't hold water.  
  
You might "equivalently" argue that "it is actually long-standing copy-semantics of `symbols<>`". That's a dubious choice, precisely because you **correctly** argued that they will not normally be _copied_ into parsers anyways. That could easily be an argument for regular value semantics instead.  
  
In any case, either deleting the copy ctor/assignment or clearly documenting the surprising state sharing behavior seems in order. In particular because it's possible to get into thread-safety issues with different parsers sharing symbols instances.

---

## Comment 5

> Username: saki7  
> Created at: 2025-05-11 03:50:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/760#issuecomment-2869397245  

I guess I need to make my stance clear :)  
  
#### 1. All codes that are against the normal C++ semantics should be fixed.  
The API which exposes inconsistency or pushes people toward wrong usage is really, really bad.  
  
This is not limited to value semantics. It's also not limited to Qi or the Spirit framework; it applies to all code we write. Why? Because we write C++, and we must follow that rule for the sake of our sanity.  
  
If we consider only this point, then undoubtedly *all* parsers on Qi must be fixed.  
  
---  
  
#### 2. Fixing `qi::symbols` is easy, but fixing other primitives is not.  
Say your garage has 20 holes on the roof. Your family is keep complaining about leaky roof, and even if nobody is complaining, the holes shall be fixed for a good reason.  
  
In reality, the 20th hole (`qi::symbols`) is relatively easy to fix, but the other 19 holes are not. You explain that to your family, saying, "OK, I can fix the 20th hole using duct tape (i.e. deleting the copy ctors)." Your family replies, "Then just do it!"   
  
You try to explain further, "But we will be still having 19 holes, because they need a non-trivial fix in their implementation details..." your family instantly replies: "19 holes is clearly better than 20 holes, why don't you just fix it?"  
  
---  
  
#### 3. We must invest our time on X3.  
Submitting a hotfix for `qi::symbols` surely does reduce the number by 1. But it's going to be a difficult decision for me to whether accept the new inconsistent status where the `qi::symbols` is compatible with value semantics while other parser primitives are not.  
  
We have limited resources, and it's 2025. We should focus on improving X3. Other maintainers may think otherwise, but my personal stance is clearly this.  
  
I beg you, please just keep it dormant.

---

## Comment 6

> Username: sehe  
> Created at: 2025-05-11 14:18:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/760#issuecomment-2869895234  

Thanks for clarifying your stance. The clarification is very clear.   
  
Sadly it shows my comment apparently wasn't.  
  
> In reality, the 20th hole (qi::symbols) is relatively easy to fix, but the other 19 holes are not.   
  
 - It's not "the 20th hole". It's the one silent surprise that tools (ubsan/asan) won't flag.   
 - Also the "other 19 holes" aren't issues: _they_ were a design choice in Proto. At least referencing non-terminals in parser expressions by reference is *clearly* a required feature (otherwise recursion could never work in Qi, and there would be a lot of duplicated parser instantiation)  
  
> Submitting a hotfix for qi::symbols surely does reduce the number by 1.  
  
That's unfair, because it doesn't "leave the similar issues". It's _unrelated_ (see my previous comment).  
  
> But it's going to be a difficult decision for me to whether accept the new inconsistent status where the qi::symbols is compatible with value semantics while other parser primitives are not.  
  
It's **not** inconsistent. Parser expressions *still* behave exactly how they behave (including when they use `symbols`), but symbols don't have the surprising behavior of shared state.  
  
> We have limited resources, and it's 2025. We should focus on improving X3.  
  
Did you miss the fact that x3::symbols has the same issue? Like I said, that's  
has never even been a Proto Expression. Also x3 expressions do not hold  
children by reference! All this clearly highlights how this is a separate  
concern.  
  
Here's a [side-by-side live on Coliru](https://coliru.stacked-crooked.com/a/5e5b252a0952211a)  
  
```c++  
#include <boost/spirit/home/qi/string/symbols.hpp>  
#include <boost/spirit/home/x3/string/symbols.hpp>  
#include <iostream>  
using boost::core::demangle;  
  
// note how none of this code uses any parser expressions  
template <typename Sym> void run() {  
    std::cout << "Testing " << demangle(typeid(Sym).name()) << std::endl;  
    std::cout << " -> is Proto Expression? " << std::boolalpha << boost::proto::is_expr<Sym>::value  
              << std::endl;  
  
    Sym a;  
    a.name("keywords");  
    a.add("a", 1);  
    a.add("b", 2);  
    assert(a.at("b") == 2);  
    assert(!a.find("c"));  
  
    Sym b = a;  
    b.name("variables");  
    a.at("a") = 42;  
    a.add("c", 3);  
  
    // name is *not* shared  
    assert(a.name() == "keywords"); // not surprising here...  
    assert(b.name() == "variables");  
  
    // trie is shared...  
    assert(b.at("a") == 42); // surprse... Level 1  
    assert(b.at("b") == 2);  
    assert(b.at("c") == 3);  
  
    // adding a variable adds a keyword...  
    b.add("z", 4);  
    assert(a.at("z") == 4); // Level WTaF?!  
}  
  
int main() {  
    using namespace boost::spirit;  
    run<qi::symbols<char, int>>();  
    run<x3::symbols<int>>();  
}  
```  
  
And the output:  
  
```c++  
Testing boost::spirit::qi::symbols<char, int, boost::spirit::qi::tst<char, int>, boost::spirit::qi::tst_pass_through>  
 -> is Proto Expression? true  
Testing boost::spirit::x3::symbols_parser<boost::spirit::char_encoding::standard, int, boost::spirit::x3::tst<char, int> >  
 -> is Proto Expression? false  
```  
  
Two complete side-notes for this issue, but still addressing since you brought them up:  
  
 1. As a side note that reference semantics or ownership sharing *isn't* against the C++ language semantics. The only principles that apply here are [PitOfSuccess](https://blog.codinghorror.com/falling-into-the-pit-of-success/) and [PrincipleOfLeastSurprise](https://en.wikipedia.org/wiki/Principle_of_least_astonishment). They of course relate to expectations, which come from language and standard library, among others.  
 2. Even if you'd rate the Proto expression child node references as defects, they should be *easy* to fix in the library (just use `as_expr` instead of `as_child` in a few spots). I would *not* suggest you to do that because it risks changing behavior of existing code, and the impact analysis is not easy to do.  
  
## TL;DR  
  
I didn't particularly care one way or the other whether this is "fixed" or just  
documented. I **do** care that any choice is made on sound arguments, based on  
a correct undestanding of the library, as well as the issue reported.  
  
So. I dove in to see whether it would be easy to fix, at all.  
  
Ironically, it *is* for Qi. The proto expression is _explicitly_ using  
`proto::reference` so the behavior doesn't even break under `qi::copy`.  
  
Sadly, it is *not* for X3. Precisely because child expressions are not  
references anywhere in x3. This means that changing the behavior ends up  
changing the behavior for parsers that rely on modifiable symbol lookups. This  
is a frequent pattern e.g. for identifier lookup when user-defined variables  
can be added.  
  
> I suppose this problem, like any problem, could be solved by a layer of indirection (`symbols_parser` could _refer_ to an x3::symbols object, instead of being an alias for it).  
  
See my side-by-side qi/x3 patches with regression tests [Live On Compiler  
Explorer](https://godbolt.org/z/vKjGb7Gzq) (anti-bitrot  
[copy](http://stackoverflow-sehe.s3.amazonaws.com/25/05/11/e2ea71e5-cae2-4e0c-9fed-4dc7b5d0c3a1/project.zip))  
  
So I might propose a PR for the documentation to clarify the semantics of {x3,qi,karma}::symbols. I'll mull it over for a bit. Reopening just to assign it to myself.

---

## Comment 7

> Username: saki7  
> Created at: 2025-05-11 14:51:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/760#issuecomment-2869917711  

@sehe You’re the one who’s dealt with these non-trivial issues firsthand and has guided others on StackOverflow for years. If you feel strongly about it, I’m open to your PR.  
  
Please note that I'm not always quick to change my mind on issues like this. Remember, if we keep improving and patching the older edition, it becomes increasingly difficult to build a community-wide consensus to transition to X3.  
  
Over time, people may continue relying on Qi far longer than intended, which would ultimately make future decisions harder for the maintainers. _(Believe me, I tried.)_   
  
For that reason, I'm generally not in favor of merging major improvements into Qi, even if they seem reasonable to some. Adding documentations is ok though. Again, this is my own opinion, and other maintainers may think otherwise.

---

## Comment 8

> Username: saki7  
> Created at: 2025-09-03 23:29:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/760#issuecomment-3251121350  

Closing as stale. No PR was made since the last consensus.
