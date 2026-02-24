# #3 Correct update of .all-tests, thereby fixing --dump-tests [Merged]

> Username: pdimov  
> Created at: 2017-12-17 15:00:02 UTC  
> Updated at: 2017-12-17 21:04:20 UTC  
> Merged at: 2017-12-17 15:25:38 UTC  
> Closed at: 2017-12-17 15:25:38 UTC  
> Url: https://github.com/boostorg/quickbook/pull/3  

The current code overwrites `.all-tests`, so the `--dump-tests` option doesn't work (it only shows the last test).

---

## Comment 1

> Username: danieljames  
> Created_at: 2017-12-17 15:03:25 UTC  
> Url: https://github.com/boostorg/quickbook/pull/3#issuecomment-352261869  

What's the point of a pull request if you merge it immediately?

---

## Comment 2

> Username: danieljames  
> Created_at: 2017-12-17 15:05:35 UTC  
> Url: https://github.com/boostorg/quickbook/pull/3#issuecomment-352262019  

Oh sorry, the 'merged' is for the super project.

---

## Comment 3

> Username: swatanabe  
> Created_at: 2017-12-17 21:04:20 UTC  
> Url: https://github.com/boostorg/quickbook/pull/3#issuecomment-352285400  

It would be better to use testing.make-test, instead of directly modifying another module's variables.

---
