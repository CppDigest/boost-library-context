# #199 - [Core] Negated version of boost::hana::is_default [Closed]

> Username: m-j-w  
> Created at: 2015-11-09 12:28:32 UTC  
> Updated at: 2015-12-02 22:16:15 UTC  
> Closed at: 2015-12-02 22:15:48 UTC  
> Url: https://github.com/boostorg/hana/issues/199  

I realized all concepts make use of a negated `is_default` when looking for a valid specialization. Since that exclamation mark is easily overlooked leading to misinterpretation, a negated `is_not_default` might improve readability. Perhaps a `is_specialized`, `has_specialization` or `provides_specialization` might be even more clear in indicating the intent.  
Also, in its definition, shouldn't the line `static_cast<default_>(*(Method*)0)` make use of `nullptr` ?

---

## Comment 1

> Username: ldionne  
> Created at: 2015-11-09 13:34:32 UTC  
> Updated at: 2015-11-09 13:43:25 UTC  
> Url: https://github.com/boostorg/hana/issues/199#issuecomment-155063915  

You're right that `is_default` is always negated. I think that `is_implemented` would be more descriptive. Regarding the usage of `0` instead of `nullptr`; it's really just a nitpick, since I'm casting it to `Method*` anyway. I guess `nullptr` might be more idiomatic.  
  
[Edit: I'm using `(SomeType*)0` in several places because it's shorter to type than `(SomeType*)nullptr`. Consider for example [detail/concepts.hpp](https://github.com/boostorg/hana/blob/62bed060b2d08991846bf40848ad0f839491aea5/include/boost/hana/detail/concepts.hpp#L35); it would be less readable to use `nullptr` there, for no real benefit. These are implementation details. If you're annoyed by it and would prefer `nullptr`, you can submit a PR that changes them throughout the code base. If readability is not impacted, I'll happily merge it. --end edit]

---

## Comment 2

> Username: ldionne  
> Created at: 2015-11-09 13:37:23 UTC  
> Url: https://github.com/boostorg/hana/issues/199#issuecomment-155064794  

Oh wait, `is_implemented` won't work, since a method can be implemented but with a default implementation. This is why I didn't use `is_implemented` in the first place, I think (I have already thought about this issue some time ago, but I had decided to keep negating `is_default`).

---

## Comment 3

> Username: ldionne  
> Created at: 2015-12-02 22:15:48 UTC  
> Url: https://github.com/boostorg/hana/issues/199#issuecomment-161451231  

Closing this, since I'm not convinced of the added value of `is_not_default` vs `!is_default`, and using something like `is_specialized` would sometimes be wrong, as explained above.  
  
But just to expand on that, what I meant above is that since it is possible for an algorithm to be implemented (via a default implementation) without a container specializing it, `is_specialized` wouldn't say the right thing. Also, `is_implemented` would _also_ not work, since it could be the case that a "default" algorithm has no implementation, which is the case e.g. of `at`:  
  
``` c++  
template <typename It, bool condition>  
struct at_impl<It, when<condition>> : default_ {  
    template <typename ...Args>  
    static constexpr auto apply(Args&& ...) = delete;  
};  
```  
  
For lack of a clearly superior alternative, I'd rather stick with `hana::is_default`.
