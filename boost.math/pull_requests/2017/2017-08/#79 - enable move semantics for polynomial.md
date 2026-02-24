# #79 enable move semantics for polynomial [Merged]

> Username: lakshayg  
> Created at: 2017-08-14 06:16:08 UTC  
> Updated at: 2017-12-08 16:32:12 UTC  
> Merged at: 2017-08-20 08:42:13 UTC  
> Closed at: 2017-08-20 08:42:13 UTC  
> Url: https://github.com/boostorg/math/pull/79  

This patch is to enable move semantics in boost polynomial. I will be updating in small patches so that I can get feedback on how to move forward and stay consistent with boost standards. Any feedback/advice in appreciated.

---

## Review 1 [Commented]

> Username: lakshayg  
> Created_at: 2017-08-14 09:34:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/79#pullrequestreview-56033371  

📁 include/boost/math/tools/polynomial.hpp

```diff
 294 |+    polynomial(polynomial&& p)
 295 |+       : m_data(std::move(p.m_data)) { }
 296 |+ 
```

> Username: lakshayg  
> Created_at: 2017-08-14 09:34:13 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r132909299  

Is there a way to detect if C++11 is supported and disable this constructor if it is not?

> Username: lakshayg  
> Created_at: 2017-08-14 10:13:07 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r132916570  

Just found out about BOOST_NO_RVALUE_REFERENCES in Boost.Config.


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-08-14 10:57:40 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-322161387  

On 14/08/2017 11:13, Lakshay Garg wrote:  
>  
> *@lakshayg* commented on this pull request.  
>  
> ------------------------------------------------------------------------  
>  
> In include/boost/math/tools/polynomial.hpp   
> <https://github.com/boostorg/math/pull/79#discussion_r132916570>:  
>  
> > @@ -290,6 +290,10 @@ class polynomial :  
>             m_data.push_back(point);  
>      }  
>     
> +   // move:  
> +   polynomial(polynomial&& p)  
> +      : m_data(std::move(p.m_data)) { }  
> +  
>  
> Just found out about BOOST_NO_RVALUE_REFERENCES in Boost.Config.  
>  
  
Nod, I would prefer to keep C++03 support if possible, some more   
comments will follow soon....  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 3

> Username: lakshayg  
> Created_at: 2017-08-14 11:00:09 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-322161806  

@jzmaddock I amended the commit a few minutes ago. It now checks if Rvalue references are supported.

---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2017-08-14 11:02:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/79#pullrequestreview-56050230  

📁 include/boost/math/tools/polynomial.hpp

```diff
 565 |-    return result;
 567 |+    a += b;
 568 |+    return a;
```

> Username: jzmaddock  
> Created_at: 2017-08-14 11:02:57 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r132924745  

I'm not completely certain about this, but I believe we need a:  
  
return std::move(a);  
  
Here to be sure that we don't make an accidental copy.  If the function is expanded inline it will make not one jot of difference of course, but IMO we can't rely on that.  
  
To make it C++03 compatible we would need something like:  
  
#ifndef BOOST_NO_RVALUE_REFERENCES  
#  define BOOST_MATH_MOVE(x) std::move(x)  
#else   
#  define BOOST_MATH_MOVE(x) x  
#endif    
  
Then   
  
return BOOST_MATH_MOVE(a);

> Username: jzmaddock  
> Created_at: 2017-08-14 11:05:34 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r132925182  

Sorry about the messed up formatting, haven't figured this stupid interface out yet :(

> Username: lakshayg  
> Created_at: 2017-08-14 11:12:17 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r132926235  

I benchmarked this code and this seems to be working fine. If you still insist then I will go ahead and change it to `return std::move(a);`. Please let me know.

> Username: lakshayg  
> Created_at: 2017-08-14 12:03:45 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r132934165  

I think we don't need to `return std::move(...)`. Please see: https://youtu.be/AKtHxKJRwp4?t=38m3s and let me know if I interpreted something incorrectly.


---

## Review 5 [Commented]

> Username: glenfe  
> Created_at: 2017-08-14 11:28:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/79#pullrequestreview-56051506  

📁 include/boost/math/tools/polynomial.hpp

```diff
 291 |    }
 292 | 
 293 |+ #if !defined(BOOST_NO_RVALUE_REFERENCES)
```

