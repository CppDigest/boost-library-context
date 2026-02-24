# #19 - Huge difference between cpp_dec_float_100 and double [Closed]

> Username: andrew-vdb  
> Created at: 2017-03-20 18:41:54 UTC  
> Updated at: 2017-06-15 09:52:40 UTC  
> Closed at: 2017-06-08 09:23:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19  

```  
typedef boost::multiprecision::cpp_dec_float_100 decimal;  
decimal IRR = decimal(0.0227);  
auto x = decimal(-105.0) + decimal(2.0) * (decimal(1.0) / (IRR / decimal(2.0)) - decimal(1.0) / (IRR / decimal(2.0))*(decimal(1.0) / pow((decimal(1.0) + IRR / decimal(2.0)), decimal(6.0)))) + decimal(100.0) / pow((decimal(1.0) + IRR / decimal(2.0)), decimal(6.0));  
//x=111.537  
```  
  
I replace decimal with double with exact and same formula  
```  
double IRR = 0.0227;  
auto x = double(-105.0) + double(2.0) * (double(1.0) / (IRR / double(2.0)) - double(1.0) / (IRR / double(2.0))*(double(1.0) / pow((double(1.0) + IRR / double(2.0)), double(6.0)))) + double(100.0) / pow((double(1.0) + IRR / double(2.0)), double(6.0));  
//x=-0.0100884  
```  
I get the same result with double if I use excel.  
Is there a bug in my formula when I use decimal or is there any other issue?

---

## Comment 1

> Username: ckormanyos  
> Created at: 2017-03-20 21:26:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-287903277  

> Is there a bug in my formula when I use decimal or> is there any other issue?  
I am looking into this.  
Thanks for the report.  
Best regards, Chris  
   
  
    On Monday, March 20, 2017 7:41 PM, andrew-vandenbrink <notifications@github.com> wrote:  
   
  
 typedef boost::multiprecision::cpp_dec_float_100 decimal;  
decimal IRR = decimal(0.0227);  
auto x = decimal(-105.0) + decimal(2.0) * (decimal(1.0) / (IRR / decimal(2.0)) - decimal(1.0) / (IRR / decimal(2.0))*(decimal(1.0) / pow((decimal(1.0) + IRR / decimal(2.0)), decimal(6.0)))) + decimal(100.0) / pow((decimal(1.0) + IRR / decimal(2.0)), decimal(6.0));  
//x=111.537  
I replace decimal with double with exact and same formuladouble IRR = 0.0227;  
auto x = double(-105.0) + double(2.0) * (double(1.0) / (IRR / double(2.0)) - double(1.0) / (IRR / double(2.0))*(double(1.0) / pow((double(1.0) + IRR / double(2.0)), double(6.0)))) + double(100.0) / pow((double(1.0) + IRR / double(2.0)), double(6.0));  
//x=-0.0100884  
I get the same result with double if I use excel.  
Is there a bug in my formula when I use decimal or is there any other issue?—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2017-03-20 23:14:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-287927476  

>> Is there a bug in my formula when I use decimal or>> is there any other issue?  
> I am looking into this.> Thanks for the report.  
There seems to be a problem with the pow function forthese arguments. The result of the pow() term is infinite.Only cpp_dec_float has the problem. Neither cpp_bin_floatnor double suffer from this problem.  
I can try to track this down.  
Thanks for the bug report.  
Best regards, Chris  
  
   
  
    On Monday, March 20, 2017 7:41 PM, andrew-vandenbrink <notifications@github.com> wrote:  
   
  
 typedef boost::multiprecision::cpp_dec_float_100 decimal;  
decimal IRR = decimal(0.0227);  
auto x = decimal(-105.0) + decimal(2.0) * (decimal(1.0) / (IRR / decimal(2.0)) - decimal(1.0) / (IRR / decimal(2.0))*(decimal(1.0) / pow((decimal(1.0) + IRR / decimal(2.0)), decimal(6.0)))) + decimal(100.0) / pow((decimal(1.0) + IRR / decimal(2.0)), decimal(6.0));  
//x=111.537  
I replace decimal with double with exact and same formuladouble IRR = 0.0227;  
auto x = double(-105.0) + double(2.0) * (double(1.0) / (IRR / double(2.0)) - double(1.0) / (IRR / double(2.0))*(double(1.0) / pow((double(1.0) + IRR / double(2.0)), double(6.0)))) + double(100.0) / pow((double(1.0) + IRR / double(2.0)), double(6.0));  
//x=-0.0100884  
I get the same result with double if I use excel.  
Is there a bug in my formula when I use decimal or is there any other issue?—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 3

