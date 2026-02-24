# #72 - Consider allowing serialization of classes with user-defined operator& [Closed]

> Username: alfC  
> Created at: 2017-11-02 07:42:44 UTC  
> Updated at: 2017-11-10 06:05:48 UTC  
> Closed at: 2017-11-08 17:21:54 UTC  
> Url: https://github.com/boostorg/serialization/issues/72  

BS uses `operator&` as a way to obtain addresses (probably to track identical objects), and therefore the intent is clearly to take the address and not some fancy handler resulting from and hypothetical return type of the user defined `operator&`.  
  
If the user has a custom `operator&` that returns something different than a pointer, serialization fails to compile. If it returns a fake pointer value the program may crash.  
  
The only change necessary, is to replace `&t` by `boost::addressof(t)`.  
`boost::addressof` is warrantied  to return the real address of the object in a `decltype(t)*`.  
  
Yes, this is repeat of  
https://github.com/boostorg/serialization/pull/41/  
  
I made a pull request (explained in the issue) but it was at a time when I didn't know how use git/github  
https://github.com/boostorg/serialization/pull/41/commits and I am afraid the changes didn't go anywhere.  
  
In part I had a hard time making the changes and testing because BS is not a header-only library :)  
https://github.com/boostorg/serialization/issues/71

---

## Comment 1

> Username: robertramey  
> Created at: 2017-11-08 17:21:54 UTC  
> Url: https://github.com/boostorg/serialization/issues/72#issuecomment-342890337  

done on develop branch

---

## Comment 2

> Username: correaa  
> Created at: 2017-11-08 17:50:18 UTC  
> Url: https://github.com/boostorg/serialization/issues/72#issuecomment-342898929  

Awesome! thank you for the library.

---

## Comment 3

> Username: alfC  
> Created at: 2017-11-09 15:05:49 UTC  
> Updated at: 2017-11-09 15:06:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/72#issuecomment-343182306  

There are couple of additional places that need to be changed as well for this to work:  
  
nvp.hpp:  
```  
    explicit nvp(const char * name_, T & t) :  
        // note: added _ to suppress useless gcc warning  
        std::pair<const char *, T *>(name_, --& t--++boost::adressof(t)++)  
    {}  
```  
  
  
iserializer.hpp  
```  
             void * x = --&--++boost::addressof(++ const_cast<T &>(t) ++)++;  
```

---

## Comment 4

> Username: robertramey  
> Created at: 2017-11-09 18:20:13 UTC  
> Url: https://github.com/boostorg/serialization/issues/72#issuecomment-343244527  

What the heck is this?  It doesn't even look like C++.  It's impossible for me to verify.  Did you actually run the test suite on this?  If this can't be resolved, I'll have to back out the original change.

---

## Comment 5

> Username: alfC  
> Created at: 2017-11-09 21:57:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/72#issuecomment-343304894  

On Thu, Nov 9, 2017 at 10:20 AM, Robert Ramey <notifications@github.com>  
wrote:  
  
> What the heck is this? It doesn't even look like C++.  
>  
Sorry for the confusion. What I mean is that  
in nvp.hpp:  
  
std::pair<const char *, T *>(name_, & t)  
  
...needs to be replaced by:  
  
std::pair<const char *, T *>(name_, boost::adressof(t))  
  
and in iserializer.hpp  
  
             void * x = & const_cast<T &>(t);  
  
...needs to be replaced by  
  
             void * x = boost::addressof(const_cast<T &>(t));  
  
It's impossible for me to verify. Did you actually run the test suite on  
> this?  
>  
Yes, these are the changes I made and they worked.  
  
> If this can't be resolved, I'll have to back out the original change.  
>  
I hope the misunderstanding is resolved. I regret being too creative with  
the notation (-- remove-- and ++add++). At the end it is no different to  
what is was done to oserializar.hpp, and the changes are independent,  
completing this changes will make the class with operator& word for both,  
serialization, deserialization and as part NVP xml.

---

## Comment 6

> Username: alfC  
> Created at: 2017-11-10 06:05:48 UTC  
> Url: https://github.com/boostorg/serialization/issues/72#issuecomment-343383262  

Also perhaps to minimize code, it may be useful to use only boost::addressof, which does the job, and if boost::addressof is later implemented in terms of std::addressof at the end is going to be the same. Just a suggestion.
