# #19 - Coverity static analysis defects (Trac ticket #13331) [Closed]

> Username: mloskot  
> Created at: 2018-09-21 08:53:07 UTC  
> Updated at: 2018-09-21 18:42:24 UTC  
> Closed at: 2018-09-21 17:00:35 UTC  
> Url: https://github.com/boostorg/function/issues/19  

(copied from https://svn.boost.org/trac10/ticket/13331, only Function's parts from the attached report)  
  
Boost 1.57.0  
  
A static analysis tool called Coverity found medium and high defects in the boost source code. See attached file for defect type, defect category, filename and line number of defect.   
  
Defect   Type | Defect Category | Line Number | Filename  
-- | -- | -- | --  
Same on   both sides | Incorrect expression | 939 | /boost/include/boost/function/function_template.hpp

---

## Comment 1

> Username: mclow  
> Created at: 2018-09-21 12:10:05 UTC  
> Url: https://github.com/boostorg/function/issues/19#issuecomment-423510090  

I think we need more information here; there's already a Coverity warning suppression here (line 938), and the code on 939 doesn't have a "both sides"; it's an `if (expr && expr && expr)` expression.  
  
        // coverity[pointless_expression]: suppress coverity warnings on apparant if(const).  
        if (boost::has_trivial_copy_constructor<Functor>::value &&  
            boost::has_trivial_destructor<Functor>::value &&  
            boost::detail::function::function_allows_small_object_optimization<Functor>::value)  
  
Alternately, this report refers to an older version of boost, where the code is different.

---

## Comment 2

> Username: mloskot  
> Created at: 2018-09-21 12:19:27 UTC  
> Url: https://github.com/boostorg/function/issues/19#issuecomment-423512296  

I've copied the missing detail from the Track ticket: it was Boost 1.57.0

---

## Comment 3

> Username: mclow  
> Created at: 2018-09-21 16:13:42 UTC  
> Url: https://github.com/boostorg/function/issues/19#issuecomment-423590023  

That's the same line in Boost 1.57.0, and the coverity suppression line was there as well in 1.57.0

---

## Comment 4

> Username: mloskot  
> Created at: 2018-09-21 16:34:41 UTC  
> Url: https://github.com/boostorg/function/issues/19#issuecomment-423596895  

Apparently, OP did not verified that. I have also failed to do so before copying the ticket.  
  
I guess, this can be closed (the original Trac ticket has been closed).

---

## Comment 5

> Username: mclow  
> Created at: 2018-09-21 17:00:35 UTC  
> Url: https://github.com/boostorg/function/issues/19#issuecomment-423605439  

Closing.

---

## Comment 6

> Username: jeking3  
> Created at: 2018-09-21 18:35:36 UTC  
> Url: https://github.com/boostorg/function/issues/19#issuecomment-423632660  

Coverity Scan runs regularly on this repository: https://scan.coverity.com/projects/boostorg-function

---

## Comment 7

> Username: mloskot  
> Created at: 2018-09-21 18:42:24 UTC  
> Url: https://github.com/boostorg/function/issues/19#issuecomment-423634543  

@mclow Thanks
