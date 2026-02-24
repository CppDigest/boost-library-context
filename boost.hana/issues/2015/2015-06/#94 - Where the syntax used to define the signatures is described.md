# #94 - [Question] Where the syntax used to define the signatures is described? [Closed]

> Username: viboes  
> Created at: 2015-06-13 10:37:45 UTC  
> Updated at: 2015-06-16 17:18:42 UTC  
> Closed at: 2015-06-13 17:40:00 UTC  
> Url: https://github.com/boostorg/hana/issues/94  

There are a lot of imprecisions in the description, e.g.   
  
```  
Given a Sequence S and a generalized type T, the signature is  
  
cartesian_product:S(S(T))→S(S(T))  
```  
  
Where is the definition of generalized type? Where is described the notation used to describe a signature?  
  
If not defined, it would be better to define them just here http://ldionne.com/hana/modules.html

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-13 16:49:29 UTC  
> Url: https://github.com/boostorg/hana/issues/94#issuecomment-111729276  

> Where is the definition of generalized type?  
  
The definition of generalized type used to be in a section about generalized types, which was removed because it was a bit unclear. A generalized type is basically `Tuple`, `Map`, etc... They are tags representing families of related types (e.g. `Tuple` represents all of Hana's tuples, regardless of their actual representation like `tuple_t<...>` and `make_tuple(...)`). I will clarify this in the documentation. Actually, I will remove the usage of _generalized type_ and replace it by the simpler and less obscure _tag_.  
  
> Where is described the notation used to describe a signature?  
  
That notation is the usual mathematical notation for defining functions. I did not think it was necessary to introduce it, but I will.  
  
> If not defined, it would be better to define them just here http://ldionne.com/hana/modules.html  
  
They will be explained in the "User manual" section on "Tags", which I'm almost done re-writing. Putting stuff where you suggest is a damn pain because Doxygen is too inflexible.

---

## Comment 2

> Username: viboes  
> Created at: 2015-06-14 12:24:01 UTC  
> Url: https://github.com/boostorg/hana/issues/94#issuecomment-111821866  

Louis, don't misunderstand me, the notation is intuitive to me, but I don't know for others.  
  
I find the generalized/datatype/Tag less clear than a type constructor.  
I see a type constructor as a class having an nested class template apply used to build concrete types.   
A class template can be seen as a type constructor also.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-06-14 14:21:35 UTC  
> Url: https://github.com/boostorg/hana/issues/94#issuecomment-111830462  

> Louis, don't misunderstand me, the notation is intuitive to me, but I don't know for others.  
  
Sure, sorry if I made it look like that. I do appreciate your comments cause it allows me to improve the library.  
  
> I find the generalized/datatype/Tag less clear than a type constructor.  
> I see a type constructor as a class having an nested class template apply used to build concrete types.   
> A class template can be seen as a type constructor also.  
  
I understand your view, but the link with _metafunctions_/_metafunction classes_ is not the right one, since Hana does not have parametric tags. They are just dumb tags used for dispatching, like in Fusion/MPL. Also, the C++ community will be more familiar to the word **tag** than **type constructor**, and I don't want to be the one pointing them to a Haskell wiki page (lol).

---

## Comment 4

> Username: viboes  
> Created at: 2015-06-14 14:30:59 UTC  
> Url: https://github.com/boostorg/hana/issues/94#issuecomment-111833709  

I understand you. Nevertheless I would like to know why Hana doesn't have parametric tags?

---

## Comment 5

> Username: ldionne  
> Created at: 2015-06-14 14:47:30 UTC  
> Url: https://github.com/boostorg/hana/issues/94#issuecomment-111836820  

Because knowing the parameters of a parametric tag requires computations, and it's also not that useful in practice since we're really just trying to have a simple tag-dispatching machinery.   
  
To define parametric tags properly, here's a gist of what we would need, say for Tuple:  
  
``` c++  
template <typename ...>  
struct Tuple; // proper parametric Tuple tag  
  
template <typename ...T>  
struct _tuple : implementation-defined { };  
  
template <typename ...T>  
struct datatype<_tuple<T...>> {  
    using type = Tuple<typename datatype<T>::type...>;  
};  
```  
  
However, when creating a Tuple, you would now have to state the tag with  
the proper parameters:  
  
``` c++  
auto xs = make<Tuple<int, char>>(1, 'c');  
```  
  
Which is more cumbersome. Now, here's what we would need for Optional:  
  
``` c++  
template <typename T>  
struct Optional; // proper parametric Optional tag  
  
template <typename T>  
struct _just : implementation-defined { };  
  
template <typename Tag>  
struct _nothing : implementation-defined { };  
  
  
template <typename T>  
struct datatype<_just<T>> {  
    using type = Optional<typename datatype<T>::type>;  
};  
  
template <typename Tag>  
struct datatype<_nothing<Tag>> {  
    using type = Optional<Tag>;  
};  
```  
  
Now, when you create a `nothing`, you have to specify the tag of what's in the `nothing`, since that can't be inferred automatically:  
  
``` c++  
auto n = nothing<Tuple<int, char, float>>;  
```  
  
So we don't win that much by doing this, and the syntax becomes clunkier for most cases. Right now, the syntax is pretty most of the time and only ugly in some specific cases (`sum`, `product`). Of course, we would win expressive power, that's undeniable. But at what cost?  
  
However, the real dealbreaker for me is that I fear this would have a bad effect on compile times, because the tag-dispatching system would become much more complex. The tag system is already slightly too complex for my liking, and I'd rather not make it worse.

---

## Comment 6

> Username: viboes  
> Created at: 2015-06-14 15:55:53 UTC  
> Url: https://github.com/boostorg/hana/issues/94#issuecomment-111842878  

I use  
  
``` c++  
struct tuple_tc { // proper parametric Tuple tag  
  template <typename ...Ts>  
  using apply = tuple<Ts...>;  
};  
template <typename ...T>  
struct tuple : implementation-defined { };  
  
template <typename ...T>  
struct type_constructor<tuple<T...>> {  
    using type = Tuple;  
};  
auto xs = make<tuple_tc>(1, 'c');  
```  
  
Sum types are particular on the compile time context. As the type is know at compile time is as if we had two independent types. The link between the two types is done via the operations, but we have never an `Optional<T>`.   
  
For me `Comparable` is applied to types, while `Functor` is applied to type constructors.  
The way you have customized all of them is via the Tag, while I prefer to customize Comparable with types and Functor with type constructors as it is the type constructor (Tuple) that is a `Functor`, not _tuple<T1, T2>).   
  
