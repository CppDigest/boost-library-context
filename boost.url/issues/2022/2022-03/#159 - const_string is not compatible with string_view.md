# #159 - const_string is not compatible with string_view [Closed]

> Username: krzysztof-i  
> Created at: 2022-03-30 12:18:59 UTC  
> Updated at: 2022-07-26 20:47:14 UTC  
> Closed at: 2022-07-26 20:47:14 UTC  
> Url: https://github.com/boostorg/url/issues/159  

```cpp  
const_string::  
const_string(  
    const_string const& other) noexcept  
    : string_view()  
{  
    if (is_small(other.size()))  
    {  
        // other is small,  
        // nothing to release  
        std::memcpy( data_.buf_,  
            other.data_.buf_, other.size());  
        static_cast<string_view&>(*this) =  
            string_view(data_.buf_, other.size());  
        return;  
    }  
    data_.p_ = other.data_.p_;  
    ++data_.p_->refs;  
    static_cast<string_view&>(  
        *this) = string_view(other);  
}  
```  
  
const_string inherits from boost::string_view and violates Liscov substitution principle. It is not possible to use const_string exactly like boost::string_view because in string_view data() always point to external memory address (other object), while in const_string may point to own member in which will be freed in destructor.  
  
Following statements are true for boost::string_view but they are false for const_string:  
std::string text = "test";  
  
1. boost::string_view(text).data() == text.data()  
2. boost::string_view(text).data() == boost::string_view(text).data()

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-03-30 14:30:06 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1083213016  

I agree, but all the alternatives are worse. If you think you can do better, show us here https://godbolt.org/  
  
But let me warn you... we spent weeks trying different schemes, and the current solution is the "cleanest dirty shirt."

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-03-30 15:01:31 UTC  
> Updated at: 2022-06-28 22:10:25 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1083251196  

Hi @krzysztof-i ,  
  
Thanks for noticing the issue. Yes. It is a problem that this is false:  
  
```cpp  
const_string(text).data() == text.data();  
const_string(text).data() == const_string(text).data();  
```  
  
In a _very_ limited sense, this seems fine because   
  
```cpp  
std::string(text).data() == text.data();  
std::string(text).data() == std::string(text).data();  
```  
  
is also false, but this is particularly problematic for const_string because const_string inherits from string_view without inheriting its behavior.   
  
In particular, const_string is a value type and string_view is a constant reference type. Some other known problems are the availability of string_view functions that lead to undefined behavior and warnings about slicing.  
  
Do you have any proposed alternative to inheritance? We've spent a long time discussing and comparing alternatives:  
  
1) Inheriting from `string_view`. The current solution.  
2) Replacing `const_string` with another reference type that lazily decodes the data. The `decoded_string` would allocate no memory and have a bidirectional iterator.  
3) Replacing `const_string` completely with `std::basic_string`, which would still allow any allocator.  
4) Solution 1, but implementing operator `string_view` for conversions instead of inheritance.  
5) A combination of the above  
  
Each of these alternatives has its own problems too. In #127, we included a before/after Tony table comparing inheritance and operator string_view. The biggest advantage of inheritance is it's a simple way to provide lots of implicit conversions with less code, while some of these implicit conversions might not be possible otherwise.

---

## Comment 3

> Username: krzysztof-i  
> Created at: 2022-04-01 12:42:15 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1085851774  

I can't promise anything but if I find some solution I will share it with you.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2022-06-27 16:25:51 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1167573248  

> Do you have any proposed alternative to inheritance?  
  
An alternative that would address what problem? What are the constraints?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-06-27 17:08:41 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1167626369  

`const_string` is supposed to behave as close to `string_view` as possible in terms of passing it around or returning it from functions, and being convertible to string_view and std::string.

---

## Comment 6

> Username: akrzemi1  
> Created at: 2022-06-27 17:52:55 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1167680218  

But why is const_string needed in Boost.URL?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-06-27 21:46:30 UTC  
> Updated at: 2022-06-27 21:46:59 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1167943523  

> But why is const_string needed in Boost.URL?  
  