> Username: glenfe  
> Created_at: 2017-08-14 11:10:39 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r132925970  

That is the deprecated macro; use BOOST_NO_CXX11_RVALUE_REFERENCES

> Username: lakshayg  
> Created_at: 2017-08-14 11:30:44 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r132929052  

Done! Thanks.


---

## Review 6 [Commented]

> Username: jzmaddock  
> Created_at: 2017-08-14 11:56:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/79#pullrequestreview-56059061  

📁 include/boost/math/tools/polynomial.hpp

```diff
 578 | template <class T>
 577 |- inline polynomial<T> operator * (const polynomial<T>& a, const polynomial<T>& b)
 579 |+ inline polynomial<T> operator * (polynomial<T> a, const polynomial<T>& b)
```

> Username: jzmaddock  
> Created_at: 2017-08-14 11:56:49 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r132933129  

I think we need to be careful about changing the parameter type to an object type rather than an lvalue-ref.  
  
We have 4 possibilities here:  
  
2 l-value refs: need a new temporary for the result.  
left r-value ref, right l-value ref: reuse left argument for the result.  
right r-value ref, left lvalue ref: reuse right argument for the result (except division!)  
both rvalue-refs: reuse either for the result.  
  
Your change handles the first two but not the last two, and in order to avoid overload ambiguities, I *think* the only way to do this right is to provide all 4 overloads.  I realise that sucks, but it does work well (or at least it did for Boost.Multiprecision when I did this there).  
  
For multiplication, I think we should also rewrite the *= operator to carry out 2-arg multiplication since there isn't really an efficient in-place multiplication, something like:  
  
`  
void multiply(const Polynomial& a, const polynomial& b)  
{  
  // allocates temporary vector for result, then swaps it into *this.  
}  
`  
  
The code would be much the same as now, but I think we can avoid at least one copy doing this when we're doing 2 arg multiplication and both in-place  and 2 arg multiplication can simply call the above routine?

> Username: lakshayg  
> Created_at: 2017-08-14 12:18:18 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r132936753  

I just created the overloads  
  
```  
operator+(const polynomial<T>&, const polynomial<T>&)  
operator+(const polynomial<T>&, polynomial<T>&&)  
operator+(polynomial<T>&&, const polynomial<T>&)  
operator+(polynomial<T>&&, polynomial<T>&&)  
```  
This causes the following code to use the overload with both const lvalue refs  
```  
polynomial<double> p{1, 2, 3};  
polynomial<double> q{1, 2, 3};  
auto sum = p + q + p;  
```  
  
Which in my opinion is undesirable. Please advise. @jzmaddock

> Username: lakshayg  
> Created_at: 2017-08-14 12:29:36 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r132938545  

I could not understand what you said about `operator *=`, could you please explain?


---

## Comment 7

> Username: jzmaddock  
> Created_at: 2017-08-14 17:55:30 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-322261328  

> I think we don't need to |return std::move(...)|. Please see:   
> https://youtu.be/AKtHxKJRwp4?t=38m3s and let me know if I interpreted   
> something incorrectly.  
>  
  
No you're quite right!  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 8

> Username: lakshayg  
> Created_at: 2017-08-14 17:57:18 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-322261844  

@jzmaddock Is this ready to be merged then? Or do you see any further improvements which can be made?

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2017-08-14 18:00:46 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-322262753  

> I think we don't need to |return std::move(...)|. Please see:   
> https://youtu.be/AKtHxKJRwp4?t=38m3s and let me know if I interpreted   
> something incorrectly.  
>  
  
Forgot to say.... as long as we're returning a named value.  If we   
condense things down to:  
  
T foo(T a, T b)  
{  
   return a += b;  
}  
  
Then we do need a move.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 10

> Username: lakshayg  
> Created_at: 2017-08-14 18:02:41 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-322263251  

Yes right, but I prefer to keep it this way. It is one extra line but looks cleaner to the eye.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2017-08-14 18:15:34 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-322266646  

