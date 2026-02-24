# #102 - Is that a bug or I misunderstood it? [Closed]

> Username: jojogh  
> Created at: 2020-02-08 11:47:35 UTC  
> Updated at: 2020-04-13 12:40:29 UTC  
> Closed at: 2020-04-08 23:21:11 UTC  
> Url: https://github.com/boostorg/yap/issues/102  

I made a small test on the yap, I made two simple plus expression templates under minial template example case. One is directly in a main function, and another is in a struct operator. As we expected, both results should be the small. In fact, it is not, if called the struct operator, the result is not 42, but a wired number.   
Is that a bug or I misunderstood it, and made wrong setting? I really looking forward to your replys, many thanks! @tzlaine @danieljames   
  
   
```  
#include <boost/yap/algorithm.hpp>  
  
#include <array>  
#include <iostream>  
  
  
//[ minimal_template  
template <boost::yap::expr_kind Kind, typename Tuple>  
struct minimal_expr  
{  
	static const boost::yap::expr_kind kind = Kind;  
  
	Tuple elements;  
};  
//]  
  
struct eval {  
	auto operator() (const double a, const double b) const {  
		const auto lhs_w = boost::yap::make_terminal<minimal_expr>(std::move(a));  
		const auto rhs_w = boost::yap::make_terminal<minimal_expr>(std::move(b));  
		const auto expr = boost::yap::make_expression<  
			minimal_expr, boost::yap::expr_kind::plus  
		>(lhs_w, rhs_w);  
  
		return expr;  
	}  
};  
  
  
int main()  
{  
	//[ minimal_template_manual_construction  
	auto left = boost::yap::make_terminal<minimal_expr>(1);  
	auto right = boost::yap::make_terminal<minimal_expr>(41);  
  
	auto expr = boost::yap::make_expression<  
		minimal_expr,  
		boost::yap::expr_kind::plus	>(left, right);  
	//]  
  
	//[ minimal_template_evaluation  
	auto result = boost::yap::evaluate(expr);  
  
	std::cout << result << "\n"; // prints "42"  
//]  
	eval Eval;  
	auto ex = Eval(1, 41);  
	auto res = boost::yap::evaluate(ex);  
	std::cout << res << "\n"; // prints "42"  
	system("pause");  
	return 0;  
}  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2020-04-08 23:21:11 UTC  
> Url: https://github.com/boostorg/yap/issues/102#issuecomment-611243184  

In `eval::operator()`, `return expr;` returns an expression whose subexpressions are lvalue references to locals.  You have a dangling reference problem.

---

## Comment 2

> Username: jojogh  
> Created at: 2020-04-13 12:40:28 UTC  
> Url: https://github.com/boostorg/yap/issues/102#issuecomment-612882021  

many thanks  
  
Zach Laine <notifications@github.com> 于2020年4月9日周四 上午7:21写道：  
  
> Closed #102 <https://github.com/boostorg/yap/issues/102>.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/yap/issues/102#event-3216193827>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AABCZJKRGYRK4ESO73GEVWTRLUBHJANCNFSM4KR2AW4A>  
> .  
>
