# #140 Fix signed/unsigned mismatch warning on gcc. [Merged]

> Username: Lastique  
> Created at: 2015-07-14 07:57:34 UTC  
> Updated at: 2015-07-27 13:19:54 UTC  
> Merged at: 2015-07-27 13:01:34 UTC  
> Closed at: 2015-07-27 13:01:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/140  



---

## Comment 1

> Username: djowel  
> Created_at: 2015-07-14 09:50:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/140#discussion_r34552130  

Why did you add the logic with MaxDigits < 0 ? I don' think this will ever happen in this code branch.

---

## Comment 2

> Username: Lastique  
> Created_at: 2015-07-14 09:58:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/140#discussion_r34552670  

On Tue, Jul 14, 2015 at 12:50 PM, Joel de Guzman notifications@github.com  
wrote:  
  
> In include/boost/spirit/home/qi/numeric/detail/numeric_utils.hpp  
> https://github.com/boostorg/spirit/pull/140#discussion_r34552130:  
>   
> > @@ -316,7 +316,7 @@ namespace boost { namespace spirit { namespace qi { namespace detail  
> >              if (!Accumulate)  
> >              {  
> >                  // skip leading zeros  
> > -                while (it != last && *it == '0' && leading_zeros < MaxDigits)  
> > -                while (it != last && *it == '0' && (MaxDigits < 0 || leading_zeros < static_cast< std::size_t >(MaxDigits)))  
>   
> Why did you add the logic with MaxDigits < 0 ? I don' think this will ever  
> happen in this code branch.  
> 1. I didn't find any restrictions on the MaxDigits values. From the  
>    specialization below it seems MaxDigits == -1 is allowed and means "no  
>    limit". I don't know what behavior should be for other negative values, so  
>    I extended the same semantics on them as well. Let me know if this  
>    assumption is wrong.  
> 2. I found only one specialization of extract_int, which is the one below -  
>    for MinDigits == 1 and MaxDigits == -1. This specialization won't be used  
>    for e.g. MinDigits == 3 && MaxDigits == -1 which means the generic template  
>    will be used and the 'while' condition should be prepared for negative  
>    values.

---

## Comment 3

> Username: djowel  
> Created_at: 2015-07-14 11:02:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/140#discussion_r34556925  

Indeed. You are right. I'll review the code some more. It's been a while.

---

## Comment 4

> Username: Lastique  
> Created_at: 2015-07-27 11:10:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/140#issuecomment-125168056  

Ping? Would be great if this fix made it to 1.59.

---

## Comment 5

> Username: djowel  
> Created_at: 2015-07-27 11:51:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/140#issuecomment-125177422  

I'm not comfortable with the code change and possible behaviour change, unless there are some tests that exploit the new code, if it is indeed a bug without it. In that case, then perhaps it's best to have that in another PR.

---

## Comment 6

> Username: Lastique  
> Created_at: 2015-07-27 12:08:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/140#issuecomment-125182413  

The original code was comparing size_t with an int. The negative int would be converted to a large unsigned number in this case, so the behavior was "to skip up to 4 billion something leading zeros". The current behavior is "to skip all leading zeros", which is the same for all practical purposes.  
  
I can change this PR to explicitly cast the int to size_t, which will remove the warning and keep the 4 billion quirk. It just doesn't make much sense to me. Should I do it?

---

## Comment 7

> Username: djowel  
> Created_at: 2015-07-27 13:01:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/140#issuecomment-125198249  

OK, I studied the code a bit more. Pardon my being slow. MaxDigits < 0 is indeed a good idea and that branch will be optimized away by the compiler quite nicely (being const).

---

## Comment 8

> Username: djowel  
> Created_at: 2015-07-27 13:02:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/140#issuecomment-125198502  

Thanks, Andrey.

---
