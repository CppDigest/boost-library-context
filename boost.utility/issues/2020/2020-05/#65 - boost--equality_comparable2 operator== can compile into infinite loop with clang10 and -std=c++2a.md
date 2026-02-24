# #65 - boost::equality_comparable2 operator== can compile into infinite loop with clang10 and -std=c++2a [Open]

> Username: LumaGhost  
> Created at: 2020-05-05 19:21:46 UTC  
> Updated at: 2021-08-06 15:35:08 UTC  
> Url: https://github.com/boostorg/utility/issues/65  

```  
#include <boost/operators.hpp>  
  
struct my_type : boost::equality_comparable2<my_type, double> {  
    explicit my_type(double mem) : mem_{mem}{}  
    double mem_{0};  
    operator double() {  
        return mem_;  
    }  
    bool operator==(my_type l) {  
        return l.mem_ == mem_;  
    }  
};   
  
  
int main() {  
    my_type x{0};  
    return x == double{0};  
}  
```  
https://godbolt.org/z/zy9L4C  
  
expected: return 1  
  
actual: infinite loop  
  
this minimum example is based on the implementation of BOOST_STRONG_TYPEDEF in the boost serialization library. I originally opened an issue in the serialization library https://github.com/boostorg/serialization/issues/201 although it seems that the problem can be further narrowed down to `equality_comparable2`  
  
the issue only seems to happen on clang10, clang trunk, and gcc trunk with std=c++2a or std=c++20 flag.  clang10 and company produce the expected result when passed the std=c++17 flag instead of 2a. other versions (gcc9 and clang9)  produce the expected result even with the std=c++2a flag. I tried with boost 1.72 and 1.73 and they both produce the same behavior. as an additional note, the optimization level does seem to affect the behavior.  
  
let me know if any additional info is required. thank you!

---

## Comment 1

> Username: d-frey  
> Created at: 2020-05-05 20:20:47 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-624284891  

That's really strange. And it can be reduced even further, up to a point where it doesn't make any sense anymore... https://godbolt.org/z/F5coAY  
  
```c++  
#include <boost/operators.hpp>  
  
struct my_type : boost::equality_comparable2<my_type, int> {  
};   
  
int main() {  
    my_type x;  
    return x == double{0};  
}  
```  
  
GIven that C++2a support is AFAIK still experimental, I wonder if this should be a bug report against Clang.

---

## Comment 2

> Username: glenfe  
> Created at: 2020-05-05 20:38:35 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-624293320  

It's not Clang specific, you'll get the same thing on GCC trunk as well.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-05-05 20:46:36 UTC  
> Updated at: 2020-05-05 20:49:35 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-624296919  

I'm not very familiar with C++20 additions, but it looks like the compiler is now allowed to replace `x == y` with `y == x`, in which case the `operator==` defined here:  
  
https://github.com/boostorg/utility/blob/691f3238d7050aaf87b06c196a5e666e9ddf39bc/include/boost/operators.hpp#L161  
  
becomes infinite recursion. You can see it's a recursion in godbolt with `-O0`.

---

## Comment 4

> Username: d-frey  
> Created at: 2020-05-05 21:01:33 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-624304082  

Ah, got it. Yes, it's the space-ship operator causing it, but only because the original example is broken. If you derive from `boost::equality_comparable<my_type, double>` you *must* implement an `operator==` for `my_type` and `double`. Relying on implicit conversions doesn't work.  
  
Fixed example: https://godbolt.org/z/15IbkD

---

## Comment 5

> Username: d-frey  
> Created at: 2020-05-05 21:11:03 UTC  
> Updated at: 2020-05-05 21:12:35 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-624308338  

To elaborate: The documentation says that the user must provide `t == u`, then Boost.Operators will generate `u == t`, `t != u`, and `u != t`.  
  
The problem is that if `t == u` only works by relying on implicit conversions, the generation of `u == t` will now cause the new rules that the space-ship operator brought us to prefer the generated operator over the implicit conversions as it thinks it can simply swap the arguments. That creates the infinite loop.  
  
To break it, one needs to provide an implementation for `t == u` which is a better match and therefore prevents the loop.

---

## Comment 6

> Username: LumaGhost  
> Created at: 2020-05-05 21:14:09 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-624309801  

> If you derive from boost::equality_comparable<my_type, double> you must implement an operator== for my_type and double.  
  
...  
  
> To break it, one needs to provide an implementation for t == u which is a better match and therefore prevents the loop.  
  
understood. thank you everyone! 😸

---

## Comment 7

> Username: Lastique  
> Created at: 2020-05-05 21:26:11 UTC  
> Updated at: 2020-05-05 21:26:41 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-624315207  

