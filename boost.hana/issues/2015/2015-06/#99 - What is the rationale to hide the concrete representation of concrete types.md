# #99 - [Question] What is the rationale to hide the concrete representation of concrete types? [Closed]

> Username: viboes  
> Created at: 2015-06-13 13:37:47 UTC  
> Updated at: 2015-06-17 01:56:33 UTC  
> Closed at: 2015-06-17 01:56:33 UTC  
> Url: https://github.com/boostorg/hana/issues/99  

Types in Hana are hidden around a Tag representing the data type.  
  
Why do you need to hide the concrete type? E.g. for Sets  
  
what prevents you to declare  
  
```  
struct Set {}; // type constructor for sets  
  
template <class ...Ts>  
set; // implementation defined  
  
make_set = [](auto&& ...xs) -> set<...> // more detail needed  
make<Set> = make_set  
```  
  
What is the rationale to hide the concrete representation of concrete types?

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-13 18:02:34 UTC  
> Url: https://github.com/boostorg/hana/issues/99#issuecomment-111737070  

The rationale for leaving most container types unspecified is [here](http://ldionne.github.io/hana/#tutorial-rationales-container_types). However, your question is slightly more specific than that. IIUC, you are suggesting that I actually provide a `set` type, but that I leave it implementation defined. Well, what is the interest of knowing that `make_set` returns a `set<...>` if that `set<...>` is implementation defined? You could never use that `set<...>` type anyway, since you know nothing about it (it could be `void` as far as the user is concerned).  
  
I think the idea is interesting since it might make containers much easier to document. However, we should also be very careful not to leave the users thinking that they can actually do something with the type `set<...>`, because they really can't.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-13 18:03:52 UTC  
> Url: https://github.com/boostorg/hana/issues/99#issuecomment-111737121  

The above link to the rationale seems to only send to the main documentation. The rationale is in the "Rationales/FAQ" section, "Why leave container types unspecified?" subsection.

---

## Comment 3

> Username: viboes  
> Created at: 2015-06-14 12:12:20 UTC  
> Url: https://github.com/boostorg/hana/issues/99#issuecomment-111819620  

I can understand the rational for the library, but what the user gain?  
  
Implementation defined don't means that it can be void, You need at least `set<T1, ... Tn>`. What I mean by implementation defined was more that there is nothing public, they can only use the free functions associated to it.

---

## Comment 4

> Username: ldionne  
> Created at: 2015-06-14 12:31:17 UTC  
> Updated at: 2015-06-14 12:31:41 UTC  
> Url: https://github.com/boostorg/hana/issues/99#issuecomment-111822328  

`set<T1, ..., Tn>` could be a template alias to `void`. But seriously, I'm not being pedantic. The problem is that some of these types would be dependent types, so the user can't pattern match on it. So for example, the following wouldn't work:  
  
``` c++  
template <typename ...T>  
void f(type_tuple<T...>) {  
    // ...  
}  
```  
  
where `type_tuple<T...>` would be the type of `tuple_t<T...>` (if we specified it), which creates a Hana `Tuple` containing types only. The problem is that `type_tuple<T...>` is a _dependent type_, so the above overload would never be matched. I would find this to be pretty misleading.
