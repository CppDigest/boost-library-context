# #21 - lightweight_test and containers [Closed]

> Username: breese  
> Created at: 2017-01-28 13:07:10 UTC  
> Updated at: 2017-03-18 11:49:30 UTC  
> Closed at: 2017-03-18 11:49:30 UTC  
> Url: https://github.com/boostorg/core/issues/21  

This issue is to gauge the interest in adding a test macro to lightweight_test for comparing containers. I am using such a macro extensively in my own unit-tests.  
  
The test macro, called `BOOST_TEST_ALL_WITH`, takes the begin and end iterators of two containers plus a predicate. You can see the code [here](https://github.com/breese/core/commit/eb9b8ebf684abe38e723b39ac1736456a36c21f9).  
  
For instance two vectors of ints can be compared with  
```  
BOOST_TEST_ALL_WITH(input.begin(), input.end(),  
                    output.begin(), output.end(),  
                    std::equal_to<int>());  
```  
and two vectors of floats can be approximately compared with  
```  
const float tolerance = 1e-5;  
auto with_tolerance = [tolerance] (decltype(input)::value_type lhs, decltype(output)::value_type rhs)  
{  
    return (std::fabs(lhs - rhs) <= tolerance);  
};  
...  
BOOST_TEST_ALL_WITH(input.begin(), input.end(),  
                    output.begin(), output.end();  
                    with_tolerance);  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2017-01-29 13:54:22 UTC  
> Url: https://github.com/boostorg/core/issues/21#issuecomment-275915254  

Sounds interesting. Wouldn't it be useful to show the actual values though, like BOOST_TEST_EQ does?

---

## Comment 2

> Username: breese  
> Created at: 2017-01-29 15:45:34 UTC  
> Url: https://github.com/boostorg/core/issues/21#issuecomment-275922305  

That is a good idea.  
  
I initially wrote the macro without, because I have unit-tests where I compare structs that have an equality operator but no output operator. While these can be added non-intrusively, my structs are really templates (or nested inside templates) so I need to overload the output operator for each combination of template parameters. Not impossible, just inconvenient.  
  
It is for the same reason that I sometimes resort to `BOOST_TEST(x == y)` instead of `BOOST_TEST_EQ(x, y)`.  
  
So maybe we could have one with and one without value printing, or is that overkill?

---

## Comment 3

> Username: pdimov  
> Created at: 2017-01-30 20:03:56 UTC  
> Url: https://github.com/boostorg/core/issues/21#issuecomment-276174226  

We could have BOOST_TEST_ALL_EQ, which uses op== and outputs the mismatching values, and BOOST_TEST_ALL_WITH, which uses a predicate and doesn't.

---

## Comment 4

> Username: Beman  
> Created at: 2017-01-31 03:12:36 UTC  
> Url: https://github.com/boostorg/core/issues/21#issuecomment-276263101  

On Mon, Jan 30, 2017 at 3:03 PM, Peter Dimov <notifications@github.com>  
wrote:  
  
> We could have BOOST_TEST_ALL_EQ, which uses op== and outputs the  
> mismatching values, and BOOST_TEST_ALL_WITH, which uses a predicate and  
> doesn't.  
>  
+1  
  
--Beman

---

## Comment 5

> Username: glenfe  
> Created at: 2017-02-09 13:23:32 UTC  
> Url: https://github.com/boostorg/core/issues/21#issuecomment-278639066  

Bjorn, looks useful. One comment about the implementation: You label them as Input iterators (template parameter name), but are you assuming that they're Forward iterators?

---

## Comment 6

> Username: breese  
> Created at: 2017-02-11 11:57:42 UTC  
> Url: https://github.com/boostorg/core/issues/21#issuecomment-279139443  

@glenfe I used the `InputIterator` concept because that is what `std::mismatch` uses, but you are right that `ForwardIterator` is more correct because I "iterate" twice (first with `std::distance`).

---

## Comment 7

> Username: breese  
> Created at: 2017-02-11 14:04:12 UTC  
> Url: https://github.com/boostorg/core/issues/21#issuecomment-279145724  

I have submitted the `BOOST_TEST_ALL_EQ` macro (#26) as described above.  
  
The `BOOST_TEST_ALL_WITH` macro will follow in a later pull request.  
  
Should I add a `BOOST_TEST_WITH` macro for completeness that compares two values with a predicate?

---

## Comment 8

> Username: pdimov  
> Created at: 2017-02-17 23:43:34 UTC  
> Url: https://github.com/boostorg/core/issues/21#issuecomment-280797138  

I think that `BOOST_TEST_WITH(x,y,p)` doesn't give anything over `BOOST_TEST(p(x,y))`, so it doesn't look like we need it.

---

## Comment 9

> Username: breese  
> Created at: 2017-02-18 12:46:38 UTC  
> Url: https://github.com/boostorg/core/issues/21#issuecomment-280843451  

Good observation about `BOOST_TEST_WITH`. I agree that it is unnecessary.  
  
I have submitted the `BOOST_TEST_ALL_WITH` macro ( #27). It works like `BOOST_TEST_ALL_EQ` with two exceptions: (i) it takes a binary predicate instead of using `operator==`, and (ii) it only outputs mismatching indices, not their values.  
  
The test shows how to use either the binary predicates from `<functional>` or a custom binary predicate.

---

## Comment 10

> Username: glenfe  
> Created at: 2017-03-17 11:51:10 UTC  
> Url: https://github.com/boostorg/core/issues/21#issuecomment-287335019  

Since I made a claim on the mailing list about being more diligent about closing GitHub issues that are resolved... :-) Bjørn, any other functionality you want to include as part of this issue?

---

## Comment 11

> Username: breese  
> Created at: 2017-03-18 11:49:30 UTC  
> Url: https://github.com/boostorg/core/issues/21#issuecomment-287539459  

I am all set. Thanks for all the review comments.
