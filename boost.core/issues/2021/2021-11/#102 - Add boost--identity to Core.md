# #102 - Add boost::identity to Core? [Closed]

> Username: denzor200  
> Created at: 2021-11-05 17:14:54 UTC  
> Updated at: 2021-12-13 18:49:49 UTC  
> Closed at: 2021-12-13 18:49:36 UTC  
> Url: https://github.com/boostorg/core/issues/102  

https://en.cppreference.com/w/cpp/utility/functional/identity  
  
Just like `std::identity`, but available in earlier standards.  
Why not?

---

## Comment 1

> Username: pdimov  
> Created at: 2021-11-05 17:20:31 UTC  
> Url: https://github.com/boostorg/core/issues/102#issuecomment-962075977  

The proper place, in theory, for this would be http://boost.org/libs/functional, although that's a bit outdated now. :-)

---

## Comment 2

> Username: denzor200  
> Created at: 2021-11-14 00:38:28 UTC  
> Url: https://github.com/boostorg/core/issues/102#issuecomment-968181735  

> The proper place, in theory, for this would be http://boost.org/libs/functional, although that's a bit outdated now. :-)  
  
Can I create a corresponding pull request to _boost functional_ repository, or should I not?

---

## Comment 3

> Username: mclow  
> Created at: 2021-11-21 17:04:55 UTC  
> Url: https://github.com/boostorg/core/issues/102#issuecomment-974855722  

Just saw this; and I think a better place for this is in Boost.Algorithm.

---

## Comment 4

> Username: glenfe  
> Created at: 2021-12-13 18:49:49 UTC  
> Url: https://github.com/boostorg/core/issues/102#issuecomment-992768131  

https://github.com/boostorg/functional/commit/0be868143d53bd582a6ade485b58ed6bb6f38518