E.g. instead of customizing Comparable on the tag,  
  
``` c++  
    template <>  
    struct equal_impl<Optional, Optional> {  
        template <typename T, typename U>  
        static constexpr decltype(auto) apply(_just<T> const& t, _just<U> const& u)  
        { return hana::equal(t.val, u.val); }  
  
        static constexpr auto apply(_nothing const&, _nothing const&)  
        { return true_; }  
  
        template <typename T, typename U>  
        static constexpr auto apply(T const&, U const&)  
        { return false_; }  
    };  
```  
  
 I will customize on the type  
  
``` c++  
    template <typename T, typename U>, when<valid<is_optional<T> && is_optional<U>>>  
    struct equal_impl<T, U> {  
        template <typename T1, typename U1>  
        static constexpr decltype(auto) apply(_just<T1> const& t, _just<U1> const& u)  
        { return hana::equal(t.val, u.val); }  
  
        static constexpr auto apply(_nothing const&, _nothing const&)  
        { return true_; }  
  
        template <typename T, typename U>  
        static constexpr auto apply(T const&, U const&)  
        { return false_; }  
    };  
```  
  
I don't remember exactly, but on previous version of the library the user was able to instantiate Comparable::instance class. This instance class had an additional parameter so that we can SFINAE the instantiation.  
  
``` c++  
template <typename T, typename U>  
    struct Comparable::instance<T, U, when<valid<is_optional<T> && is_optional<U>>> : ...  
    {  
      // ....  
    };  
  
```  
  
I like a lot the previous design. Why have you changed this design?  
  
Customizing Functor on the tag or doing it on the type constructor is equivalent, as the tag is the type constructor.  
  
In the run-time context, I would make `just<T>` and `nothing` to be convertible to `optional<T>`.   
`optional_tc`or `optional<>` or `lift<optional> or`quote<optional>` could plays the role of type constructor.  
  
IMHO, making the difference between a type and a type constructor is essential.   
The concepts are not always applied to types, but a lot of them are applied to type constructors.  
  
Hana has made the choice to make the customization point using a Tag. This is a different choice that I don't share, even if I accept it.

---

## Comment 7

> Username: ldionne  
> Created at: 2015-06-16 17:18:42 UTC  
> Url: https://github.com/boostorg/hana/issues/94#issuecomment-112502769  

> I don't remember exactly, but on previous version of the library the user was able to instantiate Comparable::instance class. This instance class had an additional parameter so that we can SFINAE the instantiation.  
>   
> [...]  
>   
> I like a lot the previous design. Why have you changed this design?  
  
The mailing list initially prompted me to change this, but then I started agreeing more and more. I think providing standalone tag-dispatched functions is more flexible, because it's easier to customize individual algorithms.  
  
Also, regarding Tags vs type constructors: I agree that type constructors are definitely more expressive, and they would be a better design choice for a homogeneous library. I have no doubt on that. However, I'm not sure for a heterogeneous library, and this would have a significant impact on the current design. I guess this is an experiment that should be attempted in a future 2.0 version of the library. It would also be interesting to see how Concepts-lite interact with this design choice.
