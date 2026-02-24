# #101 - why two evaluation results are different? [Closed]

> Username: jojogh  
> Created at: 2020-02-04 07:16:12 UTC  
> Updated at: 2020-04-08 23:19:24 UTC  
> Closed at: 2020-04-08 23:19:24 UTC  
> Url: https://github.com/boostorg/yap/issues/101  

I want to use boost yap to do some small example, the codes are below, why  I put the expression into an operator or member function in a structure, the two evaluation results are different? I am looking forward to your reply, many thanks!  
[new4.cpp.txt](https://github.com/boostorg/yap/files/4151862/new4.cpp.txt)

---

## Comment 1

> Username: jojogh  
> Created at: 2020-02-06 05:45:11 UTC  
> Url: https://github.com/boostorg/yap/issues/101#issuecomment-582747225  

seems no one would answer my question.

---

## Comment 2

> Username: greole  
> Created at: 2020-02-16 17:19:12 UTC  
> Url: https://github.com/boostorg/yap/issues/101#issuecomment-586730573  

It seems like I experience a similar issue when using stateful  terminals created from rvalue references. I'll have look if I can figure out what is going on...

---

## Comment 3

> Username: jojogh  
> Created at: 2020-02-19 05:30:03 UTC  
> Url: https://github.com/boostorg/yap/issues/101#issuecomment-588043529  

many thanks  
  
greole <notifications@github.com> 于2020年2月17日周一 上午1:19写道：  
  
> It seems like I experience a similar issue when using stateful terminals  
> created from rvalue references. I'll have look if I can figure out what is  
> going on...  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/yap/issues/101?email_source=notifications&email_token=AABCZJLIS5YJYPMJEWBPOO3RDFYRBA5CNFSM4KPR2LNKYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOEL4MYTI#issuecomment-586730573>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AABCZJOHJL22ARBCIMEGNFTRDFYRBANCNFSM4KPR2LNA>  
> .  
>

---

## Comment 4

> Username: tzlaine  
> Created at: 2020-04-08 23:19:24 UTC  
> Url: https://github.com/boostorg/yap/issues/101#issuecomment-611242655  

```  
struct eval   
{  
	auto operator() (double a, double b) {  
		auto lhs_w = boost::yap::make_terminal<tarray_expr>(std::move(a));  
		auto rhs_w = boost::yap::make_terminal<tarray_expr>(std::move(b));  
  
		return lhs_w + rhs_w;  
	}  
};  
```  
  
This returns a dangling reference to both lhs_w and rhs_w.  The documentation explains why.  If you std::move them both, as in `return std::move(lhs_w) + std::move(rhs_w);`, your code will work as intended.
