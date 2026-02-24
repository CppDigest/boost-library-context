# #13 x3 new facade-based API [Merged]

> Username: jamboree  
> Created at: 2014-04-23 14:05:33 UTC  
> Updated at: 2014-07-20 21:59:57 UTC  
> Merged at: 2014-04-24 02:07:37 UTC  
> Closed at: 2014-04-24 02:07:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/13  

### The Plan For X3 (step-by-step)  
- **new facade API (parser/directive)** `< we are here`  
- inherited attributes for rule  
- locals directive  
- phoenix actors  
- eps/attr adaption  
- all directives adaption

---

## Comment 1

> Username: djowel  
> Created_at: 2014-04-24 02:07:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#issuecomment-41235777  

Looks good overall. I'll merge this for now, but I have a few minor things I'd like to discuss.

---

## Comment 2

> Username: vtnerd  
> Created_at: 2014-04-26 00:02:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12021143  

The parameter f was taken by l-value reference and then moved. Its a small issue because its being called in a spot where the value was copied anyway. Should this be a template argument + forwarded to prevent a possible refactor issue?

---

## Comment 3

> Username: djowel  
> Created_at: 2014-04-26 00:20:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12021484  

I'd prefer a more understandable struct tag here instead of mpl::int_<2> which is meaningless; especially if the usage spans multiple header files.

---

## Comment 4

> Username: djowel  
> Created_at: 2014-04-26 00:27:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12021551  

I'd prefer a more understandable struct tag here instead of mpl::int_ which is meaningless; especially if the usage spans multiple header files.

---

## Comment 5

> Username: vtnerd  
> Created_at: 2014-04-26 02:17:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12022384  

Wouldn't enable_if with is_transformed be more clear here? This comment also applies to everywhere ::yes or ::no is being used.

---

## Comment 6

> Username: vtnerd  
> Created_at: 2014-04-26 02:29:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12022434  

I _think_ the call to apply will fail on l-values.  
  
The call to apply is not deduced because the type comes from the struct. The struct type is not completely based on the deduction from eval since references are being removed. This may be the intent, but the call to std::forward on line 26 made me think otherwise.

---

## Comment 7

> Username: vtnerd  
> Created_at: 2014-04-26 02:29:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12022435  

Is remove_rvalue_reference needed? I think std::remove_reference would suffice, it removes l-value references and r-value references. http://www.cplusplus.com/reference/type_traits/remove_reference/

---

## Comment 8

> Username: jamboree  
> Created_at: 2014-04-26 05:23:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12023023  

Yes it's needed, I don't want to remove l-value reference.

---

## Comment 9

> Username: jamboree  
> Created_at: 2014-04-26 05:25:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12023030  

It's only used here, and it does have some meaning -- the arity that the action called.

---

## Comment 10

> Username: jamboree  
> Created_at: 2014-04-26 05:29:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12023115  

I think ::yes / ::no clearly explain the code, and it'll be more verbose written in enable_if.

---

## Comment 11

> Username: jamboree  
> Created_at: 2014-04-26 05:48:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12023173  

At least it was my intent, but think more about it, maybe we can just remove_reference.  
In fact, in the early version, I decided to store both T and T& in the args-pack, but then I changed to T only afterwards, so maybe we don't need to distinguish T or T& now...

---

## Comment 12

> Username: jamboree  
> Created_at: 2014-04-26 05:53:31 UTC  
> Updated_at: 2014-04-26 05:53:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12023185  

`eval_impl<T, Context>` will keep the reference if it's a l-value reference, that is, T is a reference type.

---

## Comment 13

> Username: jamboree  
> Created_at: 2014-04-26 06:22:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12023263  

I might need some suggestion: result_of_eval is used for instantiating the caller_traits of parser/directive -- should I keep the info that the type is value or reference, and let the user decide how to deal with it in caller_traits, or should I remove reference and const at the first place?

---

## Comment 14

> Username: vtnerd  
> Created_at: 2014-04-26 16:32:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12025113  

