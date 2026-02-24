# #37 Make certain optional<T> operations trivial if they are for T [Open]

> Username: z33ky  
> Created at: 2017-10-24 12:18:29 UTC  
> Updated at: 2023-11-09 00:16:10 UTC  
> Url: https://github.com/boostorg/optional/pull/37  

See [trac-issue](https://svn.boost.org/trac10/ticket/13208).

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2017-10-24 12:26:11 UTC  
> Url: https://github.com/boostorg/optional/pull/37#issuecomment-338972667  

Yes. This will be a useful addition. I just need some time to implement it.

---

## Comment 2

> Username: z33ky  
> Created_at: 2017-10-24 12:33:13 UTC  
> Url: https://github.com/boostorg/optional/pull/37#issuecomment-338974363  

This *is* an implementation thereof.  
Did you mean you need some time to review it, or do you want to implement it differently?

---

## Comment 3

> Username: akrzemi1  
> Created_at: 2017-10-24 13:24:11 UTC  
> Url: https://github.com/boostorg/optional/pull/37#issuecomment-338988486  

Ooops, sorry. Somehow I only saw the test file. Let me review and test it.

---

## Comment 4

> Username: z33ky  
> Created_at: 2017-10-24 14:05:49 UTC  
> Updated_at: 2017-10-24 14:16:31 UTC  
> Url: https://github.com/boostorg/optional/pull/37#issuecomment-339001762  

Note that the trac-issue includes a short description of about the implementation:  
  
> I moved `m_storage` and `m_initialized` to a new class `optional_storage`, representing the new base class. Some methods managing those members are move there as well. `optional_base` indirectly inherits from `optional_storage` through `optional_destroyer`, `optional_mover` and `optional_copier`, which are templated classes including a boolean parameter to determine whether `T` is trivially destructible, movable and copyable and use template specialization to provide the respective trivial members.  
> The implementation relies on defaulting constructors, so without C++11 or newer only trivial destruction is possible.

---

## Comment 5

> Username: akrzemi1  
> Created_at: 2017-10-24 14:20:01 UTC  
> Url: https://github.com/boostorg/optional/pull/37#issuecomment-339006465  

The alternative, which I had in mind would only specialize for trivial `T`s. This is enough for most cases.

---

## Comment 6

> Username: akrzemi1  
> Created_at: 2017-10-24 16:24:07 UTC  
> Url: https://github.com/boostorg/optional/pull/37#issuecomment-339048741  

I can see how making `optional<T>` trivially_copyable fo trivial `T`s can help, and I intend to support it. But I fail to see how making it only has_trivial_move_constructor can help anyone in anything. Could you give me an example of where this would be useful?

---

## Comment 7

> Username: z33ky  
> Created_at: 2017-10-24 17:04:19 UTC  
> Url: https://github.com/boostorg/optional/pull/37#issuecomment-339061521  

If `T` is movable, but non-copyable, then has_trivial_copy would fail.  
  
This would work with `std::is_trivially_copy_constructible<T>::value || !std::is_copy_constructible<T>::value`.  
It seems there is no Boost-equivalent for `std::is_copy_constructible`, meaning this specialization would only be possible for C++11 without enhancing Boost.TypeTraits. For me personally, support for C++11 would be sufficient.  
  
I can't think of a useful example of a non-trivially copyable, but trivially movable type. If such a type exists and the semantics are useful, then `std::is_trivial` would be insufficient.  
  
On Tue, Oct 24, 2017 at 04:24:08PM +0000, Andrzej Krzemieński wrote:  
> I can see how making `optional<T>` trivially_copyable fo trivial `T`s can help, and I intend to support it. But I fail to see how making it only has_trivial_move_constructor can help anyone in anything. Could you give me an example of where this would be useful?  
>   
> --   
> You are receiving this because you authored the thread.  
> Reply to this email directly or view it on GitHub:  
> https://github.com/boostorg/optional/pull/37#issuecomment-339048741

---

## Comment 8

> Username: akrzemi1  
> Created_at: 2017-10-24 17:14:56 UTC  
> Url: https://github.com/boostorg/optional/pull/37#issuecomment-339064736  

For resource-managing classes move constructors are not trivial because you have to zero out the source object. If a class really has a trivial move constructor (it manages no resources), what would be the purpose for making it move-constructible but not copy-constructible? Have you ever seen one like that?  
  
Or, to put my concerns in another way, if I only implement a coarse grained feature: "if `T` is trivial, `optional<T>` is trivially-copyable", would you be missing anything in practice?

---

## Comment 9

> Username: z33ky  
> Created_at: 2017-10-24 21:50:04 UTC  
> Url: https://github.com/boostorg/optional/pull/37#issuecomment-339143748  

For resource-managing classes that need to release the resource, true.  
  
I have objects, that possess a ID, which is unique during the whole execution, meaning IDs should not be reused. Since it doesn't need to unregister the ID, it's destructor can be trivial.  
It wouldn't cause an issue with C++, but within my application logic, it does not make sense to copy such an object, hence the copy-constructor is deleted to prevent accidentally having two objects sharing same ID.  
However, there's nothing wrong with moving that object; It is even required to keep them in a `std::vector` for instance. On top of that, it doesn't need to do anything fancy when moving, so the move-constructor is defaulted. Now of course it also requires all members to be trivially movable in order to remain trivially movable.  
This example is unrelated to my `optional` use-case, where currently the class is indeed both trivially copyable and movable, but it is an instance of a class that has a move-, but no copy-constructor. Since your proposed requirement would be sufficient for my current use-case, I wouldn't be terribly disappointed.

---
