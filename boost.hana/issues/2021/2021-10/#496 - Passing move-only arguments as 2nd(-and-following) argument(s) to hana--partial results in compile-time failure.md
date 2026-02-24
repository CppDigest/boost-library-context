# #496 - Passing move-only arguments as 2nd(-and-following) argument(s) to hana::partial results in compile-time failure [Open]

> Username: Aster89  
> Created at: 2021-10-29 17:04:59 UTC  
> Updated at: 2021-10-29 17:04:59 UTC  
> Url: https://github.com/boostorg/hana/issues/496  

## Foreword  
  
I have read #239 and skimmed through [the conversation on gitter](https://gitter.im/boostorg/hana?at=5696b9565de13b3f15e2e681) that was linked there.  
  
It is about why `hana::partial` calls the `&` overload of its first argument's `operator()` when its second argument is an rvalue. (@badair expected that the `const&` const overload be called, despite the presence of a `&&` overload too, but that's not relevant here.)  
  
@ldionne explained that `hana::partial` stores the arguments by value (see `basic_tuple<F, X...> storage_;` in `partial.hpp`), and he also wrote  
  
> The goal here is that `p` is now a fully standalone closure that owns whatever objects it needs to do its job   
  
so the by-value policy was intended.  
  
However, imho, he also seems to leave some room for reconsidering that choice:  
  
> the most flexible option (assuming we hold the arguments by value inside `partial`). However, having partial provide a non-const overload to `operator()` […] might just be a possibility to shoot oneself in the foot.  
>  
> […] do you think this behavior is broken? I'm ambivalent about it, but I'm inclined to think that this is unintuitive to most people given your own confusion.  
  
In the conversation on gitter I linked at the top, the hypothesis was made that `hana::partial` could store references to the arguments, and Louis wrote this  
  
> I think a perfect forwading partial would be easy to achieve. Go look at the rest of partial's implementation, but we would most likely need the following only:  
>  
>     template <std::size_t ...n, typename F, typename ...X>  
>     struct partial_t<std::index_sequence<n...>, F, X...> {  
>         // ... same as before ...  
>      
>         // Here, instead of storing actual values, store references  
>         basic_tuple<F, X&&...> storage_;  
>      
>         // ... same as before ...  
>     };  
>  
  
and commented  
  
> `partial` would only hold references, and it would assume that whatever the references point to are in scope for the whole lifetime of the `partial` object.  So there would be no need to move-construct any actual objects. One way to think of it is like if `partial` was holding pointers to external objects. No need to move construct anything there.  
>  
> […]  
>  
> Note that we wouldn’t be using `std::cref` or `std::ref` at all to implement this.  
  
So again Louis seems to not discard _a priori_ the idea of storing references. However, not long after that comment, I think the conversation on the topic has been abandoned. Indeed, Louis closed the post 2 years later.  
  
## My question/proposal  
  
One consequence of `hana::partial` storing by value that was not mentioned is that it bans the possibility of being passed move-only lvalue arguments for the function. [Example](https://godbolt.org/z/hfs7EYcrK):  
```cpp  
#include <boost/hana/functional/id.hpp>  
#include <boost/hana/functional/partial.hpp>  
  
using namespace boost::hana;  
  
struct MoveOnly {  
    MoveOnly(MoveOnly const&) = delete; MoveOnly(MoveOnly&&) = default;  
};  
  
int main() {  
    MoveOnly m{};  
    partial(id, m);  
}  
```  
  
Now, changing `basic_tuple<F, X&&...> storage_;` to `basic_tuple<F, X...> storage_;` would change the behavior that `hana::partial` has had so far, as Louis described (_`partial` would only hold references, and it would assume that whatever the references point to are in scope for the whole lifetime of the `partial` object_).  
  
On the other hand, we could take another approach: let `hana::partial` behave as it currently does in the cases that it currently supports, and also add support the case of move-only arguments (by storing them by `const&`), which currently results in a compile-time error.  
  
I'd be happy to make a PR. To address this.
