# #124 - Potential divide by zero? [Closed]

> Username: e4lam  
> Created at: 2020-02-12 03:33:15 UTC  
> Updated at: 2020-03-08 21:08:32 UTC  
> Closed at: 2020-03-07 23:35:04 UTC  
> Url: https://github.com/boostorg/date_time/issues/124  

Reported by the clang static analyzer, we have a potential divide by zero in  
https://github.com/boostorg/date_time/blob/061aec856d4fdb2b53cc17cfa565b4e1c923b62a/include/boost/date_time/int_adapter.hpp#L342  
where we have:  
```  
  int_adapter operator/(const int rhs) const  
  {  
    if(is_special() && rhs != 0)  
    {  
      return mult_div_specials(rhs);  
    }  
    return int_adapter<int_type>(value_ / rhs);  
  }  
```  
However, I'm not sure if this is intentional? If it is, we should at least have a comment here explaining it.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-03-01 23:09:46 UTC  
> Url: https://github.com/boostorg/date_time/issues/124#issuecomment-593161386  

Given how long ago the code was written I'm uncertain of my intent.  What it does do is force a divide by zero instead of going down the special value handling path.  So basically it will behave like and int in all cases if the client calls with a zero.  A comment to that effect would be reasonable.

---

## Comment 2

> Username: e4lam  
> Created at: 2020-03-02 12:55:53 UTC  
> Url: https://github.com/boostorg/date_time/issues/124#issuecomment-593387913  

I see. I'm not sure what the boost policy with regards to this but if we also add a `// NOLINT` on the line with the return statement or a `// NOLINTNEXTLINE` on the line above it, then we can mark this situation as well.

---

## Comment 3

> Username: JeffGarland  
> Created at: 2020-03-03 00:06:13 UTC  
> Url: https://github.com/boostorg/date_time/issues/124#issuecomment-593693451  

Good idea.  I'll add this to work I'm doing in this file for constexpr support (issue #123).

---

## Comment 4

> Username: JeffGarland  
> Created at: 2020-03-07 23:35:04 UTC  
> Url: https://github.com/boostorg/date_time/issues/124#issuecomment-596148422  

This has been merged to develop and should be in the 1.73 release.  Closing the issue.

---

## Comment 5

> Username: e4lam  
> Created at: 2020-03-08 21:08:32 UTC  
> Url: https://github.com/boostorg/date_time/issues/124#issuecomment-596252901  

Thanks!