If eval is invoked using argument deduction, the type for T will not contain references (l or r value). The type for T will contain references if the arguments are explicitly defined. Tested in clang 3.3 and gcc 4.7:  
  
``` c++  
template<typename Type>  
struct Invoke  
{  
    static Type&& apply(Type&& value)  
    {  
        return std::forward<int>(value);  
    }      
};  
  
template<typename Type>  
Type eval(Type&& value)  
{  
    static_assert(std::is_reference<Type>::value, "not a reference");  
    return Invoke<Type>::apply(std::forward<Type>(value));  
}  
  
int main()  
{  
    const int i = eval<const int&>(1); // compiles  
    const int j = eval<int&&>(2);      // compiles  
    const int k = eval(3);             // fails static assert  
    int l = 4;  
  
    return i + j + k + eval(l);        // fails on call to apply  
}  
```

---

## Comment 15

> Username: vtnerd  
> Created_at: 2014-04-26 16:53:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12025196  

My first sentence in the last message was incorrect - which is obvious from clangs output in the example I gave. T does contain references when its an l-value as you stated. The call to apply is not deduced, which is the problem.

---

## Comment 16

> Username: jamboree  
> Created_at: 2014-04-26 17:40:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12025442  

I don't see any problem, you should change `std::forward<int>` to `std::forward<Type>` in your example.

---

## Comment 17

> Username: K-ballo  
> Created_at: 2014-04-26 23:36:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12026892  

This is really hard to read, have you considered a tag-based alternative?

---

## Comment 18

> Username: K-ballo  
> Created_at: 2014-04-26 23:38:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12026897  

`transform_params<...>::yes` doesn't say to me that this overload will be disabled. Our idiomatic use is the `enable_if` one.

---

## Comment 19

> Username: K-ballo  
> Created_at: 2014-04-26 23:39:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12026900  

What is this alias for?

---

## Comment 20

> Username: djowel  
> Created_at: 2014-04-26 23:39:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12026902  

I'm with k-ballo on this one. Makes my head hurts trying to understand this.

---

## Comment 21

> Username: K-ballo  
> Created_at: 2014-04-26 23:39:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12026904  

`mpl::identity<typename xxx::type>` looks redundant, is it needed?

---

## Comment 22

> Username: djowel  
> Created_at: 2014-04-26 23:42:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12026911  

I know it has some meaning. What I am trying to say is that without knowing the context, mpl::int_N> is meaningless. But to understand the context, you have to study the code first. Unlike for example, if it is something like detail::arity<N>, then that is meaningful when read as it is.

---

## Comment 23

> Username: djowel  
> Created_at: 2014-04-26 23:45:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12026923  

Agreed. let's be more consistent with our C++ idioms.

---

## Comment 24

> Username: K-ballo  
> Created_at: 2014-04-26 23:47:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12026933  

I would appreciate an explanation on what this class does and why is it needed

---

## Comment 25

> Username: K-ballo  
> Created_at: 2014-04-26 23:50:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12026948  

I think it would be better to avoid a monolithic header, and split this one into several ones. Please take a look at the existing convention for copyright and header guards.

---

## Comment 26

> Username: K-ballo  
> Created_at: 2014-04-26 23:51:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12026949  

Would you mind using `typename` here for consistency? (you do that already later in this same file)

---

## Comment 27

> Username: djowel  
> Created_at: 2014-04-26 23:53:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12026970  

Also, I'm guessing typename Enable = void ?

---

## Comment 28

> Username: K-ballo  
> Created_at: 2014-04-27 01:17:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12027167  

^ This redundant `;` generates a noisy warning.

---

## Comment 29

> Username: jamboree  
> Created_at: 2014-04-27 03:13:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12027409  

How about adding a comment? like `bool call_action(mpl::int_<2>/*arity*/, ...)`?

---

## Comment 30

> Username: jamboree  
> Created_at: 2014-04-27 03:14:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12027415  

You mean those mpl::int_? Would comment help?

---

## Comment 31

> Username: jamboree  
> Created_at: 2014-04-27 03:16:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12027425  

For the default `caller_traits` below to inherit.

---

## Comment 32

