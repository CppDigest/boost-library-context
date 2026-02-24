# #318 - Ambiguous partial specialization on Clang 4.0 (trunk) [Closed]

> Username: brunocodutra  
> Created at: 2017-01-15 20:40:55 UTC  
> Updated at: 2017-01-17 21:06:14 UTC  
> Closed at: 2017-01-17 21:06:14 UTC  
> Url: https://github.com/boostorg/hana/issues/318  

Specializations of algorithms for `hana::tuple` fail with   
  
```.sh  
ambiguous partial specializations of 'tag_of<boost::hana::tuple<>, boost::hana::when<true> >'  
    struct tag_of : tag_of<T, when<true>> { };  
```  
  
on  
  
```.sh  
$ clang --version  
  
clang version 4.0.0 (trunk 291432)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```

---

## Comment 1

> Username: palebedev  
> Created at: 2017-01-15 22:52:30 UTC  
> Url: https://github.com/boostorg/hana/issues/318#issuecomment-272744604  

Looks like what I've already reported here:  
http://lists.llvm.org/pipermail/cfe-dev/2017-January/052160.html

---

## Comment 2

> Username: ldionne  
> Created at: 2017-01-15 22:54:06 UTC  
> Url: https://github.com/boostorg/hana/issues/318#issuecomment-272744713  

Thanks for the heads up. I guess we still don't know whether the problem is with the library of the compiler?

---

## Comment 3

> Username: pfultz2  
> Created at: 2017-01-15 23:22:54 UTC  
> Url: https://github.com/boostorg/hana/issues/318#issuecomment-272746427  

You can see the commit for the change here: https://github.com/llvm-mirror/clang/commit/89014aa92ecb6c7f3c2c95ae6342f729e9724dc2

---

## Comment 4

> Username: palebedev  
> Created at: 2017-01-16 19:19:56 UTC  
> Url: https://github.com/boostorg/hana/issues/318#issuecomment-272942842  

I've pinged cfe-dev once more, hopefully they'll notice before clang 4 is released.

---

## Comment 5

> Username: ldionne  
> Created at: 2017-01-16 22:44:00 UTC  
> Url: https://github.com/boostorg/hana/issues/318#issuecomment-272978893  

Unfortunately, I'm tempted to think that it's Hana's fault. If so, that will require a significant change to the way dispatching is done across the library, and that'll be a breaking change. In any case, that'll have to wait for Boost 1.64. I'm still hoping that it's a Clang bug, but I'm not too hopeful.

---

## Comment 6

> Username: brunocodutra  
> Created at: 2017-01-17 08:03:05 UTC  
> Url: https://github.com/boostorg/hana/issues/318#issuecomment-273046649  

I'm also tempted to go with Clang on this one unfortunately. The mental  
model that I use is that one partial template specialization is only more  
specialized than another if it depends on less *types*, i.e. non type  
template parameters are *mostly* ignored.  
  
cppreference corroborates this mental model and also gives an example where  
it doesn't fit, but I think this particular example doesn't apply in this  
case.  
  
http://en.cppreference.com/w/cpp/language/partial_specialization#Partial_ordering  
  
That said, I wouldn't be too surprised to be proven wrong here.

---

## Comment 7

> Username: brunocodutra  
> Created at: 2017-01-17 09:54:20 UTC  
> Url: https://github.com/boostorg/hana/issues/318#issuecomment-273073534  

Taking a closer look into it, the simplified mental model that I mentioned doesn't help here, because it is only good to address the rule that   
  
> The type of a template parameter corresponding to a specialized non-type argument shall not be dependent on a parameter of the specialization.  
  
The present case is more complex and involves non-deduced context in the partial specialization ordering of class templates, which is as hairy as it gets:  
  
http://stackoverflow.com/questions/31497357/partial-specialization-ordering-with-non-deduced-context  
  
I'm not too confident Clang is correct here anymore.

---

## Comment 8

> Username: palebedev  
> Created at: 2017-01-17 10:42:45 UTC  
> Url: https://github.com/boostorg/hana/issues/318#issuecomment-273101951  

This problem is complicated, indeed, as we're in underspecified area of the standard, but it looks like we're safe for now - the pattern in question is now part of clang tests: https://github.com/llvm-mirror/clang/commit/ae0874ff9877a328e03b768cf9c6d164f001e751 . I've verified that clang with this commit works again.

---

## Comment 9

> Username: ldionne  
> Created at: 2017-01-17 21:06:13 UTC  
> Url: https://github.com/boostorg/hana/issues/318#issuecomment-273299348  

That's great news, at least for now. Thanks a lot! I'll close this now.
