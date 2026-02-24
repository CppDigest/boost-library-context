# #188 - xml_oarchieve fails to write </boost_serialization> end tag when exception is active [Open]

> Username: thorsten-ottosen  
> Created at: 2020-01-13 12:59:54 UTC  
> Updated at: 2022-04-28 06:53:04 UTC  
> Url: https://github.com/boostorg/serialization/issues/188  

The end tag is written in the destructor which contains code like:  
  
```cpp  
    if(std::uncaught_exception())  
        return;  
    if(0 == (this->get_flags() & no_header)){  
        os << "</boost_serialization>";  
```  
  
The implication is that one cannot save one's xml file, say, inside a scope guard.  
  
Why is the check for uncaught_exception there in the first place?

---

## Comment 1

> Username: robertramey  
> Created at: 2020-01-13 13:58:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/188#issuecomment-573675553  

>The implication is that one cannot save one's xml file, say, inside a scope guard.  
I would need a better explanation of this implication here  
>Why is the check for uncaught_exception there in the first place?  
I think this might be in there so that the destructor won't throw.    
  
The handling of possible exception inside a destructor has never been right.  It's trickier to get right that it would first appear.  And fixing would change the API which might break existing code.  I'd like to see it fixed though - but I've never had the time for what has seemed to be a low priority item.  Note there is an issue on this subject.

---

## Comment 2

> Username: thorsten-ottosen  
> Created at: 2020-01-13 14:17:24 UTC  
> Updated at: 2020-01-13 14:25:01 UTC  
> Url: https://github.com/boostorg/serialization/issues/188#issuecomment-573683777  

1. explanation of implication:  
If you have a function, that may exist via an exception, then you better not save an xml-file on that path of the program. Why should xml-files be special in this regard? The file will be saved, but it won't be possible to load it again.  
  
2. prohibiting the destructor from throwing:  
Sure. That would be bad if the destructor threw another exception. Would it not be ok just to do the following:  
  
```cpp  
 if(0 == (this->get_flags() & no_header) && os ) )  
        os << "</boost_serialization>";  
```  
If the disk is full or we are out of memory, we can probably tolerate a crash

---

## Comment 3

> Username: code-affinity  
> Created at: 2021-11-26 14:21:50 UTC  
> Updated at: 2021-11-26 14:22:22 UTC  
> Url: https://github.com/boostorg/serialization/issues/188#issuecomment-980011064  

This issue interacts in a very unpleasant way with issue #220.  
  
If an XML archive is (silently) corrupted because of issue #188, then the next time we attempt to read that archive, our application terminates because of issue 220.  
  
Our project has been fighting this application-terminating bug for years, and I've only just discovered these two issues and put them together.  It has been bad enough that we have considered abandoning Boost.Serialization entirely.

---

## Comment 4

> Username: thorsten-ottosen  
> Created at: 2022-04-28 06:53:04 UTC  
> Url: https://github.com/boostorg/serialization/issues/188#issuecomment-1111813729  

@robertramey   
  
Hi Robert,  
  
I don't see how you can be afraid of breaking existing code; existing code would already be very easy to break.  
  
We should prioritize that the code works in 99.99% of the time, not that it works in 50% of the time.   
  
**What exactly would break if you make the destructor noexcept(false) and removed the uncaught_exception() test?**  
  
As code-affinity writes, having a corrupt xml file is really, really bad. And it happens _silently_, cause an exception at some unexpected later point in time (in our case when users started the app again, which could be after one day or one week or one month).