> Username: jamboree  
> Created_at: 2014-04-27 03:19:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12027429  

No, I'll change that.

---

## Comment 33

> Username: jamboree  
> Created_at: 2014-04-27 03:24:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12027443  

To help store T[N], or you can't do:  
  
```  
int a[2];  
tuple<int[2]> t(a);  
```

---

## Comment 34

> Username: jamboree  
> Created_at: 2014-04-27 03:27:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12027449  

It was separated, for simplicity I merge them into one, it's ok to split it.

---

## Comment 35

> Username: jamboree  
> Created_at: 2014-04-27 03:30:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12027456  

OK.

---

## Comment 36

> Username: jamboree  
> Created_at: 2014-04-27 03:31:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12027457  

fixed.

---

## Comment 37

> Username: vtnerd  
> Created_at: 2014-04-27 14:58:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12029500  

You are correct, I messed up in that example. The original line of code you wrote does work as-is. I thought l-values would bind to a `&&` as part of the template argument deduction process. I didn't think the expression `eval_impl<int&>::apply(int_value_)` would accept l-values since apply is not templated, and no deduction would be done. This case would fall under reference collapsing, and the behavior is definitely correct. Some of the resources discussing universal references focus on the deduction part, and mention reference collapsing tangentially. Examples are usually with a templated function, not with a templated class and a non-template member function. I've found some good resources on this subject, but I might have to go RTFM, and get it straight from the source.  
  
R-values seem to have amplified the difficulty of writing _correct_ generic code.

---

## Comment 38

> Username: K-ballo  
> Created_at: 2014-04-27 17:12:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12030075  

That wouldn't help. I happen to know that actions can possibly be invoked in three different ways, and `int_<2>` happens to be the first one with also happens to have two arguments: a context and an attribute. I don't think many other would get all that from "arity" and "2".

---

## Comment 39

> Username: K-ballo  
> Created_at: 2014-04-27 19:23:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12030771  

I mean the machinery that maps from `is_callable` with different signatures to different magic constants. I remember we had some drafts were this was selected by overload resolution, but it might not be the best approach either.  
  
Also, comments are whitespace.

---

## Comment 40

> Username: K-ballo  
> Created_at: 2014-04-27 19:26:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12030785  

That I can immediately see from the code, but still doesn't say me what the purpose of those traits is, why is this needed, how should traits be defined, how are they tested, etc.

---

## Comment 41

> Username: K-ballo  
> Created_at: 2014-04-27 19:26:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12030787  

Why is storing an array into a tuple needed, and when does this situation come up?

---

## Comment 42

> Username: jamboree  
> Created_at: 2014-04-28 01:19:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12032822  

Well, the `traits` / `caller_traits` are default just like `static bool const is_pass_through_unary = false;`. The logic is: if the test of `traits<Subject>::xxx` fails, it fallbacks to the default mechanism as defined in the traits/ directory, and if the test of `caller_traits<Subject, Ts...>::xxx` fails, it fallbacks to `traits<Subject>::xxx`, and so on...

---

## Comment 43

> Username: jamboree  
> Created_at: 2014-04-28 01:25:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12032880  

