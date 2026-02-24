# #48 - detail/voronoi_robust_fpt.hpp [Open]

> Username: LLlKuIIeP  
> Created at: 2020-07-30 13:37:20 UTC  
> Updated at: 2024-01-13 18:28:33 UTC  
> Url: https://github.com/boostorg/polygon/issues/48  

Доброе время суток.  
  
Коллеги из соседнего отдела нашли особенность, там деление на 0 происходит.  
  
182 строка  
```  
  robust_fpt operator-(const robust_fpt& that) const {  
    floating_point_type fpv = this->fpv_ - that.fpv_;  
    relative_error_type re;  
    if ((!is_neg(this->fpv_) && !is_pos(that.fpv_)) ||  
        (!is_pos(this->fpv_) && !is_neg(that.fpv_))) {  
      re = (std::max)(this->re_, that.re_) + ROUNDING_ERROR;  
    } else {  
      floating_point_type temp =  
        0;          //  
      if (fpv != 0) // этот случай в библиотеке не обработан  
        temp =      //  
        (this->fpv_ * this->re_ + that.fpv_ * that.re_) / fpv;  
      if (is_neg(temp))  
        temp = -temp;  
      re = temp + ROUNDING_ERROR;  
    }  
    return robust_fpt(fpv, re);  
  }  
```  
Сможете посмотреть и поправить?

---

## Comment 1

> Username: asydorchuk  
> Created at: 2020-07-31 12:47:54 UTC  
> Url: https://github.com/boostorg/polygon/issues/48#issuecomment-667102485  

Yes, I will look into it.  
  
Да, я посмотрю.

---

## Comment 2

> Username: LLlKuIIeP  
> Created at: 2020-09-29 10:20:58 UTC  
> Url: https://github.com/boostorg/polygon/issues/48#issuecomment-700610783  

Good afternoon.  
  
Are there any successes?

---

## Comment 3

> Username: eadf  
> Created at: 2021-03-24 00:29:54 UTC  
> Url: https://github.com/boostorg/polygon/issues/48#issuecomment-805376732  

Is there really an issue with divide by zero here?   
To me it looks like the err value intentionally is set to infinity whenever additions or subtractions results in zero.  
What took me a while to figure out was if the divide by zero could result in NaN (0/0).   
  
That would be a problem, since NaN compared to any ulp value would be false, but it doesn't look like it can happen

---

## Comment 4

> Username: LLlKuIIeP  
> Created at: 2021-03-29 06:58:54 UTC  
> Updated at: 2021-03-29 13:12:17 UTC  
> Url: https://github.com/boostorg/polygon/issues/48#issuecomment-809123337  

Good day.  
  
Let's connect colleagues to clarify division by zero.

---

## Comment 5

> Username: meretin-as  
> Created at: 2021-03-29 15:17:09 UTC  
> Url: https://github.com/boostorg/polygon/issues/48#issuecomment-809465966  

> Is there really an issue with divide by zero here?  
> To me it looks like the err value intentionally is set to infinity whenever additions or subtractions results in zero.  
> What took me a while to figure out was if the divide by zero could result in NaN (0/0).  
>   
> That would be a problem, since NaN compared to any ulp value would be false, but it doesn't look like it can happen  
  
Problem occur when you test your code with some kind of sanitizers, which check division by zero. So in current code realization sanitizer fails even in correct data because of division. If I understood you right, your suggestion is to use isnan or something like that, but it will not help to solve our problem.

---

## Comment 6

> Username: tjulyz  
> Created at: 2024-01-11 02:10:27 UTC  
> Url: https://github.com/boostorg/polygon/issues/48#issuecomment-1886084630  

Hi！  
I also encountered this problem - "divide by zero".  
`temp =   (this->fpv_ * this->re_ + that.fpv_ * that.re_) / fpv;`  
I have not found why fpv will be zero in some of condition.  If fpv could be zero, how can we fix it?

---

## Comment 7

> Username: eadf  
> Created at: 2024-01-13 18:28:32 UTC  
> Url: https://github.com/boostorg/polygon/issues/48#issuecomment-1890670758  

@tjulyz   
> Hi！ I also encountered this problem - "divide by zero". `temp = (this->fpv_ * this->re_ + that.fpv_ * that.re_) / fpv;` I have not found why fpv will be zero in some of condition. If fpv could be zero, how can we fix it?  
  
Do you have any sample data that generates the fault? That would be really helpful for tracking down the issue.