One thing I'm not getting is that how `operator<=>` comes into play. I don't see it defined anywhere, and AFAIK the compiler should not implicitly generate one (the user has to opt-in by `=default` it). And I can't find where the standard allows the `x == y` -> `y == x` transform. So I'm not quite sure how the code in https://github.com/boostorg/utility/issues/65#issuecomment-624284891 managed to compile.

---

## Comment 8

> Username: d-frey  
> Created at: 2020-05-05 21:35:59 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-624319491  

The spaceship operator itself `<=>` is not needed here, but it's addition also brought new rules for `==`. It is nicely summarized here: https://brevzin.github.io/c++/2019/07/28/comparisons-cpp20/#summary-of-rules

---

## Comment 9

> Username: d-frey  
> Created at: 2020-05-05 21:38:54 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-624320693  

Side note: With the new rules, there is pretty much no point in deriving from `boost::equality_comparable<T,U>` anymore once you implemented the base operator for `t==u`. The language simply takes care of it :)

---

## Comment 10

> Username: Lastique  
> Created at: 2020-05-05 21:50:56 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-624325703  

Yes, thank you for the link. So they did add the new rules for the `x == y` -> `y == x` transform. But they are described as part of overload resolution ([over.match.oper]/3.4) instead of implicitly generating new operators.

---

## Comment 11

> Username: d-frey  
> Created at: 2020-05-05 22:03:50 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-624330925  

Yeah, they extend the candidate set for overloads including two special tie-breakers. But in the OPs example all this doesn't help, as the tie-breakers don't eliminate the candidates and the candidates are then a better match than the pre-C++20 overload that was chosen. This is unfortunate and breaks existing programs. Ping @foonathan

---

## Comment 12

> Username: foonathan  
> Created at: 2020-05-06 13:39:39 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-624655652  

Your assessment is correct. OP wants to compare `my_type` and `double`, The only `operator==` are `my_type` and `my_type` (in the class) and `double` and `my_type` (from the base class). To use `double` and `my_type` the compiler needs to swap args; to use `my_type` and `my_type` the compiler needs an implicit conversion to `double`. As such, the compiler picks the version that swaps. This in turn swaps back, and we have the same problem again.  
  
Yes, this is a breaking change, but the language rules themselves are sensible: you want to prefer the swap over conversion.  
  
There are two solutions here: First, you could argue that the code was always broken as it didn't honor the contract of `boost::equality_comparable2` - the implementation of `operator==(T, U)`. Second, you could change your implementation to make the class empty in C++20 - the new rules are sufficient anyway and it just so happens that then the original example works fine and has the same behavior as it did with C++17 (convert to double and compare doubles).

---

## Comment 13

> Username: d-frey  
> Created at: 2020-05-06 14:09:54 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-624672525  

It was more of an FYI / heads-up. I agree that both the new rules coming with C++20 as well as Boost.Operators are fine, one just needs to provide the correct primary operators. Making the Boost.Operator classes empty for C++20 also seems kind of superfluous, it's better to not derive from them at all - or if you fix the primary operators, it will still work. Thanks for checking, though.

---

## Comment 14

> Username: mglisse  
> Created at: 2020-05-16 21:26:52 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-629707856  

I believe that making equality_comparable* empty for C++20 is not superfluous. First, we often use combined classes like ordered_field_operators2, and replacing that with "everything but equality_comparable" is not nice. Second, even if I do provide an implementation of the mixed operator==, the compiler may still give an infinite loop:  
  
```c++  
#include<boost/operators.hpp>  
template<class T>struct A:boost::equality_comparable2<A<T>,int>{};  
template<class T>bool operator==(A<T>const&,int){return false;}  
int main(){3==A<short>();}  
```  
`#ifdef`ing out a few lines of code from boost operators seems worth the trouble to me, especially for users who need their code compatible with both C++17 (or before) and C++20 for a while.

---

## Comment 15

> Username: d-frey  
> Created at: 2020-05-17 09:46:16 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-629770919  

@mglisse Yes, that is a problem, but calling Boost.Operators "broken" is not really fair.  
  
Modifying Boost.Operators seems easy, but if users can't upgrade their Boost version the problem remains. And a lot of people will simply recompile with C++20 and might not even notice the problem as there is no warning, just different codegen in the background. Delivering an infinite loop into production is certainly not going to make them happy. And it might come up in other situations as well, without using Boost.Operators. I already have some candidates I need to check now :(  
  
Therefore, I wonder if this is a problem with C++20 or the compilers, code like the one you posted is perfectly reasonable and worked before. If C++20 (silently!) breaks it, well, I certainly wouldn't be happy about that!  
  