On 14/08/2017 13:18, Lakshay Garg wrote:  
>  
> *@lakshayg* commented on this pull request.  
>  
> ------------------------------------------------------------------------  
>  
> In include/boost/math/tools/polynomial.hpp   
> <https://github.com/boostorg/math/pull/79#discussion_r132936753>:  
>  
> >  }  
>     
>   template <class T>  
> -inline polynomial<T> operator * (const polynomial<T>& a, const polynomial<T>& b)  
> +inline polynomial<T> operator * (polynomial<T> a, const polynomial<T>& b)  
>  
> I just created the overloads  
>  
> |operator+(const polynomial<T>&, const polynomial<T>&) operator+(const   
> polynomial<T>&, polynomial<T>&&) operator+(polynomial<T>&&, const   
> polynomial<T>&) operator+(polynomial<T>&&, polynomial<T>&&) |  
>  
> This causes the following code to use the overload with both const   
> lvalue refs  
>  
> |polynomial<double> p{1, 2, 3}; polynomial<double> q{1, 2, 3}; auto sum   
> = p + q + p; |  
>  
> Which in my opinion is undesirable. Please advise. @jzmaddock   
> <https://github.com/jzmaddock>  
>  
  
You should see the 2 lvalue ref overload called first - and that's   
correct, at some point a new temporary needs to be created - and then   
the second operator+ should call the rvalue+lvalue overload - and that   
one can just call operator += on the rvalue and return it.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2017-08-14 18:29:37 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-322270336  

On 14/08/2017 13:29, Lakshay Garg wrote:  
>  
> *@lakshayg* commented on this pull request.  
>  
> ------------------------------------------------------------------------  
>  
> In include/boost/math/tools/polynomial.hpp   
> <https://github.com/boostorg/math/pull/79#discussion_r132938545>:  
>  
> >  }  
>     
>   template <class T>  
> -inline polynomial<T> operator * (const polynomial<T>& a, const polynomial<T>& b)  
> +inline polynomial<T> operator * (polynomial<T> a, const polynomial<T>& b)  
>  
> I could not understand what you said about |operator *=|, could you   
> please explain?  
>  
  
I'm suggesting we reimplement like this:  
  
inline polynomial<T> operator * (const polynomial<T>& a, const polynomial<T>& b)  
  
{  
   polynomial<T> t;  // no need to copy anything, cheap to construct   
zero order polynomial allocates nothing.  
   t.multiply(a, b);  
   return t;  
}  
  
Likewise operator *= changes to:  
  
polynomial<T>& operator*=(const polynomial& a)  
{  
   multiply(*this, a);  
   return *this;  
}  
  
And the new "multiply" member function is virtually the same as the   
current operator*=, but takes 2 arguments, rather than replying on *this   
for one of the input vectors.  
  
Currently we have:  
  
template <class T>  
inline polynomial<T> operator * (const polynomial<T>& a, const   
polynomial<T>& b)  
{  
    polynomial<T> result(a);  // copies a, allocates memory.  
    result *= b;   // reallocates more memory.  
    return result;  
}  
  
Which does 2 allocations, where as I'm suggesting a little re-factoring   
would only do 1.  
  
Best, John.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 13

> Username: lakshayg  
> Created_at: 2017-08-14 20:20:55 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-322298397  

Should we remove the `explicit` keyword from [the constructor][1]  
  
```  
    template <class U>  
    explicit polynomial(const U& point)  
    {  
        if (point != U(0))  
           m_data.push_back(point);  
    }   
```  
it should provide us with opportunities to simplify the code by removing some overloads like [this][2].  
  
[1]: https://github.com/boostorg/math/pull/79/files/f0e7199034c0a208f3ef2ac20d4704dac201db9e#diff-46985bfe357deae42e1778baec521dfaR287  
[2]: https://github.com/boostorg/math/pull/79/files/f0e7199034c0a208f3ef2ac20d4704dac201db9e#diff-46985bfe357deae42e1778baec521dfaR626

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2017-08-15 11:29:17 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-322443310  

On 14/08/2017 21:20, Lakshay Garg wrote:  
>  
> Should we remove the |explicit| keyword from the constructor   
> <https://github.com/boostorg/math/pull/79/files/f0e7199034c0a208f3ef2ac20d4704dac201db9e#diff-46985bfe357deae42e1778baec521dfaR287>  
>  
> |template <class U> explicit polynomial(const U& point) { if (point !=   
> U(0)) m_data.push_back(point); } |  
>  
> it should provide us with opportunities to simplify the code by   
> removing some overloads like this   
> <https://github.com/boostorg/math/pull/79/files/f0e7199034c0a208f3ef2ac20d4704dac201db9e#diff-46985bfe357deae42e1778baec521dfaR626>.  
>  
  
