# #50 - construct - template cannot be MoveConstructible [Open]

> Username: viboes  
> Created at: 2015-10-04 02:13:48 UTC  
> Updated at: 2016-03-05 17:29:56 UTC  
> Url: https://github.com/boostorg/hof/issues/50  

It is not clear from the description what would be the result of   
  
```  
auto x = construct<vector>()(1);  
```  
  
I have a make function that can also take a type-constructor so that we can  
  
```  
auto x = make<expected<_, error_code>>(1);  
auto x = make<unique_ptr<_, MyDeleter>>()(1);  
```  
  
Of course, we need some kind of customization from the user.

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 05:09:40 UTC  
> Url: https://github.com/boostorg/hof/issues/50#issuecomment-145431763  

> template cannot be MoveConstructible  
  
Well, the template itself cannot, but after the types are placed in the template, it must be MoveConstructible.  
  
> It is not clear from the description what would be the result of   
  
It probably needs a better description for "it will deduce the parameters to the template". So this:  
  
``` cpp  
auto x = construct<vector>()(1);  
```  
  
Would be the equivalent to this:  
  
``` cpp  
auto x = vector<int>(1);  
```  
  
Because it deduces `int` from `1`.  
  
> Of course, we need some kind of customization from the user.  
  
Template aliases can be used for this.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 17:22:17 UTC  
> Url: https://github.com/boostorg/hof/issues/50#issuecomment-145604124  

> Template aliases can be used for this.  
  
Yes, but this mean one template for each E in expected<_,E>.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-05 17:24:35 UTC  
> Url: https://github.com/boostorg/hof/issues/50#issuecomment-145604657  

> Yes, but this mean one template for each E in expected<_,E>.  
  
No, template aliases can be defined at class scope.

---

## Comment 4

> Username: viboes  
> Created at: 2015-10-05 22:33:31 UTC  
> Url: https://github.com/boostorg/hof/issues/50#issuecomment-145688365  

Please could you show me the definition and the usage of such a nested alias for expected<_,E>, it seems to me that it is not very user friendly.

---

## Comment 5

> Username: pfultz2  
> Created at: 2015-10-05 23:02:32 UTC  
> Url: https://github.com/boostorg/hof/issues/50#issuecomment-145693102  

Something like this:  
  
``` cpp  
template<class E>  
struct e  
{  
    template<class T>  
    using apply = expected<T, E>;  
};  
  
auto x = make<e<error_code>::apply>(1);  
  
```  
  
Although it may not be quite as slick as the placeholder expressions, which is a cool idea, but its kind of beyond the scope of the library. Fit is a function library not a metaprogramming library. Do you agree?

---

## Comment 6

> Username: viboes  
> Created at: 2015-10-06 00:10:09 UTC  
> Url: https://github.com/boostorg/hof/issues/50#issuecomment-145702813  

I don't like this kind of `e<error_code>::apply.`  
  
I prefer   
  
``` c++  
template <class E>  
struct expected<_, E>  
{  
    template<class T>  
    using apply = expected<T, E>;  
};  
```  
  
Now we can use `expected<_, E>` as a type constructor.  
  
About function versus meta-programming library. I believe that we should use the paradigms that a more appropriated to a specific context to make the user code as friendly as possible.  
  
What I proposing to you don't includes too much meta-programming.  
  
An alternative solution is to define the make function only on type constructors and use `lift/reverse_lift` to move from a template to a type constructor.  
  
```  
auto x = make<reverse_lift<expected, E>>(1);  
```

---

## Comment 7

> Username: pfultz2  
> Created at: 2015-10-14 14:01:24 UTC  
> Url: https://github.com/boostorg/hof/issues/50#issuecomment-148058389  

> What I proposing to you don't includes too much meta-programming.  
  
It is a quite bit of metaprogramming, not just internally, but the interface would need to provide these meta placeholders. That is beyond the scope of the Fit library.  
  
I could provide instead a `construct_meta` that would construct the type from a metafunction class. Then the user could construct the type using placeholder expressions from their favorite metaprogramming library.

---

## Comment 8

> Username: pfultz2  
> Created at: 2015-10-20 13:38:35 UTC  
> Url: https://github.com/boostorg/hof/issues/50#issuecomment-149570812  

Ok i've added `construct_meta` and documention.

---

## Comment 9

> Username: viboes  
> Created at: 2016-03-05 13:46:03 UTC  
> Url: https://github.com/boostorg/hof/issues/50#issuecomment-192646707  

I don't believe construct_meta is the solution. IMHO, we need just a function that do whatever is needed.

---

## Comment 10

> Username: viboes  
> Created at: 2016-03-05 13:47:35 UTC  
> Url: https://github.com/boostorg/hof/issues/50#issuecomment-192647531  

BTW   
  
```  
auto x = construct<vector>()(1);  
```  
  
should not work as there is a `vector<T>` constructor  
  
```  
explicit vector (size_type n);  
```

---

## Comment 11

> Username: pfultz2  
> Created at: 2016-03-05 17:26:41 UTC  
> Updated at: 2016-03-05 17:29:56 UTC  
> Url: https://github.com/boostorg/hof/issues/50#issuecomment-192693439  

> I don't believe construct_meta is the solution. IMHO, we need just a function that do whatever is needed.  
  
I don't know what you mean. What should the solution be?

---

## Comment 12

> Username: pfultz2  
> Created at: 2016-03-05 17:29:40 UTC  
> Url: https://github.com/boostorg/hof/issues/50#issuecomment-192694663  

> should not work as there is a vector<T> constructor  
  
Nope `construct<vector>()` does not work. I don't see that as a problem. The template version should be used when you want to deduce the types of the template. The constructors of vector were not written that way. Instead, `construct<vector<T>>()` should be used.