> Username: andrew-vdb  
> Created at: 2017-03-21 07:24:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-287996688  

Thank you for your confirmation.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2017-03-21 09:44:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-288025813  

On 20/03/2017 23:14, Christopher Kormanyos wrote:  
> >> Is there a bug in my formula when I use decimal or>> is there any   
> other issue?  
> > I am looking into this.> Thanks for the report.  
> There seems to be a problem with the pow function forthese arguments.   
> The result of the pow() term is infinite.Only cpp_dec_float has the   
> problem. Neither cpp_bin_floatnor double suffer from this problem.  
> I can try to track this down.  
  
Thanks for checking this Chris!  
  
Are you using the latest develop - or at least 1.63?  I seem to remember   
there was a bug in the expression template code for pow triggered by   
variable re-use.  
  
HTH, John.  
  
  
  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 5

> Username: andrew-vdb  
> Created at: 2017-03-21 10:00:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-288029813  

I use boost_1_63_0, stable release i think

---

## Comment 6

> Username: jzmaddock  
> Created at: 2017-03-21 12:04:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-288058256  

On 21/03/2017 10:00, andrew-vandenbrink wrote:  
>  
> I use boost_1_63_0, stable release i think  
>  
  
Just tested with current develop and it produces -0.0100884 as   
expected.  I'm not sure which fix is the cure though....  
  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 7

> Username: andrew-vdb  
> Created at: 2017-03-21 12:09:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-288059427  

That's good to hear! But I will wait for stable release of 1.64...

---

## Comment 8

> Username: ckormanyos  
> Created at: 2017-03-21 20:31:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-288209185  

> Thank you for your confirmation.  
Well it seems like this might be fixed in the develop branch.  
But I have made some more progress on a workaroundand explanation.  
The pow function is getting confused in combinationwith auto type inference. This can happen easilybecause the result of a mathematical expressionis an expression template, not the actual numeric type.  
This happens when the multiprecision type has et_on(expression templates on). With et_off, the original codeworks, just not working with et_on.  
  
In addition, the code below makes it more clear to thecompiler what type is actually being inferred with theauto keyword. The workaround also separates some termsand includes some optimizing simplifications. This codeworks with expression templates both on and off.  
Please also note the initialization of IRR. The original codeinitializes from double and will thus be limited to the precisionof double. The initialization of IRR below gets the full precisionof the type.  
I still would like to investigate this further. So let's notclose the issue quite yet...  
Best regards, Chris  
  
  const auto IRR          = decimal(227) / 10000;  
  const auto two_over_IRR = decimal(2 / IRR);  
  const auto one          = decimal(1);  
  
  const auto x1 = decimal(-105);  
  const auto p1 = pow(decimal(one + (IRR / 2)), decimal(6));  
  const auto x2 = decimal(decimal(2) * decimal(two_over_IRR - (two_over_IRR * (one / p1))));  
  const auto x3 = decimal(100) / p1;  
  const auto x  = x1 + x2 + x3;  
  
   
  
    On Tuesday, March 21, 2017 8:24 AM, andrew-vandenbrink <notifications@github.com> wrote:  
   
  
 Thank you for your confirmation.—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 9

> Username: pabristow  
> Created at: 2017-03-22 09:50:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-288348402  