Please see the [attr-parser](https://github.com/boostorg/spirit/blob/x3-devel/include/boost/spirit/home/x3/auxiliary/attr.hpp), as you can see, it treats arrays specially, another example is char-parser, it treats "a" (i.e. char[2]) as a single char-string, so I must keep array, not just pointer (and for lifetime issues).

---

## Comment 44

> Username: jamboree  
> Created_at: 2014-04-28 01:57:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12033254  

What would you suggest then? Is nested-if-else really so hard to read?  
Note that I also take the advantage of integer, so I can use condition like `N > 0` not just plain tag-dispatching.

---

## Comment 45

> Username: jamboree  
> Created_at: 2014-04-28 02:05:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12033331  

BTW, my [wiki](https://github.com/jamboree/spirit/wiki/New-API#gerneralized-way-to-define-a-directive) page has some description about how they should be defined.

---

## Comment 46

> Username: vtnerd  
> Created_at: 2014-04-28 02:39:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12033689  

The problem that I think Joel and K-ballo are raising is one that I've never considered before. When writing a program (a non-crazy metaprogram), it is considered bad practice to pass around a series of integer values instead of an enumerated type because contextual information is lost. Most programmers are initially able to grasp what the numbers represent. But if integers were used in entirely different contexts, a programmer has to first determine the context for the particular integer, and then determine the meaning in that context.  
  
Everyone involved is likely able to read and understand this now, but what if spirit used `int_<N>` to represent other things as well? Anyone maintaining spirit would have to understand what `int_<N>` meant in that particular context, which can get really damn difficult to debug/understand with code that gets executed at compile and then runtime.  
  
Its likely with custom tags and mpl or_ statements the same behavior can be achieved. The advantage is that a future maintainer is easily able to know the context simply by reading the custom tag name.

---

## Comment 47

> Username: K-ballo  
> Created_at: 2014-04-28 03:06:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12033962  

After thinking about this for a while, I doubt there is a reason for an `action_category` at all. We should be able to handle all three variations transparently, and we shouldn't need to know which one will be called. Only a single point actually doing the low-level invocation would have to know about those details, but that should be its own self-contained machinery.  
  
I don't think any of the code below should have a need for `tag` nor `action_category`, instead it should merely call something like `detail::invoke_action(f, context, attribute)`.

---

## Comment 48

> Username: djowel  
> Created_at: 2014-04-28 03:10:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12034007  

Nods! I like that direction!

---

## Comment 49

> Username: K-ballo  
> Created_at: 2014-04-28 03:11:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12034024  

After reading the wiki (very informative!), I wonder whether there is a need at all for all this complex machinery. I would like the idea of a `directive` helper, but I would rather have it be `directive<Derived, Subject>` and set all the default typedefs that I can later "override" on my derived class. What is the benefit of all this complex machinery over a traditional CRTP approach?  
  
To clarify: I would rather have `directive` choose sensible defaults for me, and let me override them just as I would in any other parser.

---

## Comment 50

> Username: K-ballo  
> Created_at: 2014-04-28 03:35:52 UTC  
> Updated_at: 2014-04-28 03:36:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12034284  

Here is a proof-of-concept of what I had in mind, any thoughts?  
  
```  
// <x3/support/utility/priority_tag.hpp>  
namespace utility  
{  
    template <std::size_t I>  
    struct priority_tag : priority_tag<I - 1> {};  
  
    template <>  
    struct priority_tag<0> {};  
}  
  
// <x3/core/detail/invoke_action.hpp>  
namespace detail  
{  
    template <typename F, typename Context, typename Attribute,  
        typename Enable = typename std::enable_if<  
            is_callable<F(Context const&, Attribute&)>::value>::type>  
    void invoke_action(utility::priority_tag<2> const&,  
        F& f, Context const& context, Attribute& attribute)  
    {  
        f(context, attribute);  
    }  
  
    template <typename F, typename Context, typename Attribute,  
        typename Enable = typename std::enable_if<  
            is_callable<F(Attribute&)>::value>::type>  
    void invoke_action(utility::priority_tag<1> const&,  
        F& f, Context const& context, Attribute& attribute)  
    {  
        f(attribute);  
    } // this one should be split in mem-fn and non-mem-fn  
  
    template <typename F, typename Context, typename Attribute,  
        typename Enable = typename std::enable_if<  
            is_callable<F()>::value>::type>  
    void invoke_action(utility::priority_tag<0> const&,  
        F& f, Context const& context, Attribute& attribute)  
    {  
        f();  
    }  
  
    template <typename F, typename Context, typename Attribute>  
    void invoke_action(F&& f, Context const& context, Attribute& attribute)  
    {  
        return invoke_action(utility::priority_tag<3>{},  
            f, context, attribute);  
    }  
}  
```

---

## Comment 51

> Username: jamboree  
> Created_at: 2014-04-28 03:42:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12034350  

Note that `d[p]` and `d(arg)[p]` generate different parsers.  
Having `directive<Derived, Subject>` means you still need a directive-gen, and you have to handle its `operator()` manually, which is the main reason I proposed the facade-based API, which automatically builds the scaffold for you.

---

## Comment 52

> Username: jamboree  
> Created_at: 2014-04-28 04:02:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12034562  

Works for me :)

---

## Comment 53

> Username: K-ballo  
> Created_at: 2014-04-28 04:19:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12034717  

I wouldn't worry about having to write generators. After all, this approach wouldn't work for the `as<T>` directive, nor for directives that have a bunch of logic and members derived from the attributes and/or subject. I do worry a lot about lazy arguments and I think this approach is the right one for that, I only hope it could reconcile with usual constructors, members, etc... Let me give this some thought.  
  
On a side note, I noticed in one of your examples that an instance of the directive itself serves as the generator. Is that correct? I don't think that's a good approach, and I don't think it would be difficult to move that logic away from the parser into a nested `generator` member struct. Would it be feasible to do that while maintaining all the other goals?

---

## Comment 54

> Username: jamboree  
> Created_at: 2014-04-28 05:30:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12035395  

Actually my `directive` is a generator itself, **not** a parser, `d[p]` genereates `directive_parser<Directive, Subject>` while `d(arg)[p]` genereates `directive_parser<directive_caller<Directive, Args...>, Subject>`.  
  
With regard to `as<T>` directive, what's wrong with  
  
``` c++  
    template <typename T>  
    struct as : directive<as<T>>  
    {  
        typedef T attribute_type;  
        ...  
    };  
  
    as<std::string> const as_string{};  
```  
  
And which example are you referring to?

---

## Comment 55

> Username: jamboree  
> Created_at: 2014-04-28 09:44:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12040212  

Some problem, how would you dispatch "attr==unused, action wants attribute" like line 114 and how would you wrap `call_action` like line 104 in `parse_impl`?

---

## Comment 56

> Username: vtnerd  
> Created_at: 2014-04-28 12:32:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12044226  

Could there be an overload:  
  
``` c++  
void invoke_action(utility::priority_tag<1> const&,  
        F& f, Context const& context, unused)  
```  
  
that created the attribute like before? I think overload selection would catch `priority<1>` and `priority<2>` cases, so only one implementation would be needed. After the attribute is created it should go to `priority<1>` or `priority<2>` overload just like before.  
  
Also, is refactoring action necessary for this patch? Theres been a lot of discussion about the refactoring of a feature that I thought worked. Is there a feature being added that am I missing?

---

## Comment 57

> Username: jamboree  
> Created_at: 2014-04-28 12:54:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12044808  

the problem is that you have to ensure that F is callable with certain signature as well.  
  
I don't think the refactoring is necessary myself, but if anyone can come up of a better idea, I'm all ears.

---

## Comment 58

> Username: K-ballo  
> Created_at: 2014-04-28 15:39:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12052018  

I don't see what the problem would be, nor I see what the need is for separate parse implementations. I will try making the changes and see if I figure out what are the problems that you expect here. Do we have tests taking care of all this added _callables_ functionality?

---

## Comment 59

> Username: vtnerd  
> Created_at: 2014-04-28 23:53:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12074402  

What if std::array was used instead? Parsers would have to accept std::arrays instead of c-arrays, but I can't think of any negatives to that.

---

## Comment 60

> Username: jamboree  
> Created_at: 2014-04-29 01:08:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#discussion_r12076035  

You mean internally? no, std::array<T, N> doesn't have a ctor accept T[N].

---

## Comment 61

> Username: djowel  
> Created_at: 2014-04-30 22:25:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/13#issuecomment-41859122  

Tongari, there are so many issues that need to be resolved. I think it was premature for me to have merged this _huge_ pull request. I think it's best to start again, so I'm gonna have to revert this merge and go back to the previous state. What we really want are fine grained pull requests (small chunks of code) that can be inspected fully. Can we try again, please?  
  
I do think you have a splendid coding strategy and I'm very glad to have you in X3 development. It's just a matter of scrutinising everything as a matter of policy with the X3 developers.

---
