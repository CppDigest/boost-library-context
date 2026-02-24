# #35 - Should Traits be Available for View Proxies? [Closed]

> Username: stellarpower  
> Created at: 2021-11-25 20:29:36 UTC  
> Updated at: 2021-12-13 23:15:25 UTC  
> Closed at: 2021-12-13 23:15:25 UTC  
> Url: https://github.com/boostorg/qvm/issues/35  

Demo:  
https://godbolt.org/z/E9vjKha1G  
  
It seems that traits specialisations aren't available for view proxies. Is this by design? Or am I doing something wrong?  
  
I found this in the context of writing some stream operator overloads. I wanted to use concepts to filter out the correct overoads, and in these scenarios, making a copy would be undesirable for the sole reason of outputting as I just take by const lvalue reference.  
  
Should we be able to deduce whether a view proxy's type is (or can be converted to) a quaternion/vector/matrix, and deduce the dimensions and scalar types of these?

---

## Comment 1

> Username: zajo  
> Created at: 2021-11-26 00:10:48 UTC  
> Updated at: 2021-11-26 00:12:33 UTC  
> Url: https://github.com/boostorg/qvm/issues/35#issuecomment-979526261  

Try https://godbolt.org/z/M9qWa68nY. I should probably provide specializations for removing const automatically, but it seems appropriate that a ref isn't a vec/mat type.

---

## Comment 2

> Username: stellarpower  
> Created at: 2021-11-26 00:53:53 UTC  
> Url: https://github.com/boostorg/qvm/issues/35#issuecomment-979566860  

Ohh, I'm just missing the decay! 😆  
  
Interesting though as my overload should've taken a `const T&` and then required `is_vec<T>::value`. I should have a look in more detail where I lose the binding properly.  
  
Maybe this could be added as one of the quirks/FAQs at the bottom of the docs? As we're interested largely in value types it seems somewhat unusual from a user perspective to differentiate between the two (it never occurred to me to think about the reference), but I appreciate that it may of course be needed internally.  
  
Thanks!!

---

## Comment 3

> Username: zajo  
> Created at: 2021-11-26 00:58:47 UTC  
> Url: https://github.com/boostorg/qvm/issues/35#issuecomment-979576604  

That's why I'm saying, `is_vec`, `is_mat` etc. should get rid of the `const` automatically, and then your use case would have worked just fine. Does this sound right?

---

## Comment 4

> Username: stellarpower  
> Created at: 2021-11-26 02:55:56 UTC  
> Updated at: 2021-11-26 02:56:20 UTC  
> Url: https://github.com/boostorg/qvm/issues/35#issuecomment-979650627  

What I had in my original real code should have stripped the CV-qualifiers anyway, so I'm not entirely sure what happened. I just removed the copy from the proxy to a mat/vec and recompiled my original code, and it's now building again 🙄. If I have the time, I'll try to update a better MRE. I was also hitting a separate issue that `is_vec<char[N]>` is true, which then meant that string literals were being treated as QVM types and resulting in a second level of long errors.  
  
Would a separate "concept" perhaps make sense, rather than modifying the original? One that decays the type, strips qualifiers, and is generally for less strict checking. Or perhaps something like `is_convertible`?  
  
I was wondering what the thinking was behind not specialising for reference types. Thinking about it, I'm not sure where it wouldn't be appropriate for a T& to be treated as a q/v/m if a T can be - except explicitly in template arguments, etc. After all, once inside a function body and the referene is bound, referencing a T& is exactly like operating on a T on the stack.

---

## Comment 5

> Username: zajo  
> Created at: 2021-11-26 20:59:13 UTC  
> Url: https://github.com/boostorg/qvm/issues/35#issuecomment-980433181  

Thanks. I've changed the definitions of `is_vec`, `is_mat`, `is_quat` to require `is_scalar<T>` to be true for their scalar type; and also changed `is_scalar<char>` to false (`is_scalar<signed char>` and `is_scalar<unsigned char>` are still both true). This makes `is_vec<char[N]>` false, as it should be. https://github.com/boostorg/qvm/commit/cfa94dbd406ee25fcd0e6a07b8824a413b2672c5  
  
I'll think about ref types, but this is a good change regardless.
