# #98 - what is the return value type to replace the auto obscure [Closed]

> Username: jojogh  
> Created at: 2020-01-13 07:10:26 UTC  
> Updated at: 2020-04-08 23:07:21 UTC  
> Closed at: 2020-04-08 23:07:21 UTC  
> Url: https://github.com/boostorg/yap/issues/98  

I want to write a small expression compiler by using boost spirit x3 and yap. The idea are like these:  
1.  Write a parser to parser the syntax into an AST;  
2. Than compile the ast into the expression, like A = B(a,b) + C(a,b) - 1.2;  
3. When needed, pass the expression to where it is needed, and use yap evaluate to get the result.  
  
Following is my code to implement some ideas. However, i met a problem in the eval struct operator, like following. I just want to know if i use this pattern, what exactly the return value is(not auto), to adapt plus and minus expression template? thanks.....   
  
			auto operator()(operation const& x,int rhs, int lhs) const  
			{  
				//int rhs = boost::apply_visitor(*this, x.operand_);  
				auto rhs_w = boost::yap::make_terminal<tarray_expr>(rhs);  
				auto lhs_w = boost::yap::make_terminal<tarray_expr>(lhs);  
				switch (x.operator_)  
				{  
				case '+': return lhs_w + rhs_w;  
				case '-': return lhs_w - rhs_w;  
				}  
				//return rhs;  
			}  
  
 I re-editted the post, and attached the code as a txt file, and looking forward for the help, thanks!  
[main.cpp.txt](https://github.com/boostorg/yap/files/4075189/main.cpp.txt)

---

## Comment 1

> Username: tzlaine  
> Created at: 2020-04-08 23:07:21 UTC  
> Url: https://github.com/boostorg/yap/issues/98#issuecomment-611239038  

This is really for bug reporting, not advice.  However, at least one of the YAP examples will probably help; you should start there.
