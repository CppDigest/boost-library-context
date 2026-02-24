# #141 Correct pointer and reference formatting in .clang-format. [Closed]

> Username: jzmaddock  
> Created at: 2019-07-15 11:26:09 UTC  
> Updated at: 2021-01-30 16:09:36 UTC  
> Closed at: 2019-07-15 17:04:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/141  



---

## Comment 1

> Username: pabristow  
> Created_at: 2019-07-15 13:46:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/141#issuecomment-511408989  

Some spaces introduced are a bit/very odd.  
  
For example  
  
performance/cpp_bin_float_conversion_performance.cpp  line 24  
  
- typedef int e_type;  
+ typedef int                   e_type;  
  
but several others that are even more odd.  
  
:-(

---

## Comment 2

> Username: NAThompson  
> Created_at: 2019-07-15 14:21:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/141#issuecomment-511422807  

@jzmaddock : Do you want to put the same `.clang-format` into boost.math once you hammer out the style issues you dislike?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2019-07-15 16:53:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/141#issuecomment-511482067  

> performance/cpp_bin_float_conversion_performance.cpp line 24  
  
If you look at the actual formatted file rather than the diff, it's not quite so bad:  
  
```  
   typedef int                   e_type;  
   static boost::random::mt19937 gen;  
   T                             val      = gen();  
   T                             prev_val = -1;  
```  
  
This is because I turned on various alignment options:  
  
```  
AlignConsecutiveAssignments: true  
AlignConsecutiveDeclarations: true  
AlignEscapedNewlines: Left  
AlignOperands: true  
AlignTrailingComments: true  
```  
  
Which mostly produces rather nice results, but can make a bit of a mess of things if you have one typename that's much longer than the others.  
  
Note that these options only effect consecutive statements, so for example in this situation, I added a couple of newlines, removed the clang-format introduced spacing and re-ran clang-format, and the result was way better.  In this situation, since we have 3 different things (A typedef, a static member, and 2 regular members, the extra newlines actually improve readability anyway IMO.  I don't propose to read through all the changes looking for others that could be improved, but it's something that could be corrected as and when maintenance is carried out on specific files (or if there are other shockers that are brought to my attention!)

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2019-07-15 16:54:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/141#issuecomment-511482515  

Ooops, here's the improved version after adding newline:  
  
```  
template <class T>  
T generate_random()  
{  
   typedef int e_type;  
  
   static boost::random::mt19937 gen;  
  
   T val      = gen();  
   T prev_val = -1;  
  
   while (val != prev_val)  
   {  
      val *= (gen.max)();  
      prev_val = val;  
      val += gen();  
   }  
   e_type e;  
   val = frexp(val, &e);  
  
   static boost::random::uniform_int_distribution<e_type> ui(-20, 20);  
   return ldexp(val, ui(gen));  
}  
```  
  
Will push shortly.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2019-07-15 16:57:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/141#issuecomment-511483533  

>Do you want to put the same .clang-format into boost.math once you hammer out the style issues you dislike?  
  
I'm not sure, what do you think?  
  
I do notice that all the regression tests are currently broken with this change - either clang-format is not as robust as we think or I've messed up somewhere.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2019-07-15 17:04:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/141#issuecomment-511486171  

The failures are caused by sorting of #includes, which for this library is a very bad idea, as many detail headers are not self-contained (nor are they intended to be), but rather part of a larger whole split up for readability.  I might have to close this and try again....

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2019-07-15 17:27:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/141#issuecomment-511494569  

second try: https://github.com/boostorg/multiprecision/pull/142

---

## Comment 8

> Username: NAThompson  
> Created_at: 2019-07-15 18:02:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/141#issuecomment-511507841  

> I'm not sure, what do you think?  
  
I've never been that picky about formatting. So for now, I guess it's probably not a priority.

---

## Comment 9

> Username: pabristow  
> Created_at: 2019-07-16 08:31:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/141#issuecomment-511720190  

The indenting is not a shocker, but I really don’t like it.  
  
And the reason I think it is bad is that:  
  
1 We DO have typenames that are widely varying in length, leading to some odd-looking long indents.    
  
2  Most important, it is like the spurious tidiness of left and right justification, despite research showing that ragged right is easier to read. The reason proposed is that the eye can use the ‘raggedness’ to keep focused on the line being read more easily.  And I think this is true.    
  
Aside I think that the same is true about comment alignment.   
Color is a much better way to separate comments (though tough on the chromatically challenged 😉 )  
Keep things that are linked together, I say …

---
