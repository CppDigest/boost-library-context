# #38 - Proposal: Jump table based O(1) mp_with_index [Closed]

> Username: lennoxho  
> Created at: 2019-08-09 17:03:10 UTC  
> Updated at: 2019-08-14 09:06:51 UTC  
> Closed at: 2019-08-14 09:06:51 UTC  
> Url: https://github.com/boostorg/mp11/issues/38  

Example implementation:  
https://godbolt.org/z/AFZjKd  
  
This would be very useful for boost::variant:  
https://godbolt.org/z/sliiP3

---

## Comment 1

> Username: pdimov  
> Created at: 2019-08-09 18:07:30 UTC  
> Url: https://github.com/boostorg/mp11/issues/38#issuecomment-520012769  

https://godbolt.org/z/NgHa1o

---

## Comment 2

> Username: pdimov  
> Created at: 2019-08-09 18:12:34 UTC  
> Url: https://github.com/boostorg/mp11/issues/38#issuecomment-520014289  

Compilers are pretty good at optimizing `switch` nowadays, turning it into a (jump) table, if needed: https://godbolt.org/z/2HScsH https://godbolt.org/z/9WStAe

---

## Comment 3

> Username: pdimov  
> Created at: 2019-08-09 18:15:22 UTC  
> Url: https://github.com/boostorg/mp11/issues/38#issuecomment-520015171  

And here's the variant example: https://godbolt.org/z/IifWbw

---

## Comment 4

> Username: lennoxho  
> Created at: 2019-08-09 21:59:01 UTC  
> Updated at: 2019-08-09 22:02:01 UTC  
> Url: https://github.com/boostorg/mp11/issues/38#issuecomment-520077274  

Thanks for taking a look.  
I agree that compilers are pretty good at identifying opportunities to create jump tables.  
  
However, my proposal was motivated by the observation that in less-than-trivial cases, the compiler more often than not is not able to make that deduction.  
  
For example:  
https://godbolt.org/z/FSHzKF  
  
This is probably an extreme example, but I think it perfectly shows how the compiler can fail to see the obvious:  
https://godbolt.org/z/lyPgKb  
  
I believe there is an opportunity to take the uncertainty in the compiler's ability to optimise switch statements out of the equation.

---

## Comment 5

> Username: pdimov  
> Created at: 2019-08-09 22:24:56 UTC  
> Url: https://github.com/boostorg/mp11/issues/38#issuecomment-520083426  

Manually creating the jump table is not a guaranteed win. In many practical scenarios the compiler can optimize the switch better. I've chosen to use a switch. The gcc team has proven responsive to bug reports about switch not generating optimal code, f.ex. https://gcc.gnu.org/bugzilla/show_bug.cgi?id=87205

---

## Comment 6

> Username: lennoxho  
> Created at: 2019-08-12 22:25:45 UTC  
> Url: https://github.com/boostorg/mp11/issues/38#issuecomment-520618000  

I can see how it makes sense to delegate the jump table problem to the compiler writers.  
  
Do you think there's still any value in providing an alternative function to mp_with_index which manually creates a jump table? It may be useful for developers who want that guarantee - "O(1)" instead of "O(n) or O(1), really depends on your compiler".  
  
Feel free to close the issue if you think we shouldn't pursue that idea.