@foonathan What do you think, is this something that can/should be fixed in the standard? Or does the standard already supports this and the compilers need to be fixed? Note that the difference is the template, i.e.  
  
```cpp  
#include<boost/operators.hpp>  
template<class T>struct A:boost::equality_comparable2<A<T>,int>{};  
template<class T>bool operator==(A<T>const&,int){return false;}  
int main(){3==A<short>();}  
```  
  
doesn't work while the non-template version does:  
  
```cpp  
#include<boost/operators.hpp>  
struct A:boost::equality_comparable2<A,int>{};  
bool operator==(A const&,int){return false;}  
int main(){3==A();}  
```

---

## Comment 16

> Username: mglisse  
> Created at: 2020-05-17 10:02:30 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-629772664  

> calling Boost.Operators "broken" is not really fair.  
  
Sorry for the way I phrased it, I didn't mean it in an accusatory way, I just wanted, in that other project, to point out that our issue came from our use of boost.operators (so possible fixes are either in boost, in the way we use it, or stop using it).  
  
> as there is no warning  
  
g++ does give a nice warning about it, but if boost is installed in a system location, you need -Wsystem-headers to see it.  
  
> I wonder if this is a problem with C++20 or the compilers  
  
My first thought was the compiler and I filed https://gcc.gnu.org/bugzilla/show_bug.cgi?id=94141 but since I found out a second compiler was doing the same, C++20 seems more likely.

---

## Comment 17

> Username: foonathan  
> Created at: 2020-05-17 11:51:57 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-629784693  

The behavior seems to be correct.  
  
In  
```cpp  
#include<boost/operators.hpp>  
struct A:boost::equality_comparable2<A,int>{};  
bool operator==(A const&,int){return false;}  
int main(){3==A();}  
```  
  
the call to `3 == A()` finds `operator==(int, A const&)` from the base class, which looks for `A() == 3`. Candidates are the `operator==(int, A const&)` (1) itself and `operator==(A const&, int)` (2). (2) is a better match than (1) as (1) requires a rewrite, so it picks (2) and it works.  
  
In  
```cpp  
#include<boost/operators.hpp>  
template<class T>struct A:boost::equality_comparable2<A<T>,int>{};  
template<class T>bool operator==(A<T>const&,int){return false;}  
int main(){3==A<short>();}  
```  
the candidates are `operator==(int, A<T> const&)` (1), which notably is *not* a template, and `operator==<T>(A const<T>&, int)` (2), which is a template. http://eel.is/c++draft/over.match.best#2 prefers non-templates over templates more than it prefers non-rewritten operators over rewritten ones, so it rewrites the call and results in an infinite loop.  
  
I'm not sure whether it can be fixed in the standard, because the overload resolution rules are very fragile to change...  
  
But again, technically the user is at fault here. They promised to write an `operator==(A const<T>&, int)`, but instead it wrote a template.

---

## Comment 18

> Username: Lastique  
> Created at: 2020-05-17 12:03:19 UTC  
> Updated at: 2020-05-17 12:03:38 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-629786053  

> But again, technically the user is at fault here. They promised to write an operator==(A const<T>&, int), but instead it wrote a template.  
  
Technically, that is true, although for an uninitiated user it would look strange that the non-template version works and the template version silently doesn't. Personally, I wouldn't expect that.

---

## Comment 19

> Username: d-frey  
> Created at: 2020-05-17 12:07:34 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-629786648  

I don't think you can blame the user here, as the template operator is the canonical way for users to write the operator.  
  
For fixing the standard: The general approach should be "if no operator exists, provide one". Now, limiting what counts as "no operator exists" to a specific set of candidates seems wrong to me. It should, for backward-compatibility reasons, simply apply the existing(/old) rules and see if any operator matches. Only if *no* operator exists, it should provide a new alternative.

---

## Comment 20

> Username: mglisse  
> Created at: 2020-05-17 12:16:31 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-629787770  

> But again, technically the user is at fault here. They promised to write an `operator==(A const<T>&, int)`, but instead it wrote a template.  
  
Does the doc say that? I read  
> The requirements for the types used to instantiate the simple operator templates are specified in terms of expressions which must be valid and the expression's return type.  
  
and in the table  
> t == u.  
> Return convertible to bool.

---

## Comment 21

> Username: foonathan  
> Created at: 2020-05-17 12:31:23 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-629789504  

> For fixing the standard: The general approach should be "if no operator exists, provide one". Now, limiting what counts as "no operator exists" to a specific set of candidates seems wrong to me. It should, for backward-compatibility reasons, simply apply the existing(/old) rules and see if any operator matches. Only if no operator exists, it should provide a new alternative.  
  