No: the explicit keyword is there to express semantics, we should only   
remove it if we *really* want to change the semantics, not to make   
coding easier.  
  
Plus, if we rely on implicit conversion to polynomial<T> for the scalar   
operators these then become less efficient: it's far more efficient to   
multiply or add a scalar than to construct a polynomial temporary   
(memory allocation) and then go through an unnecessary loop in the   
operator implementation.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Review 15 [Commented]

> Username: jzmaddock  
> Created_at: 2017-08-15 11:29:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/79#pullrequestreview-56317712  

📁 include/boost/math/tools/polynomial.hpp

```diff
 361 | 
 362 |    // operators:
 363 |+    polynomial& operator =(polynomial p)
```

> Username: jzmaddock  
> Created_at: 2017-08-15 11:29:53 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r133168568  

Doesn't this create an unnecessary extra copy in C++03 land?

> Username: lakshayg  
> Created_at: 2017-08-15 11:41:25 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r133170203  

We have two cases:  
```cpp  
polynomial<double> p{1, 2, 3, 4};  
  
// case 1:  
polynomial<double> q;  
q = p; // this is handled optimally in both c++03 and c++11 since q has no memory allocated to it  
  
// case 2:  
polynomial<double> r{5, 6, 7, 8, 9, 1, 2, 3, 4};  
r = p; // this is bad. We could have prevented a copy by overwriting r.m_data  
```  
  
Will fix it shortly. I'm afraid the code wil turn into a mess of `#if`s and `#ifndef`s. Can you suggest a way we can prevent this from happening?


---

## Review 16 [Commented]

> Username: jzmaddock  
> Created_at: 2017-08-15 11:30:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/79#pullrequestreview-56317866  

📁 include/boost/math/tools/polynomial.hpp

```diff
 641 | template <class T, class U>
 585 |- inline polynomial<T> operator + (const polynomial<T>& a, const U& b)
 642 |+ inline polynomial<T> operator + (polynomial<T> a, const U& b)
```

> Username: jzmaddock  
> Created_at: 2017-08-15 11:30:51 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r133168725  

As with the assignment operator, doesn't this create an extra full copy in C++03?

> Username: lakshayg  
> Created_at: 2017-08-15 11:46:42 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r133171075  

From what I understood, here U is some type other than polynomial (am I right?). In that case, don't we need to allocate memory for addition anyway?

> Username: lakshayg  
> Created_at: 2017-08-16 11:37:12 UTC  
> Updated_at: 2017-08-17 03:41:58 UTC  
> Url: https://github.com/boostorg/math/pull/79#discussion_r133424807  

@jzmaddock I am a little confused by your comment. Here is what I think:  
  
1. This function takes a polynomial and scalar, adds them and returns the result as a new polynomial  
2. Suppose that we keep the signature I suggested:  
  * If the input is a lvalue, a copy is made due to passing by value, scalar is added (does not allocate any memory) and we return the polynomial i.e. 1 memory allocation  
  * If the input is a rvalue, the move constructor is invoked when passing and no copy is made. In the function body, we add using += and return the polynomial. This involves no copying.  
3. I don't think we can do any better in terms of memory allocation by adding separate overloads for lvalue and rvalue.  
  
Please let me know your views on this.


---

## Comment 17

> Username: jzmaddock  
> Created_at: 2017-08-15 12:02:54 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-322448722  

On 15/08/2017 12:46, Lakshay Garg wrote:  
>  
> *@lakshayg* commented on this pull request.  
>  
> ------------------------------------------------------------------------  
>  
> In include/boost/math/tools/polynomial.hpp   
> <https://github.com/boostorg/math/pull/79#discussion_r133171075>:  
>  
> >     return result;  
>   }  
>     
>   template <class T, class U>  
> -inline polynomial<T> operator + (const polynomial<T>& a, const U& b)  
> +inline polynomial<T> operator + (polynomial<T> a, const U& b)  
>  
> From what I understood, here U is some type other than polynomial (am   
> I right?). In that case, don't we need to allocate memory for addition   
> anyway?  
>  
  
