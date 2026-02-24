# #231 - What about "boost pfr" extension? [Open]

> Username: denzor200  
> Created at: 2021-03-27 11:33:55 UTC  
> Updated at: 2022-12-21 08:17:36 UTC  
> Url: https://github.com/boostorg/fusion/issues/231  

It's a new reflection library from Antony Polukhin  
https://github.com/boostorg/pfr  
Why not adapt it to fusion as another extension?  
And then we get rid of from `BOOST_FUSION_ADAPT_STRUCT`.

---

## Comment 1

> Username: denzor200  
> Created at: 2021-03-27 19:40:29 UTC  
> Url: https://github.com/boostorg/fusion/issues/231#issuecomment-808792164  

Draft here:  
https://github.com/boostorg/fusion/pull/232

---

## Comment 2

> Username: denzor200  
> Created at: 2022-11-27 18:10:53 UTC  
> Updated at: 2022-11-27 18:12:37 UTC  
> Url: https://github.com/boostorg/fusion/issues/231#issuecomment-1328308478  

I need to apply a small patch here: https://github.com/boostorg/fusion/blob/boost-1.80.0/include/boost/fusion/support/tag_of.hpp#L55|  
Just like this sample:  
https://godbolt.org/z/4ePcWbYqh  
It will allow me to integrate PFR as fallback, so thus PFR will be selected when no other reflection not found for T.  
This strategy is so simple to implement and will be conviniece for Fusion's users.

---

## Comment 3

> Username: denzor200  
> Created at: 2022-11-27 18:14:08 UTC  
> Url: https://github.com/boostorg/fusion/issues/231#issuecomment-1328309168  

@djowel what do you think about this patch above?

---

## Comment 4

> Username: djowel  
> Created at: 2022-11-29 00:21:12 UTC  
> Updated at: 2022-11-29 00:24:54 UTC  
> Url: https://github.com/boostorg/fusion/issues/231#issuecomment-1329913856  

What is `non_fusion_tag` ? [edit, sorry if that was not clear]... I meant why is the link pointing to the `non_fusion_tag` ?

---

## Comment 5

> Username: denzor200  
> Created at: 2022-11-29 07:35:14 UTC  
> Url: https://github.com/boostorg/fusion/issues/231#issuecomment-1330202330  

> What is `non_fusion_tag` ? [edit, sorry if that was not clear]... I meant why is the link pointing to the `non_fusion_tag` ?  
  
The link pointing to the line that I want to edit. Like this:  
```  
-              mpl::identity<non_fusion_tag> >::type  
+              mpl::identity<typename tag_of_fallback<Sequence>::type> >::type  
```  
And also this before the line(in the `detail` namespace):  
```  
template<typename Sequence, typename Active=void>  
struct tag_of_fallback  
{  
    typedef non_fusion_tag type;  
};  
```

---

## Comment 6

> Username: djowel  
> Created at: 2022-11-29 09:10:40 UTC  
> Url: https://github.com/boostorg/fusion/issues/231#issuecomment-1330311997  

Ah. Makes sense. Go for it.

---

## Comment 7

> Username: denzor200  
> Created at: 2022-11-29 11:55:21 UTC  
> Url: https://github.com/boostorg/fusion/issues/231#issuecomment-1330512866  

> Ah. Makes sense. Go for it.  
  
https://github.com/boostorg/fusion/pull/262  
Can you merge this before I do it?

---

## Comment 8

> Username: denzor200  
> Created at: 2022-12-21 08:17:36 UTC  
> Url: https://github.com/boostorg/fusion/issues/231#issuecomment-1360990702  

> Ah. Makes sense. Go for it.  
  
Here: https://github.com/boostorg/fusion/pull/263