Any Boost.URL API which returns a string with percent-decoding applied is going to return ` const_string`. For example, here is the documentation and signature for `url_view::fragment`:  
  
https://master.url.cpp.al/url/ref/boost__urls__url_view/fragment.html  
```  
template<  
    class Allocator =  
        std::allocator<char> >  
const_string  
url_view::  
fragment(  
    Allocator const& a = {} ) const;  
```  
  
The allocator is required because the function has to allocate memory to store the result. We explored alternative return types such as `std::basic_string< char, std::char_traits<char>, Allocator>`.  But this didn't work too well, because basic std strings with different allocator types cannot be compared: https://godbolt.org/z/MdWx5YoxW  
  
One of the design goals of the library is that it should be possible to create and modify URLs and perform percent-decoding without any dynamic allocations. This is made possible by using a stack based allocator in calls to functions that return percent-decoded strings (the docs seem to be a little bit up in the air):  
  
https://github.com/CPPAlliance/url/blob/4c2d8f71aa2b18bd6e3bc5f075127be8922b97eb/include/boost/url/static_pool.hpp#L31  
  
The type `const_string` was developed to provide a type-erased constant string suitable for functions that need to return percent-decoded strings using an Allocator. We tried a number of different approaches and the current design has the least number of drawbacks. We think it is better than `basic_string` with different allocator template parameters. It is not perfect but it seems as close as we can get.

---

## Comment 8

> Username: akrzemi1  
> Created at: 2022-06-28 12:45:53 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1168676142  

This describes a need for a type-erased allocator. But not necessarily for a const string.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2022-06-28 14:12:01 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1168780034  

Are you asking why it is const?

---

## Comment 10

> Username: akrzemi1  
> Created at: 2022-06-28 15:58:42 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1168908277  

Yes.

---

## Comment 11

> Username: akrzemi1  
> Created at: 2022-06-28 16:17:36 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1168929586  

Or not just something like std::pmr::string.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2022-06-28 16:27:51 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1168943985  

> Or not just something like std::pmr::string.  
  
Well `std::pmr::string` is not C++11 so that rules it out. But even so, this type suffers from the same problem that all `std::basic_string` with a custom allocator have: they can't be compared to regular strings.  
  
The reason the percent-decoding algorithms return const strings is twofold:  
  
1. I didn't want to repeat the enormous API of `basic_string`, but more importantly:  
  
2. Mutating strings which use the `static_pool_allocator` in Boost.URL (or any equivalent monotonic resource) is inefficient, since the old storage cannot be reclaimed. Users who modify such strings will quickly encounter an exception. Since a reallocation requires a memcpy anyway, better to return constant strings and let the caller copy it into a `std::string` with equal or higher capacity (depending on the anticipated mutation needs).  
  
The library is somewhat biased towards the use-case of returning percent-decoded strings without performing dynamic allocations. The returned type `const_string` reflects this bias.

---

## Comment 13

> Username: alandefreitas  
> Created at: 2022-06-28 22:10:33 UTC  
> Updated at: 2022-06-28 22:11:03 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1169333578  

> An alternative that would address what problem? What are the constraints?  
  
The library is focused on avoiding dynamic allocations, which is a problem for the functions that return the decoded strings. In this context, we ended up with `const_string`, which is basically a constant COW string implementation.   
  