Getting double precision by mistake is a pit into which I have plopped more often than I like to admit.  
It usually bites very, very quietly (if you'll excuse my mixing of metaphors).  
Is there any way in which we can trigger a warning about this?  
Are there big enough skull'n'crossbones warnings in the documentation?

---

## Comment 10

> Username: ckormanyos  
> Created at: 2017-03-22 21:39:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-288548223  

> Getting double precision by mistake is a pit into which> I have plopped more often than I like to admit.  
> Is there any way in which we can trigger a warning about this?  
Yes, this happens quite easily and it's really hard to detectthis error. I'm not sure if the language allows for warningdetecting initialization from a literal floating-point constant.  
  
Actually, I can't see any dedicated paragraph highlitingthis issue in the the multiprecision documentation.Maybe I missed something?  
  
There is a chapter called"Constructing and Interconverting between Number Types".We could consider adding some relevant info here.  
Best regards, Chris.  
  
   
  
    On Wednesday, March 22, 2017 10:50 AM, Paul A. Bristow <notifications@github.com> wrote:  
   
  
 Getting double precision by mistake is a pit into which I have plopped more often than I like to admit.  
It usually bites very, very quietly (if you'll excuse my mixing of metaphors).  
Is there any way in which we can trigger a warning about this?  
Are there big enough skull'n'crossbones warnings in the documentation?—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 11

> Username: pabristow  
> Created at: 2017-03-23 09:42:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-288666065  

I fell straight into this pit when I first used multiprecision and tried to document my naiveity in the section "Constructing ...".  I think the problem is that users are unlikely to find this, (even if they are looking for it!)  
  
Can we devise some sort of BOOST_STATIC_ASSERT_MSG say BOOST_STATIC_MESSAGE_MSG that doesn't  abort?  (That is if you can devise a BOOST_STATIC_ASSERT with some meta-magic?)

---

## Comment 12

> Username: andrew-vdb  
> Created at: 2017-05-26 07:00:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-304207861  

Using 1.6.4 stable release,   
  
```  
const auto IRR = decimal(227) / 10000;  
const auto two_over_IRR = decimal(2 / IRR);  
```  
when using boost::multiprecision::cpp_bin_float_100   
two_over_IRR = 88.1057 //good  
  
when using boost::multiprecision::cpp_dec_float_100   
two_over_IRR = 0.0176211 //bad!!!  
  
Looks like we still have the issue.

---

## Comment 13

> Username: jzmaddock  
> Created at: 2017-05-31 18:21:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-305273901  

There's a bug in your code: variable IRR is not a value, it's an expression template that contains a reference to a temporary - by the time you get to the second expression, that temporary has been destroyed and you're accessing invalid memory.  
  
Had you written:  
  
   decimal d(227);  
   const auto IRR = d / 10000;  
   const auto two_over_IRR = decimal(2 / IRR);  
  
Then everything is fine.  
  
That's why in the introduction the docs say:  
  
" One other potential pitfall that's only possible in C++11: you should never store an expression template using:  
auto my_expression = a + b - c;  
unless you're absolutely sure that the lifetimes of a, b and c will outlive that of my_expression. "

---

## Comment 14

> Username: ckormanyos  
> Created at: 2017-05-31 21:27:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-305322803  

Actually this issue might originate from my sample code.  
  
I wonder if, in time, we could work out some best-practices  
in the area of type inference and expression templates?  
  
You could probably also write:  
`const auto IRR = decimal(decimal(227) / 10000);`  
  
I think John's observation is correct.  
Sorry about adding to the confusion in my previous  
post.  
  
Best regards, Chris.

---

## Comment 15

> Username: andrew-vdb  
> Created at: 2017-06-01 06:28:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-305401069  

![image](https://cloud.githubusercontent.com/assets/12871379/26667196/48e72392-46a4-11e7-86c6-659ce8691aaf.png)  
  
![image](https://cloud.githubusercontent.com/assets/12871379/26667174/2ac16526-46a4-11e7-9965-652648549d16.png)  
  
![image](https://cloud.githubusercontent.com/assets/12871379/26667188/3563aafc-46a4-11e7-966a-3931aed5e7d0.png)  
  
It still give incorrect result...

---

## Comment 16

> Username: jzmaddock  
> Created at: 2017-06-01 17:37:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-305565729  

Interestingly I can only reproduce in release mode, but it's the same issue:  
  
const auto IRR = d / 10000;  
  
Generates a dangling reference to the integer literal 10000.  Then when you subsequently try to use that expression, it reads garbage.  
  
Conceivably, I could fix that by storing built in float and integer types by value in the expression template (in fact I will do so), but that doesn't change the fact that mixing auto with expression templates is in general a really bad idea.  Plus we wouldn't want to store larger type directly in the expression template as they're _supposed_ to be trivially-small-cheap-to-copy types.

---

## Comment 17

> Username: ckormanyos  
> Created at: 2017-06-01 20:30:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-305611241  

Thanks for addressing this issue John.  
  
Just out of curiosity.... Does this work with?  
  
`const auto IRR = decimal(d / 10000);`  
  
If mixing type inference with expression templates  
might often lead to confusion, perhaps we should  
be even more clear about this in the docs?  
  
Best regards, Chris

---

## Comment 18

> Username: jzmaddock  
> Created at: 2017-06-03 08:24:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-305960493  

Fixed in https://github.com/boostorg/multiprecision/pull/25.  
  
Chris, your case of:  
  
const auto IRR = decimal(d / 10000);  
  
Will work with the above fix, but not before since it creates a dangling reference to the integer literal.  
  
Leaving open till I've updated the docs...

---

## Comment 19

> Username: pabristow  
> Created at: 2017-06-05 15:46:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-306223498  

Expression templates are very useful, but a big pit awaiting the unwary (mea culpa), especially as it seems that some expressions may work with some compilers depending on settings.  Can I suggest several more examples of what works and what doesn't to ease brain pain from considering potentially dangling references.

---

## Comment 20

> Username: ckormanyos  
> Created at: 2017-06-06 22:36:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-306637175  

Thanks John. Yes, I understand.

---

## Comment 21

> Username: jzmaddock  
> Created at: 2017-06-08 09:23:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-307048942  

Documentation updated: https://github.com/boostorg/multiprecision/commit/0b77b830d66178b0171c2de72c1f9e9a48bb5ba4

---

## Comment 22

> Username: andrew-vdb  
> Created at: 2017-06-08 13:10:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-307098376  

I confirm this working fine (1.64 stable release)  
```  
decimal d(227);  
const decimal IRR = d / 10000;  
const decimal two_over_IRR = decimal(2 / IRR);  
```  
  
There is also strange case where this statement have AcceesViolation being thrown  
```  
return std::make_pair(decimal(-105) + decimal(2) * (decimal(1) / (IRR / decimal(2)) - decimal(1) / (IRR / decimal(2))*(decimal(1) / pow((decimal(1) + IRR / decimal(2)), decimal(6)))) + decimal(100) / pow((decimal(1) + IRR / decimal(2)), decimal(6)),  
					-((decimal(4) * (pow(IRR, decimal(5)) + decimal(14) * pow(IRR, decimal(4))+ decimal(84)*pow(IRR, decimal(3))+ decimal(280)*pow(IRR, decimal(2))+ decimal(560)*IRR+ decimal(19872)))/pow((IRR+ decimal(2)), decimal(7)))  
				);   
```  
![image](https://user-images.githubusercontent.com/12871379/26930110-84985542-4c5c-11e7-8b73-f60ea1377761.png)  
  
  
but if I do it like this  
```  
std::pair<decimal,decimal> z=std::make_pair(decimal(-105) + decimal(2) * (decimal(1) / (IRR / decimal(2)) - decimal(1) / (IRR / decimal(2))*(decimal(1) / pow((decimal(1) + IRR / decimal(2)), decimal(6)))) + decimal(100) / pow((decimal(1) + IRR / decimal(2)), decimal(6)),  
					-((decimal(4) * (pow(IRR, decimal(5)) + decimal(14) * pow(IRR, decimal(4))+ decimal(84)*pow(IRR, decimal(3))+ decimal(280)*pow(IRR, decimal(2))+ decimal(560)*IRR+ decimal(19872)))/pow((IRR+ decimal(2)), decimal(7)))  
				);   
return z;  
```  
It's working fine

---

## Comment 23

> Username: jzmaddock  
> Created at: 2017-06-08 17:22:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-307170739  

I'll need a complete, self contained test case to look into that, as the extracts you give both work fine for me, for example:  
  
std::pair<decimal, decimal> foo()  
{  
   decimal d(227);  
   const decimal IRR = d / 10000;  
   const decimal two_over_IRR = decimal(2 / IRR);  
  
   return (std::make_pair(decimal(-105) + decimal(2) * (decimal(1) / (IRR / decimal(2)) - decimal(1) / (IRR / decimal(2))*(decimal(1) / pow((decimal(1) + IRR / decimal(2)), decimal(6)))) + decimal(100) / pow((decimal(1) + IRR / decimal(2)), decimal(6)),  
      -((decimal(4) * (pow(IRR, decimal(5)) + decimal(14) * pow(IRR, decimal(4)) + decimal(84)*pow(IRR, decimal(3)) + decimal(280)*pow(IRR, decimal(2)) + decimal(560)*IRR + decimal(19872))) / pow((IRR + decimal(2)), decimal(7)))));  
}  
  
works just fine in vc2017.

---

## Comment 24

> Username: andrew-vdb  
> Created at: 2017-06-15 06:35:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-308642296  

```  
decimal actual = MathAPI::NewtownRaphson(  
				[](decimal const& IRR) {	  
				std::pair<decimal,decimal> z = std::make_pair(decimal(-105) + decimal(2) * (decimal(1) / (IRR / decimal(2)) - decimal(1) / (IRR / decimal(2))*(decimal(1) / pow((decimal(1) + IRR / decimal(2)), decimal(6)))) + decimal(100) / pow((decimal(1) + IRR / decimal(2)), decimal(6)),  
					-((decimal(4) * (pow(IRR, decimal(5)) + decimal(14) * pow(IRR, decimal(4))+ decimal(84)*pow(IRR, decimal(3))+ decimal(280)*pow(IRR, decimal(2))+ decimal(560)*IRR+ decimal(19872)))/pow((IRR+ decimal(2)), decimal(7)))  
				);   
				return z;  
			},  
				decimal(-0.1), decimal(-10), decimal(10)  
			);  
```  
  
```  
template <typename F, typename T>  
	static T  MathAPI::NewtownRaphson(const F& f, const T& guess, const T& min, const T& max)  
	{  
		const int digits = std::numeric_limits<T>::digits;  // Maximum possible binary digits accuracy for type T.  
		int get_digits = static_cast<int>(digits * 0.6);    // Accuracy doubles with each step, so stop when we have  
		const boost::uintmax_t maxit = 100;  
		boost::uintmax_t it = maxit;  
		  
		return boost::math::tools::newton_raphson_iterate(f, guess, min, max, get_digits, it);  
	}	  
```  
  
As the issue already closed, I don't really want to continue the issue...  
The code have function inside functor and thats where the issue happens...

---

## Comment 25

> Username: jzmaddock  
> Created at: 2017-06-15 08:13:44 UTC  
> Updated at: 2017-06-15 08:15:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-308661627  

I tried the following test case using your code above, but I'm unable to reproduce with either Boost-1.64 or current develop, so I suspect there's something else going on here.... like I said, I would need a complete self contained test case to investigate:  
```  
  
typedef boost::multiprecision::cpp_dec_float_100 decimal;  
  
template <typename F, typename T>  
T  NewtownRaphson(const F& f, const T& guess, const T& min, const T& max)  
{  
   const int digits = std::numeric_limits<T>::digits;  // Maximum possible binary digits accuracy for type T.  
   int get_digits = static_cast<int>(digits * 0.6);    // Accuracy doubles with each step, so stop when we have  
   const boost::uintmax_t maxit = 100;  
   boost::uintmax_t it = maxit;  
  
   return boost::math::tools::newton_raphson_iterate(f, guess, min, max, get_digits, it);  
}  
  
int main()  
{  
   decimal actual = NewtownRaphson(  
      [](decimal const& IRR) {  
      std::pair<decimal, decimal> z = std::make_pair(decimal(-105) + decimal(2) * (decimal(1) / (IRR / decimal(2)) - decimal(1) / (IRR / decimal(2))*(decimal(1) / pow((decimal(1) + IRR / decimal(2)), decimal(6)))) + decimal(100) / pow((decimal(1) + IRR / decimal(2)), decimal(6)),  
         -((decimal(4) * (pow(IRR, decimal(5)) + decimal(14) * pow(IRR, decimal(4)) + decimal(84)*pow(IRR, decimal(3)) + decimal(280)*pow(IRR, decimal(2)) + decimal(560)*IRR + decimal(19872))) / pow((IRR + decimal(2)), decimal(7)))  
      );  
      return z;  
   }, decimal(-0.1), decimal(-10), decimal(10) );  
   std::cout << actual << std::endl;  
   return 0;  
}  
  
```

---

## Comment 26

> Username: andrew-vdb  
> Created at: 2017-06-15 08:26:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-308664524  

I sent you the working version of code...  
This one is not working due direct return instead of putting the result in variable z  
```  
decimal actual = MathAPI::NewtownRaphson(  
				[](decimal const& IRR) {					  
				return std::make_pair(decimal(-105) + decimal(2) * (decimal(1) / (IRR / decimal(2)) - decimal(1) / (IRR / decimal(2))*(decimal(1) / pow((decimal(1) + IRR / decimal(2)), decimal(6)))) + decimal(100) / pow((decimal(1) + IRR / decimal(2)), decimal(6)),  
					-((decimal(4) * (pow(IRR, decimal(5)) + decimal(14) * pow(IRR, decimal(4))+ decimal(84)*pow(IRR, decimal(3))+ decimal(280)*pow(IRR, decimal(2))+ decimal(560)*IRR+ decimal(19872)))/pow((IRR+ decimal(2)), decimal(7)))  
				);   
				  
			},  
				decimal(-0.1), decimal(-10), decimal(10)  
			);  
```

---

## Comment 27

> Username: jzmaddock  
> Created at: 2017-06-15 09:52:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/19#issuecomment-308685143  

On 15/06/2017 09:26, andrew-vandenbrink wrote:  
>  
> I sent you the working version of code...  
> This one is not working due direct return instead of putting the   
> result in variable z  
>  
  
This is the same issue as above: the return type of the lambda is   
"auto", and the make_pair returns a pair of expression templates which   
reference variables that will be out of scope once the function returns.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com