Nope, U is a scalar, so we just add b to the zeroth element in a.   
Likewise for multiplication we just multiply each element by the scalar.  
  
There is a subsequent normalization step which may in some (not common)   
cases result in a change in size of the polynomial, but in most cases no   
memory allocation takes place.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2017-08-15 12:04:29 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-322448986  

> Will fix it shortly. I'm afraid the code wil turn into a mess of   
> |#if|s./|#ifndef|s. Can you suggest a way we can prevent this from   
> happening?  
>  
  
Well you're not going to like it, but separate overloads for  
  
operator=(const polynomial&)  
operator=(polynomial&&)  
  
would do the trick?  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 19

> Username: jzmaddock  
> Created_at: 2017-08-16 12:48:59 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-322759949  

On 16/08/2017 12:37, Lakshay Garg wrote:  
>  
> *@lakshayg* commented on this pull request.  
>  
> ------------------------------------------------------------------------  
>  
> In include/boost/math/tools/polynomial.hpp   
> <https://github.com/boostorg/math/pull/79#discussion_r133424807>:  
>  
> >     return result;  
>   }  
>     
>   template <class T, class U>  
> -inline polynomial<T> operator + (const polynomial<T>& a, const U& b)  
> +inline polynomial<T> operator + (polynomial<T> a, const U& b)  
>  
> @jzmaddock <https://github.com/jzmaddock> I am a little confused by   
> your comment. Here is what I think:  
>  
>  1. This function takes a polynomial and scalar, adds them and returns  
>     the result as a new polynomial  
>  2. Suppose that we keep the signature I suggested:  
>  
>   * If the input is a lvalue, a copy is made due to passing by value,  
>     scalar is added (does not allocate any memory) and we return the  
>     polynomial i.e. 1 memory allocation  
>   * If the input is a rvalue, the move constructor is invoked when  
>     passing and no copy is made. In the function body, we add using +=  
>     and return the polynomial. This involves no copying.  
>  
>  3. I don't think we can do any better in terms of memory allocation  
>     by adding separate overloads for lvalue and rvalue.  
>  
  
Well I think this serves me right for trying to review code of an evening!  
  
You're quite right of course, apologies for the noise!  John.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 20

> Username: lakshayg  
> Created_at: 2017-08-17 10:23:24 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-323030096  

@jzmaddock all tests have passed. Do you see anything else that needs to be done?

---

## Comment 21

> Username: jzmaddock  
> Created_at: 2017-08-17 17:37:04 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-323142656  

On 17/08/2017 11:23, Lakshay Garg wrote:  
>  
> @jzmaddock <https://github.com/jzmaddock> all tests have passed. Do   
> you see anything else that needs to be done?  
>  
  
I don't think so, but let me look at this properly over the weekend: as   
you may have noticed, this class was originally an internal detail "just   
good enough to do what needed to be done", and it sort of leaked out due   
to public demand.  I'd like to find some time to sit down and read   
through the whole file properly and see what else if anything needs to   
be done, I should probably add to the tests as well.  
  
Many thanks again for taking this on!  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 22

> Username: pabristow  
> Created_at: 2017-08-18 09:44:07 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-323309735  

I’ve viewed this interchange tangentially.  
  
Would it be useful to briefly summarise the conclusions for a ‘implementation’ section in the documentation?  
  
It seems complicated enough to me that other may find it helpful in future (and in implementing other functions too?).  
  
Paul  
  