This is not how overload resolution works. Overload resolution gathers a list of candidates and then ranks them to find the perfect match. In C++20, just more candidates are added with new ranking rules for those.

---

## Comment 22

> Username: d-frey  
> Created at: 2020-05-17 12:40:40 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-629790575  

I know how overload resolution works, but that won't solve the problem. If you want to add new operator candidates, you can either do it within the existing framework of overload resolution (which apparently can be quite tricky as we see) or as a separate step afterwards. The latter might at least provide a way to keep existing code from breaking. Yes, compiler writers will not like that. But how else can you assure backward-compatibility?  
  
But of course, I'm open to other suggestions. Can the existing approach be fixed? Because I think that the example from Marc is quite a compelling case that the current rules will break existing and reasonable code.

---

## Comment 23

> Username: foonathan  
> Created at: 2020-05-17 12:46:37 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-629791277  

> But of course, I'm open to other suggestions. Can the existing approach be fixed?  
  
I've asked around, but no reply yet. I don't think we can fundamentally change the way rewritten operator overload resolution works, but we might be able to give non-rewritten operators higher priority or add a rule that overload resolution for comparison operators will not find a rewritten recursive candidate.

---

## Comment 24

> Username: Quuxplusone  
> Created at: 2020-05-17 16:26:41 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-629824208  

> I don't think you can blame the user here, as the template operator is the canonical way for users to write the operator.  
  
IMO a non-friend is never the "canonical" way to write an operator. Here's the template version again for reference:  
  
    #include<boost/operators.hpp>  
    template<class T>struct A:boost::equality_comparable2<A<T>,int>{};  
    template<class T>bool operator==(A<T>const&,int){return false;}  
    int main(){3==A<short>();}  
  
And here's the hidden-friend version that _I claim_ is canonical best practice (in C++03 through C++17 at least; I'm not yet sure whether C++20 changes this best practice):  
  
    #include<boost/operators.hpp>  
    template<class T>struct A:boost::equality_comparable2<A<T>,int>{  
        friend bool operator==(A const&,int){return false;}  
    };  
    int main(){3==A<short>();}

---

## Comment 25

> Username: mglisse  
> Created at: 2020-05-17 16:36:25 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-629825523  

> And here's the hidden-friend version that _I claim_ is canonical best practice  
  
We have indeed been moving towards that, but when you have to support Visual Studio (and not just 2019 with `/permissive-`), hidden friends are not hidden, and they are thus way worse than the template.  
  
Anyway, making those 2 boost classes empty for C++20 doesn't seem like it should be that controversial, at best their content is useless, and at worst it hurts.

---

## Comment 26

> Username: d-frey  
> Created at: 2020-05-18 05:53:41 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-629961751  