This choice has three implications: 1) The COW still requires allocations but at least avoids extra allocations when making copies (although the extra allocations wouldn't be a problem in most loops anyway); 2) The constness removes the mutable member functions that might inadvertently require an extra copy, and 3) it inherits from `string_view` to allow implicit conversions similar to `string_view` and `string` (which is a problem because a `const_string` is simply not a `string_view`, as the example in the issue shows).  
  
> Or not just something like std::pmr::string.  
  
That's one of the options we have considered. I've listed them [here](https://github.com/CPPAlliance/url/issues/159#issuecomment-1083251196), where `std::pmr::string` would be option 3.

---

## Comment 14

> Username: akrzemi1  
> Created at: 2022-07-04 13:27:53 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1173823117  

> But this didn't work too well, because basic std strings with different allocator types cannot be compared  
  
Interesting. This looks like an oversight in the design of `std::basic_string`.

---

## Comment 15

> Username: akrzemi1  
> Created at: 2022-07-04 14:48:06 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1173903975  

I admit, I am not fully aware of all the constraints, but a type with four conversion operators seems to work:  
  
```c++  
struct const_string  
{  
    // all const string interface  
    // all comparisons  
    // all COW stuff  
  
    template <typename CH, typename TR, typename AL>  
    operator std::basic_string<CH, TR, AL> () const;  
  
    template <typename CH, typename TR>  
      operator std::basic_string_view<CH, TR> () const;  
  
    template <typename CH, typename TR>  
      operator boost::basic_string_view<CH, TR> () const;  
  
    template <typename CH>  
      operator boost::core::basic_string_view<CH> () const;  
};  
```  
  
https://godbolt.org/z/Ts97sn58a  
  
What am I missing?

---

## Comment 16

> Username: alandefreitas  
> Created at: 2022-07-04 14:57:59 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1173912632  

> a type with four conversion operators seems to work  
  
That's also one of the alternatives we have explored. It would be alternative 4 in this [list](https://github.com/CPPAlliance/url/issues/159#issuecomment-1083251196). The PR and the discussion with its drawbacks are [here](https://github.com/CPPAlliance/url/pull/127).

---

## Comment 17

> Username: akrzemi1  
> Created at: 2022-07-05 10:07:45 UTC  
> Updated at: 2022-07-05 11:39:11 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1174877437  

Thanks for the cross references. The discussion in https://github.com/CPPAlliance/url/pull/127 suggests that the choice between adding conversion operators and adding strange inheritance reflected the trade-off between the library authors having to write definitions of functions already present in string_view on the one hand, and offering users a potential UB and violating the Liskov Substitution Principle on the other (in favor of the latter). Or is there more to this decision?

---

## Comment 18

> Username: vinniefalco  
> Created at: 2022-07-05 13:59:29 UTC  
> Updated at: 2022-07-05 14:00:02 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1175095384  

> the library authors having to write definitions of functions already present in string_view  
  
Oh I dont ever want to create _yet another string view_ type.. we already have three. One in `std` and two in Boost. I rather use an actual `string_view` with the weird inheritance than add a fourth... This does seem to point to fundamental flaws in how C++ has designed strings.

---

## Comment 19

> Username: akrzemi1  
> Created at: 2022-07-05 15:42:42 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1175208809  

I fully agree with keeping `string_view` types count to minimum. I like the idea of having `const_string`. My remark is about the design choice to inherit from `core::string_view` rather than offering conversion operators.

---

## Comment 20

> Username: alandefreitas  
> Created at: 2022-07-05 15:56:40 UTC  
> Updated at: 2022-07-05 16:01:09 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1175222715  

> reflected the trade-off between the library authors having to write definitions of functions already present in string_view on the one hand, and offering users a potential UB and violating the Liskov Substitution Principle on the other (in favor of the latter). Or is there more to this decision?  
  
That sounds correct. In particular, according to Vinnie's comment, I think avoiding another string type has precedence over these other issues in this decision.   
  
It's important to note `const_string` would be another string type. Not another string view type. The fact that a `const_string` is not a `string_view` is what causes the whole problem here. Besides some other smaller problems, like derivation from a concrete type.  
  
On the other hand, a "constant copy-on-write string" _is_ another string type, like it or not. And a very special one. So this ship has sailed. I guess the real constraints are more like having another constant COW string type without _fully_ implementing another string type. Or something like that.

---

## Comment 21

> Username: akrzemi1  
> Created at: 2022-07-05 16:04:12 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1175230004  

I am confused now. Are you saying that the sole fact of having the type `const_string` is a bad solution?

---

## Comment 22

> Username: vinniefalco  
> Created at: 2022-07-05 16:08:28 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1175234291  

Yeah I didn't quite understand that Alan. We definitely need a "non-modfiable, owning string"with a type-erased allocator." I am certain of this, because there is the need for a vocabulary type that can hold strings allocated using a stack allocator. Just to keep things interesting, a benefit of using our own type instead of say, `std::basic_string` with a custom allocator, is that we can be _very_ generous with the small buffer optimization. This will give users reasonably good performance for the average case even when using a dynamic allocator (which is the default, and the easiest way to use APIs which return `const_string`).

---

## Comment 23

> Username: alandefreitas  
> Created at: 2022-07-05 16:18:11 UTC  
> Updated at: 2022-07-05 19:21:38 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1175243600  

> We definitely need a "non-modfiable, owning string"with a type-erased allocator."   
  
I agree 100%. Whatever the decoded functions return, `std::basic_string` looks like a bad solution.  
  
> Are you saying that the sole fact of having the type const_string is a bad solution?  
  
I'm just trying to synthesize the constraints in my previous comment. But I think your question embeds a good point: we have two contradictory goals in this thread: "not ever creating another string type" and "creating another string type called `const_string`".   
  
It's impossible to not get rid of one of these goals. And considering Vinnie's [latest comment](https://github.com/CPPAlliance/url/issues/159#issuecomment-1175234291), we should probably get rid of "not ever creating another string type" because that's what `const_string` is and we definitely need a "non-modifiable, owning string with a type-erased allocator.".  
  
To be honest, considering the encoded version of the functions return string views (proving reference types are not really a problem here), I think the ultimate constraint should be more like "something efficient to represent decoded strings considering the application" instead of "non-modifiable, owning string with a type-erased allocator.". In other words, I wouldn't mind if the decoded functions _also_ returned reference types, like a `decoded_string_view` with bidirectional iterators or something.   
  
If there's no simple solution to this problem, there could be some kind of customization where the user would choose how to return the decoded strings. But I have no idea what that would look like.

---

## Comment 24

> Username: vinniefalco  
> Created at: 2022-07-05 18:41:56 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1175375941  

Maybe we should revisit `std::basic_string`

---

## Comment 25

> Username: akrzemi1  
> Created at: 2022-07-05 20:19:51 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1175462309  

> Any Boost.URL API which returns a string with percent-decoding applied is going to return `const_string`.  
  
Is there a small program that would demonstrate a real-life use case for this interface? Maybe this would suggest a solution.

---

## Comment 26

> Username: alandefreitas  
> Created at: 2022-07-05 20:29:25 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1175469391  

> Is there a small program that would demonstrate a real-life use case for this interface?  
  
I think the most common use case is the same as for the encoded params:  
  
```cpp  
urls::string_view s = "https://www.boost.org/doc/libs";  
urls::url_view    u = urls::parse_uri(s).value();  
std::cout << u.encoded_segments().size() << " segments\n";  
for (string_view seg: u.encoded_segments())  
{  
    std::cout << "segment: " << seg << "\n";  
}  
for (const_string seg: u.segments())  
{  
    std::cout << "segment: " << seg << "\n";  
}  
```  
  
, which is why I think a reference type would be ok for both cases, since `encoded_<component>` and `<component>` are the same 99% of the time. (No, this 99% percentage is not scientific, but you can look at your address bar right now, experiment a little, and you'll see they rarely diverge.)

---

## Comment 27

> Username: vinniefalco  
> Created at: 2022-07-05 20:52:50 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1175486796  

> which is why I think a reference type would be ok for both cases, since encoded_<component> and <component> are the same 99% of the time.  
  
This makes no sense at all. A "reference type" for decoded strings that only works when there are no escaped characters? What happens when there's an escaped character? And how do you know whether or not there's an escaped character other than scanning the entire string? But if we're scanning the entire string, and we have to deal with escapes.... then we might as well perform the decoding as we go... bringing us to the current implementation.

---

## Comment 28

> Username: vinniefalco  
> Created at: 2022-07-05 20:54:10 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1175487681  

> Is there a small program that would demonstrate a real-life use case for this interface  
  
Yes. Consider the case where the program needs to construct a `filesystem::path` formed by starting with the base directory, and then appending each decoded segment from the URL as a path component.

---

## Comment 29

> Username: alandefreitas  
> Created at: 2022-07-05 21:00:49 UTC  
> Updated at: 2022-07-05 21:01:29 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1175494290  

> that only works when there are no escaped characters?  
  
No. Because the iterator would be bidirectional. This has been working for utf-8 strings for a long time.  
  
> bringing us to the current implementation  
  
Yes. At best, there would be no copies. At worst, we would have the current implementation.

---

## Comment 30

> Username: vinniefalco  
> Created at: 2022-07-05 21:17:53 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1175507473  

You are talking about returning a lazy range. This is not a string (since it is not laid out contiguously).

---

## Comment 31

> Username: alandefreitas  
> Created at: 2022-07-05 23:03:10 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1175585470  

We had a parallel discussion about this. It might be that this issue is not really a big problem. I'll replicate some of the most relevant comments that came up there:  
  
- That's not what the LSP means. If a type `T` behaves like type `U` in all respects, the types are identical.  
    - The LSP says nothing about `boost::string_view(text).data() == text.data()`. The LSP applies to instances of the type, not to the type itself.  
    - LSP says, if I have a function `f(Base& x)`, which uses the defined interface of `Base`, if I pass this function an instance of Derived, it should work.  
    - The above equation has nothing to do with the LSP. You can also say `sizeof(string_view) == 16`, and this will fail for any derived.  
- LSP isn't particularly applicable to `string_view` because `string_view`s are generally passed by value  
    - LSP is usually about pass by reference  
- There's not much difference in practical terms between `string` being convertible to `string_view`, and `const_string` being derived from `string_view`  
    - The behavior in typical code is the same  
- Nonetheless, a conversion and duplicating the interface would be helpful to avoid all these types of problems and discussions. Some cons are:  
    - It's more work and complexity to the code  
    - It feels wrong to duplicate an entire std type  
  
> If I pass this function an instance of `Derived`, it should work  
  
So I guess this would only be a problem if we can prove a function could take a `std::string_view` by reference and be counting on `boost::string_view(text).data() == text.data()` to be correct?   
  
And these comments are about LSP. I'm not sure about UB. Is the implicit assumption here that violating LSP is always UB?

---

## Comment 32

> Username: akrzemi1  
> Created at: 2022-07-06 13:18:19 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1176212232  

> And these comments are about LSP. I'm not sure about UB. Is the implicit assumption here that violating LSP is always UB?  
  
You are introducing an unprecedented restriction: the user is expected never to bind `const_string` to `core::string_view&`. We tend to think that users never use `core::string_view&`, but how do we know?  
  
 1. The user may be using `T&` in a generic context and may not be aware that `T` happens to be `string_view`.  
 2. The user may be implementing a clever hack (much like yours is a clever hack) and the user's hack may be incompatible with yours.  
  
Violating LSP is not an UB per se. A class definition is practically never an UB. But you are making a class that is *error prone*: innocent usages compile but have surprising behavior -- likely unintended by the user. A bug then can manifest itself as UB.

---

## Comment 33

> Username: alandefreitas  
> Created at: 2022-07-06 16:09:13 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1176411616  

> You are introducing an unprecedented restriction  
  
I agree. We can't assume `core::string_view&` is never going to be used even if it's a bad idea to use it. I'm just replicating the most common comments that came up, but my interpretation was that the points about LSP not being applicable here are the important ones, while the comment about string_views _generally_ passed by value is just the cherry on top of the cake.   
  
I'm still not sure `boost::string_view(text).data() == text.data()` really demonstrates a problem here. LSP is a semantic relation between types, so the example `sizeof(const_string) == sizeof(string_view)` failing is OK because this is a syntactic relation. Since, inheritance and composition are kind of the same here, `boost::string_view(text).data() == text.data()` seems to be a syntactic relation (i.e. the expectation of where the pointer should point is probably not semantic either). These terms seem somewhat loose. Maybe there's a context where this expectation might cause problems but I can't think of anything.  
  
> Violating LSP is not an UB per see.  
  
Oh... OK. So the UB we are talking about is just a consequence somewhat likely to happen. I always find it confusing when we talk about UB outside the context of the standard library. I still don't see when the `boost::string_view(text).data() == text.data()` could cause any problems. At this point, I'm just replicating comments here and there trying to help but I'm not even sure I'm qualified to discuss the implications of `const_string`. My initial intuition would be to use another pattern but people didn't like that either.

---

## Comment 34

> Username: vinniefalco  
> Created at: 2022-07-06 17:06:00 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1176470121  

> the user is expected never to bind const_string to core::string_view&  
  
Are users expected to bind `std::string` to `std::string_view&`?

---

## Comment 35

> Username: akrzemi1  
> Created at: 2022-07-06 18:04:00 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1176521912  

> > the user is expected never to bind const_string to core::string_view&  
>   
> Are users expected to bind `std::string` to `std::string_view&`?  
  
What I meant, is that conversion from `const_string&` to `core::string_view&` is offered syntactically, but does not guarantee the (full) semantics of  `core::string_view&`.  
  
In contrast, the converison from `std::string&` to `std::string_view&` is offered syntactically, and the full semantics of `std::string_view&` is guaranteed.  
  
So, now I changed my argument from talking about "from const_string" to talking about "from const_string ref". Maybe this is the heart of the disagreement. The present solution is fine as long as `const_string` is retuned by value and then either used directly (not bound to a reference) or converted to another type.

---

## Comment 36

> Username: akrzemi1  
> Created at: 2022-07-06 19:23:51 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1176591610  

Sorry, I just realized I talked nonsense. There is no` std::string&` to `std::string_view&` conversion.

---

## Comment 37

> Username: akrzemi1  
> Created at: 2022-07-07 05:12:20 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1177080089  

Actually, I tried to use a mutating function `remove_suffix` on `const_string` and it seemed to work: https://godbolt.org/z/jcn1bdEh7  
  
I mean, modification is allowed and works as modification is expected to work, it does not affect the copy. Could you show an example where using a mutating interface on `const_string` causes UB that the docs warn about?

---

## Comment 38

> Username: akrzemi1  
> Created at: 2022-07-08 08:33:19 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1178711317  

> We had a parallel discussion about this. It might be that this issue is not really a big problem. I'll replicate some of the most relevant comments that came up there:  
  
I think I have added an unnecessary confusion in this Issue report. I thought that it is about "every problem with `const_string`", but now I see it is only about the problem of:  
  
```c++  
boost::core::string_view(text).data() == text.data()  
```  
  
The problem boils down to what we consider to be the contract of `boost::core::string_view` (which tries to mimic `std::string_view`). The contract has never been specified formally enough, so to some extent it is subject to interpretation; and so is this issue.  
  
My understanding of the `string_view`'s contract is that:  
  
1.  The object of this type can be cheaply copied around (because it is just a pointer and the `int`), and both sides agree about this.  
2. Taking a substring in any form (including `remove_suffix`) requires no allocation.  
  
Bullet 1 -- I think -- is out of scope for LSP, because it only applies to passing by value.  
Bullet 2 is satisfied by `const_string`.  
  
Also, in the context of OO and its runtime polymorphism, when we talk about substitutability, we mean the behavior of virtual member functions, but not about constructors. Constructors are like factories: they are out of scope of LSV: it is obvious that you need different construction strategy for each subtype in the OO hierarchy, and whoever creates them needs to know implementation details of each such subtype. The use case in question:  
  
```c++  
boost::core::string_view(text).data() == text.data()  
```  
  
involves the object construction, so -- under my interpretation -- does not fall under the LSP reasoning.

---

## Comment 39

> Username: alandefreitas  
> Created at: 2022-07-09 19:17:30 UTC  
> Url: https://github.com/boostorg/url/issues/159#issuecomment-1179593537  

> mutating interface on const_string causes UB that the docs warn about  
  
I think some functions might depend on the parent std::string_view::data and std::string_view::size to infer where the string starts and ends. This might affect the correctness of allocations and deallocations. But I'm not sure we still have a case where this happens. Even if we don't, we would have to remember not to break it in the future.   
  
Then there's the semantic point of view because it's weird to say we are mutating a `const_string`.  
  
> involves the object construction, so -- under my interpretation -- does not fall under the LSP reasoning.  
  
Nice.
