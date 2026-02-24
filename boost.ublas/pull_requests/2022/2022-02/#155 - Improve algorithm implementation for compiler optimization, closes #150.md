# #155 Improve algorithm implementation for compiler optimization, closes #150  [Merged]

> Username: Neel-Shah-29  
> Created at: 2022-02-06 07:39:02 UTC  
> Updated at: 2022-02-06 18:20:35 UTC  
> Merged at: 2022-02-06 18:20:35 UTC  
> Closed at: 2022-02-06 18:20:35 UTC  
> Url: https://github.com/boostorg/ublas/pull/155  

This PR fixes issue https://github.com/boostorg/ublas/issues/150 by providing the trailing return type and passing the lambda through one of its arguments.

---

## Comment 1

> Username: amitsingh19975  
> Created_at: 2022-02-06 08:40:22 UTC  
> Url: https://github.com/boostorg/ublas/pull/155#issuecomment-1030777915  

Follow https://www.conventionalcommits.org/en/v1.0.0/ for a commit message, and be more explicit about the message rather than generic.

---

## Comment 2

> Username: Neel-Shah-29  
> Created_at: 2022-02-06 09:12:53 UTC  
> Url: https://github.com/boostorg/ublas/pull/155#issuecomment-1030782617  

> Follow https://www.conventionalcommits.org/en/v1.0.0/ for a commit message, and be more explicit about the message rather than generic.  
  
Yes sure I will do it. Also issue is resolved right as all checks are passed so i dont need to make any further changes right?

---

## Comment 3

> Username: Neel-Shah-29  
> Created_at: 2022-02-06 11:50:40 UTC  
> Url: https://github.com/boostorg/ublas/pull/155#issuecomment-1030816663  

I have made the required changes.

---

## Review 4 [Approved]

> Username: amitsingh19975  
> Created_at: 2022-02-06 11:59:53 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/155#pullrequestreview-874029009  

There're a few indentation issues, but they can be fixed in the future. So, you don't have to worry about them.  
  
Everything looks good, and thanks for contributing.

---

## Comment 5

> Username: bassoy  
> Created_at: 2022-02-06 14:28:08 UTC  
> Url: https://github.com/boostorg/ublas/pull/155#issuecomment-1030843580  

> There're a few indentation issues, but they can be fixed in the future. So, you don't have to worry about them.  
  
We should fix them now if we have any. @amitsingh19975 Please annotate the lines which you think need better indentation.   
  
> Everything looks good, and thanks for contributing.

---

## Review 6 [Changes requested]

> Username: bassoy  
> Created_at: 2022-02-06 14:32:06 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/155#pullrequestreview-874045554  

One line needs to be indented.

📁 include/boost/numeric/ublas/tensor/algorithms.hpp

```diff
 166 |- 
 167 |-   lambda = [&lambda, n, w](SizeType r, PointerIn a, ValueType k)
 161 |+ auto lambda = [n, w](auto const& self, SizeType r, PointerIn a, ValueType k)-> ValueType
```

> Username: bassoy  
> Created_at: 2022-02-06 14:29:18 UTC  
> Updated_at: 2022-02-06 14:32:06 UTC  
> Url: https://github.com/boostorg/ublas/pull/155#discussion_r800187672  

Missing indendation.

> Username: Neel-Shah-29  
> Created_at: 2022-02-06 15:45:34 UTC  
> Url: https://github.com/boostorg/ublas/pull/155#discussion_r800197532  

Done with the changes


---

## Comment 7

> Username: amitsingh19975  
> Created_at: 2022-02-06 14:37:13 UTC  
> Url: https://github.com/boostorg/ublas/pull/155#issuecomment-1030845237  

@bassoy, what's your thought on moving trailing return type on to the next line for better readability.

---

## Comment 8

> Username: amitsingh19975  
> Created_at: 2022-02-06 15:47:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/155#issuecomment-1030859022  

@Neel-Shah-29 could you indent the trailing return type a bit.

---

## Comment 9

> Username: Neel-Shah-29  
> Created_at: 2022-02-06 16:04:07 UTC  
> Url: https://github.com/boostorg/ublas/pull/155#issuecomment-1030862229  

> @Neel-Shah-29 could you indent the trailing return type a bit.  
  
I have brought the trailing return type to next line , do i need to indent it more towards the left side.

---

## Comment 10

> Username: amitsingh19975  
> Created_at: 2022-02-06 16:04:54 UTC  
> Updated_at: 2022-02-06 16:06:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/155#issuecomment-1030862398  

> > @Neel-Shah-29 could you indent the trailing return type a bit.  
>   
> I have brought the trailing return type to next line , do i need to indent it more towards the left side.  
  
Yes, but indent toward the right side.  
  
From  
  
```cpp  
auto fn = ...  
->  
```  
  
To  
  
```cpp  
auto fn = ...  
    ->  
```

---

## Comment 11

> Username: Neel-Shah-29  
> Created_at: 2022-02-06 17:28:35 UTC  
> Url: https://github.com/boostorg/ublas/pull/155#issuecomment-1030877292  

> > > @Neel-Shah-29 could you indent the trailing return type a bit.  
> >   
> >   
> > I have brought the trailing return type to next line , do i need to indent it more towards the left side.  
>   
> Yes, but indent toward the right side.  
>   
> From  
>   
> ```c++  
> auto fn = ...  
> ->  
> ```  
>   
> To  
>   
> ```c++  
> auto fn = ...  
>     ->  
> ```  
  
Done with all the changes which were suggested . Kindly have a look once. Thankyou

---
