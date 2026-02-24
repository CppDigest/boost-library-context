# #81 Added 'BOOST_TEST_DISABLE_ALT_STACK' flag to use with ESXi build. [Closed]

> Username: winder  
> Created at: 2015-07-24 00:53:37 UTC  
> Updated at: 2016-07-12 19:01:50 UTC  
> Closed at: 2016-05-27 08:42:26 UTC  
> Url: https://github.com/boostorg/test/pull/81  

I needed this flag to target a binary for ESXi. It is a stripped down version of Linux that doesn't support all of the glibc functions, and doesn't provide a compiler. So you need to build your application on a normal machine while taking care not to use unsupported functions. Because it isn't actually built with an ESXi compiler there are no default preprocessor directives to tap into, I wasn't sure of another way to implement this.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2016-05-23 22:22:19 UTC  
> Url: https://github.com/boostorg/test/pull/81#issuecomment-221113712  

Hi,  
  
Sorry for the late reply, but I had no clue what this was about. Now I know a bit more. From your comment, as I understand it, there is no proper way to detect that the target is ESXi, even with a cross compilation. Is that correct?  
  
I am fine with disabling the alternative stack, but I would definitely need an entry in the documentation or at least in the code. Thanks.

---

## Comment 2

> Username: winder  
> Created_at: 2016-05-24 14:26:56 UTC  
> Url: https://github.com/boostorg/test/pull/81#issuecomment-221288299  

@raffienficiaud Thanks. Your assessment is correct, there were no proper flags so I needed to add this custom one in order to run unit tests on ESXi. I added a comment to the code describing why the non-standard flag is being used. I'm not sure how useful it would be to document this more widely, the errors I saw led me to `BOOST_TEST_USE_ALT_STACK` pretty directly so I think this is sufficient.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2016-05-27 08:42:25 UTC  
> Url: https://github.com/boostorg/test/pull/81#issuecomment-222092897  

I was more thinking of a documentation like this: b7492a43cd8a6d7db3c417bbc53c5244dbcdfe24 , but thanks anyway. Should be in 1.62.

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2016-07-12 19:01:50 UTC  
> Url: https://github.com/boostorg/test/pull/81#issuecomment-232146134  

Merged to master a38161dd316028d64f9dbf36168095470680a747

---