Your claim might even be correct, but previously a lot of people where teaching decoupling, hence a free function (which wasn't a friend) was "preferable". I would suspect that there is a lot of code out there that looks like the original example you posted. Of course, now the friend function can serve as a work-around (unless your compiler makes things worse).  
  
Anyways, before changing Boost.Operators I'd like to give it some time to resolve the issue, if possible, on the language level. Let's wait for @foonathan to clarify the situation, changing Boost.Operators is still on the table afterwards...

---

## Comment 27

> Username: foonathan  
> Created at: 2020-05-29 16:47:02 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-636073901  

Just wanted to let you know that someone else raised the issue on the committee internal mailing lists and it seems that compiler vendors are already working together to change the rules.

---

## Comment 28

> Username: mglisse  
> Created at: 2020-05-29 17:16:25 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-636087483  

Indeed, after seeing the discussion, I now agree that we should **not** modify boost operators for now, it is best to wait until the situation has been clarified.

---

## Comment 29

> Username: glenfe  
> Created at: 2020-11-16 14:58:00 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-728115171  

I followed the discussion on the C++ committee reflector and while it was promising, we still have a problem for Boost users here. Users are using "-std=c++20" with Clang and GCC under the assumption that "20" is more stable than "2a". This issue results in a warning diagnostic but that goes unnoticed, and users discover these at runtime as infinite recursion.   
  
This was the case with Boost.Rational in C++20 mode as was reported on its GitHub and also on Reddit. This issue caused a similar infinite recursion in an equality operator ([Rational issue 43]( https://github.com/boostorg/rational/issues/43)) there. I implemented a fix in Rational for the upcoming 1.75.0 release ([Rational pull 45](https://github.com/boostorg/rational/pull/45)).  
  
We should do something in <boost/operators.hpp> for Boost 1.75.0 to prevent the above, whether it is disabling functionality in C++20 mode, or a workaround in C++20 mode, but something that doesn't let this go unnoticed until it hurts users at runtime.

---

## Comment 30

> Username: Lastique  
> Created at: 2020-11-29 22:13:41 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-735466274  

I tried to implement a PR disabling `operator==` and `operator!=` with reordered arguments, but it failed the tests on gcc 10 with multiple errors like this:  
  
```  
operators_test.cpp:715:37: error: return type of ‘{anonymous}::convertible_to_bool {anonymous}::Wrapped2<T, U>::operator==(U) const [with T = long int; U = long int]’ is not ‘bool’  
  715 |     BOOST_TEST( static_cast<bool>(2 == j) );  
```  
  
clang 11 also fails:  
  
```  
operators_test.cpp:715:37: error: return type '(anonymous namespace)::convertible_to_bool' of selected 'operator==' function for rewritten '==' comparison is not 'bool'  
    BOOST_TEST( static_cast<bool>(2 == j) );  
                                  ~ ^  ~  
```  
  
So it looks like the compilers require the operators to return `bool` in order to be able to reorder the arguments. I cannot find such a restriction in the standard, though. [over.match.oper]/3.4.4 only talks about reversing arguments and says nothing about the return type of the synthesized operator.

---

## Comment 31

> Username: mglisse  
> Created at: 2020-11-29 22:34:10 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-735468616  

> So it looks like the compilers require the operators to return `bool` in order to be able to reorder the arguments. I cannot find such a restriction in the standard, though. [over.match.oper]/3.4.4 only talks about reversing arguments and says nothing about the return type of the synthesized operator.  
  
Maybe this  
  
> If a rewritten operator== candidate is selected by overload resolution for an operator @, its return type shall  
be cv bool,  
  
(I am quite unhappy with the way they are restricting return types)

---

## Comment 32

> Username: Lastique  
> Created at: 2020-11-29 22:35:42 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-735468803  

If there is indeed a limitation in the standard then this new operator rewriting "feature" is so spectacularly broken that I cannot even tell.  
  
I tried to disable the operators based on the return type, but it doesn't work either as the compiler fails to find the `operator==` if it requires argument conversion.  
  
In any case, looks like the code will be broken in one way or another whether we remove the operators or not. At this point I'm inclined to declare C++20 unsupported with a big red warning in the 1.75 release notes.

---

## Comment 33

> Username: Lastique  
> Created at: 2020-11-29 22:39:59 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-735469295  

> If a rewritten operator== candidate is selected by overload resolution for an operator @, its return type shall  
be cv bool,  
  
The synthesized operator return type may be `bool`, and that would be sort of fine in this case because `convertible_to_bool` is convertible to `bool`. The problem is that it requires the original operator to have return type of exactly `bool`.  
  
To be clear, I do not find the restriction on the synthesized operator return type acceptable, even if that would allow the test code to compile. There is zero reason for this restriction - the rewriting should only affect argument order and nothing else.

---

## Comment 34

> Username: glenfe  
> Created at: 2020-11-30 00:12:41 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-735480921  

> If there is indeed a limitation in the standard then this new operator rewriting "feature" is so spectacularly broken that I cannot even tell.  
  
https://cdacamar.github.io/wg21papers/proposed/spaceship-dr.html sheds some light on some of what breaks at runtime, and some survey and estimation of the existing code impacted.

---

## Comment 35

> Username: Lastique  
> Created at: 2020-11-30 00:26:50 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-735483606  

IMHO, this feature is clearly half-baked. They should have removed it from C++20 and maybe include its better revision in a later standard.

---

## Comment 36

> Username: Lastique  
> Created at: 2021-04-12 15:07:09 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-817889748  

Is there a followup on this? In particular, is the resolution planned in the C++ standard? In compilers? And what is the course of action in Boost?

---

## Comment 37

> Username: EyeAndTea  
> Created at: 2021-08-06 12:06:04 UTC  
> Updated at: 2021-08-06 15:35:08 UTC  
> Url: https://github.com/boostorg/utility/issues/65#issuecomment-894214102  

I apologize for not adding much to the discussion, but I am happy to come across people talking about this.  
  
I am frustrated about the situation. I wish to add that in my own code and formulations for roughly 17 years, (a == b) does not imply (b == a). I wish this "make it easy" trend would stop because almost always it means that from some one, "we are doing it my way". The world is a complicated place, and doing something right in this world is indeed difficult. It takes years to put a building up, and hardly any time to take it down. Encourage discipline and humility, instead. And remind people that C++ is not a toy.
