# #904 - value_to of a wrong sized Object return an error a bit misleading and a bit to improove [Closed]

> Username: RoyBellingan  
> Created at: 2023-06-04 20:12:37 UTC  
> Updated at: 2023-06-21 11:39:12 UTC  
> Closed at: 2023-06-21 11:39:12 UTC  
> Url: https://github.com/boostorg/json/issues/904  

### Version of Boost  
1_82  
  
### Steps necessary to reproduce the problem  
  
https://godbolt.org/z/7jdn9xh6f  
  
The returned error is   
```  
terminate called after throwing an instance of 'boost::detail::with_throw_location<boost::system::system_error>'  
  what():  array size does not match target size [boost.json:31 at /opt/compiler-explorer/libs/boost_1_82_0/boost/json/detail/value_to.hpp:567:9 in function 'boost::json::result<T> boost::json::detail::value_to_impl(boost::json::try_value_to_tag<T>, const boost::json::value&, described_class_conversion_tag)']  
```  
  
Which is a bit misleading, it should be object size, but the problem is the lack of context, working with a big json will quickly require to have to use a debugger to pinpoint the actual element which had the error.  
  
I think an error like, would be much better   
```  
The object at /path/to/2/object  
Has more element than the destination T  
```  
Now I do understand the implication of building a json pointer for all the element can easily be a performance killer, so maybe defining a macro can be a viable option ?  
  
Or I can try to play with somekind of linkedlist structure to store the string_view of the key that composes the path, and in case of error is assembled and used.

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-06-04 20:22:22 UTC  
> Url: https://github.com/boostorg/json/issues/904#issuecomment-1575699387  

There's basically no way to put that information into the `error_code`. If you really want to collect that information, you could use a custom handler that stores the path into the document (in addition to building the result). ANd if an error occurs, print it together with the exception/`error_code`.  
  
The error message is indeed misleading though, so I will change it.

---

## Comment 2

> Username: RoyBellingan  
> Created at: 2023-06-04 20:29:57 UTC  
> Updated at: 2023-06-04 20:32:53 UTC  
> Url: https://github.com/boostorg/json/issues/904#issuecomment-1575701613  

Yes I tried to play around the error_code, and the only way to carry such information will be abusing the   
```  
char * source_location  
```  
Which Is orrible to say the least.   
  
Do you have any suggestion on how to use a custom handler ? I tried to play around, but I have no idea except review the whole value_to system to have allow customization point (some kind of visitor pattern)  
  
I am currently trying a macro based approach, so only if someone is willing to pay this price is enabled.

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-06-04 20:34:57 UTC  
> Url: https://github.com/boostorg/json/issues/904#issuecomment-1575702955  

Wait, disregard my suggestion for handler, it has nothing to do with value_to. I guess, there's no way to do this except for using a completely custom conversion system.

---

## Comment 4

> Username: RoyBellingan  
> Created at: 2023-06-04 20:37:59 UTC  
> Updated at: 2023-06-04 20:39:00 UTC  
> Url: https://github.com/boostorg/json/issues/904#issuecomment-1575703771  

Do you think a macro based approach can be ok ?  
Something like adding   
  
(before value_to invocation, in the userland code)   
#define BOOST_PATH_SOMETHING(X) //where X is the class that will handle collecting the info  
  
Inside actual library code a few of   
BOOST_PATH_PUSH()  
BOOST_PATH_POP()  
  
and in case of error you will have (in user land)  
x.composePath()  
  
Of course the use of try_value_to will be needed in that case to properly operate.

---

## Comment 5

> Username: grisumbras  
> Created at: 2023-06-04 20:39:18 UTC  
> Url: https://github.com/boostorg/json/issues/904#issuecomment-1575704289  

Are you suggesting that we add this to the library? I don't think we'll do that.

---

## Comment 6

> Username: RoyBellingan  
> Created at: 2023-06-04 20:41:46 UTC  
> Url: https://github.com/boostorg/json/issues/904#issuecomment-1575705093  

Think is not a "hard" no...  
Maybe you can be interested in a proof of concept ?  
  
If you want we can discuss on the slack channel too.

---

## Comment 7

> Username: grisumbras  
> Created at: 2023-06-04 20:43:23 UTC  
> Url: https://github.com/boostorg/json/issues/904#issuecomment-1575705541  

If you really want to make such a proof of concept, I can't stop you. But the chances of it getting adopted are very slim.

---

## Comment 8

> Username: RoyBellingan  
> Created at: 2023-06-04 20:44:21 UTC  
> Url: https://github.com/boostorg/json/issues/904#issuecomment-1575705825  

Well in any case I need for myself, and it will be far easier than rewire a value_to subsytem.

---

## Comment 9

> Username: grisumbras  
> Created at: 2023-06-04 20:46:16 UTC  
> Url: https://github.com/boostorg/json/issues/904#issuecomment-1575706181  

How would it even work without patching every value_to helper? What is supposed to call push and pop?

---

## Comment 10

> Username: RoyBellingan  
> Created at: 2023-06-04 20:49:01 UTC  
> Url: https://github.com/boostorg/json/issues/904#issuecomment-1575707075  

Still thinking about it.  
The basic idea is some kind of poor man lambda, (lambda is a class with operato() in disguise after all no )  
  
So you define this class before starting the value_to chain of operation in userspace code.  
  
You also have to define the 2 macro push and pop to operate using it and not to be a NO-OP  
  
That is more or less the idea, which I took just a few line below here  
  
https://github.com/boostorg/json/blob/be759c5051b828f77b59d5c67b9b53d7330e627e/include/boost/json/detail/value_to.hpp#L516

---

## Comment 11

> Username: grisumbras  
> Created at: 2023-06-04 20:56:36 UTC  
> Url: https://github.com/boostorg/json/issues/904#issuecomment-1575710222  

Actually, now that I think about it, it might be possible to achieve this using contextual conversions (a new feature that will be in 1.83).

---

## Comment 12

> Username: RoyBellingan  
> Created at: 2023-06-04 20:57:14 UTC  
> Url: https://github.com/boostorg/json/issues/904#issuecomment-1575710956  

I am all hears... what is this and can I do something ?

---

## Comment 13

> Username: grisumbras  
> Created at: 2023-06-04 20:57:16 UTC  
> Url: https://github.com/boostorg/json/issues/904#issuecomment-1575710968  

This could actually be an interesting example.

---

## Comment 14

> Username: pdimov  
> Created at: 2023-06-04 23:31:13 UTC  
> Url: https://github.com/boostorg/json/issues/904#issuecomment-1575787036  

Keeping track of the path in the context in one way to do it, but the exception must still propagate upwards unscathed. (This can be done by the user via a custom context.)  
  
The other way of doing this is building the path on the way up as the exception propagates. (This can't be done by the user and must be done by Boost.JSON.)
