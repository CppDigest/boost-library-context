# #117 - Incorrect information in the tutorial/example [Closed]

> Username: hashangayasri  
> Created at: 2019-02-14 05:45:17 UTC  
> Updated at: 2019-03-04 18:42:35 UTC  
> Closed at: 2019-03-04 18:42:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/117  

The example in   
  
> multiprecision/doc/html/boost_multiprecision/tut/ints/cpp_int.html  
> <https://www.boost.org/doc/libs/1_69_0/libs/multiprecision/doc/html/boost_multiprecision/tut/ints/cpp_int.html>   
  
has misleading/incorrect comments. The actual outputs are:  
`2432902008176640000`  
and  
`93326215443944152681699238856266700490715968264381621468592963895217599993229915608941463976156518286253697920827223758251185210916864000000000000000000000000`

---

## Comment 1

> Username: pabristow  
> Created at: 2019-02-14 10:19:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/117#issuecomment-463573022  

Confirmed.  I think we could make this a bit clearer (especially by using the actual output as a snippet). On my TODO list.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-02-14 11:59:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/117#issuecomment-463601670  

The comments are correct though, what it says is:  
  
```  
// prints 20!  
```  
  
And 20! = 2432902008176640000 etc.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-03-04 18:42:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/117#issuecomment-469370090  

Will be corrected for the next release.
