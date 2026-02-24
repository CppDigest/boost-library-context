# #54 - Boost.Operators should embrace constexpr [Closed]

> Username: tonyelewis  
> Created at: 2018-11-14 15:49:09 UTC  
> Updated at: 2020-04-12 13:28:27 UTC  
> Closed at: 2020-04-12 13:18:17 UTC  
> Url: https://github.com/boostorg/utility/issues/54  

Thanks to the maintainers of Boost.Utility.  
  
IMHO, the biggest limitation of Boost.Operators is that the operators it provides aren't `constexpr`. My initial experiments seem to suggest that adding `BOOST_CONSTEXPR` / `BOOST_CXX14_CONSTEXPR` to `operators.hpp` does the trick.  
  
 Would a PR along these lines be welcome? Any non-obvious issues of which I should be aware?

---

## Comment 1

> Username: tonyelewis  
> Created at: 2018-11-14 15:50:06 UTC  
> Url: https://github.com/boostorg/utility/issues/54#issuecomment-438710814  

(this follows on from [Trac#9044](https://svn.boost.org/trac10/ticket/9044))

---

## Comment 2

> Username: d-frey  
> Created at: 2018-11-22 07:14:17 UTC  
> Url: https://github.com/boostorg/utility/issues/54#issuecomment-440934439  

A PR would be welcome for the comparison operators.

---

## Comment 3

> Username: tonyelewis  
> Created at: 2020-04-12 11:58:56 UTC  
> Url: https://github.com/boostorg/utility/issues/54#issuecomment-612603543  

Thanks very much. I'm happy for this issue to be closed.

---

## Comment 4

> Username: d-frey  
> Created at: 2020-04-12 13:18:17 UTC  
> Url: https://github.com/boostorg/utility/issues/54#issuecomment-612613037  

Thank you for contributing and sorry that it took me so long. Stay safe and happy hacking! 🍀

---

## Comment 5

> Username: tonyelewis  
> Created at: 2020-04-12 13:28:27 UTC  
> Url: https://github.com/boostorg/utility/issues/54#issuecomment-612614483  

:slightly_smiling_face: You too.