From: Lakshay Garg [mailto:notifications@github.com]  
Sent: 17 August 2017 11:23  
To: boostorg/math  
Cc: Subscribed  
Subject: Re: [boostorg/math] enable move semantics for polynomial (#79)  
  
  
@jzmaddock<https://github.com/jzmaddock> all tests have passed. Do you see anything else that needs to be done?  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/math/pull/79#issuecomment-323030096>, or mute the thread<https://github.com/notifications/unsubscribe-auth/ABBuAaTjthFAOmmJQdsASqSXpHH_Wpgyks5sZBSdgaJpZM4O1-oU>.

---

## Comment 23

> Username: lakshayg  
> Created_at: 2017-08-18 13:29:08 UTC  
> Updated_at: 2017-08-18 13:30:57 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-323353671  

> Would it be useful to briefly summarise the conclusions for a ‘implementation’ section in the documentation?  
> It seems complicated enough to me that other may find it helpful in future (and in implementing other functions too?).  
  
Hey Paul. Many thanks for the suggestion. But, I'm not sure if I understand it completely.  
​  
​Are you suggesting that we should have a section called "implementation" in the documentation summarizing the implementation of this class? The suggestion sounds good but I cannot think of anyone who would benefit from such a documentation.  
  
As far as a user of this library is concerned, there is nothing that the user needs to know about this change. They can continue to use the library in the same manner as they do now. If they use C++11 and above, they should see improvement in the performance of some algorithms due to fewer memory allocations.  
  
Talking about a library dev, move semantics are fairly common technique used in modern c++ code so it should be fairly easy for him/her to grasp them and start coding. Having a section for explaining the implementation will more-or-less be  like having a tutorial on move semantics.  
  
Please let me know if you feel differently and would like to have an implementation section.  
  
On a different note, I will see if there are any places which can be updated in the documentation and update them over this weekend.  
  
Lakshay

---

## Comment 24

> Username: pabristow  
> Created_at: 2017-08-18 15:58:18 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-323392629  

I’ve only note that you and John seemed to have quite a long discussion about it ;-)  
  
I didn’t follow in detail but what may be obvious to you may not be to future maintainers.  
Perhaps a reference would suffice?  
  
(I’ve even found that implementation notes have helped me years later when I have even forgotten that I wrote it!)  
  
Paul  
  
  
  
  
From: Lakshay Garg [mailto:notifications@github.com]  
Sent: 18 August 2017 14:29  
To: boostorg/math  
Cc: Paul A. Bristow; Comment  
Subject: Re: [boostorg/math] enable move semantics for polynomial (#79)  
  
Would it be useful to briefly summarise the conclusions for a ‘implementation’ section in the documentation?  
It seems complicated enough to me that other may find it helpful in future (and in implementing other functions too?).  
  
​  
Hey Paul. Many thanks for the suggestion. I'm not sure if I understand it completely.  
​  
  
​  
​Are you suggesting that we should have a section called "implementation" in the documentation summarizing the implementation of this class? The suggestion sounds good but I cannot think of anyone who would benefit from such a documentation.  
  
As far as a user of this library is considered, there is nothing that the user needs to know about this change. They can continue to use the library in the same manner and if they use C++11 and above, they should see improvement in the performance of some algorithms due to fewer memory allocations.  
  
Talking about a library dev, move semantics are fairly common technique used in modern c++ code so it should be fairly easy for him/her to grasp them and start coding. Having a section for explaining the implementation will more-or-less be like having a tutorial on move semantics.  
  
Please let me know if you feel differently and would like to have an implementation section.  
  
On a different note, I will see if there are any places which can be updated in the documentation and update them over this weekend.  
  
Lakshay  
  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/math/pull/79#issuecomment-323353671>, or mute the thread<https://github.com/notifications/unsubscribe-auth/ABBuAVil5D0X14J_MAz82RAlCGmyBY33ks5sZZGlgaJpZM4O1-oU>.

---

## Comment 25

> Username: jzmaddock  
> Created_at: 2017-08-20 18:09:10 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-323601557  

Interestingly when I benchmarked these changes, comparing new and old, it made very little difference with msvc-14.1.  My suspicion is that the compiler is so good at inlining the operators that the copies/moves just disappear anyway...  it'll help a lot in debug mode of course.

---

## Comment 26

> Username: lakshayg  
> Created_at: 2017-08-21 02:22:26 UTC  
> Url: https://github.com/boostorg/math/pull/79#issuecomment-323631656  

On 20 August 2017 at 23:39, jzmaddock <notifications@github.com<mailto:notifications@github.com>> wrote:  
  
Interestingly when I benchmarked these changes, comparing new and old, it made very little difference with msvc-14.1. My suspicion is that the compiler is so good at inlining the operators that the copies/moves just disappear anyway... it'll help a lot in debug mode of course.  
  
​I benchmarked these changes on GCC and it indeed showed a significant speedup even after using -O3​  
​.

---
