# #26 Added BOOST_TEST_ALL_EQ macro to compare container contents [Merged]

> Username: breese  
> Created at: 2017-02-11 13:59:05 UTC  
> Updated at: 2017-02-13 16:15:11 UTC  
> Merged at: 2017-02-13 16:15:10 UTC  
> Closed at: 2017-02-13 16:15:10 UTC  
> Url: https://github.com/boostorg/core/pull/26  



---

## Review 1 [Commented]

> Username: glenfe  
> Created_at: 2017-02-11 14:54:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/26#pullrequestreview-21387224  

I wonder if instead of the ostringstream, just having test_all_eq_impl take an additional parameter of a template argument: Output& os, and passing in BOOST_LIGHTWEIGHT_TEST_OSTREAM, would be better. That way you can just write to 'os' in each iteration and the output could still show all the equal elements until the loop stops.

---

## Comment 2

> Username: breese  
> Created_at: 2017-02-11 15:31:32 UTC  
> Url: https://github.com/boostorg/core/pull/26#issuecomment-279152559  

@glenfe Good idea. Patch updated.

---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2017-02-11 16:04:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/26#pullrequestreview-21388412  

📁 include/boost/core/lightweight_test.hpp

```diff
 211 |+             if (first_iteration)
 212 |+             {
 213 |+                 first_iteration = true;
```

> Username: pdimov  
> Created_at: 2017-02-11 16:04:13 UTC  
> Updated_at: 2017-02-13 14:51:33 UTC  
> Url: https://github.com/boostorg/core/pull/26#discussion_r100670197  

Should probably be `false`.


---

## Comment 4

> Username: pdimov  
> Created_at: 2017-02-11 16:17:24 UTC  
> Url: https://github.com/boostorg/core/pull/26#issuecomment-279156441  

When the sizes differ, it would be nice if we output them.  
  
The loop doesn't look correct to me. You should add a test for the fail case, something like  
  
```  
int main()  
{  
    vector<int> x, y;  
    x.push_back( 1 ); x.push_back( 2 ); x.push_back( 3 ); x.push_back( 4 );  
    y.push_back( 1 ); y.push_back( 3 ); y.push_back( 2 ); y.push_back( 4 );  
    BOOST_TEST_ALL_EQ( x.begin(), x.end(), y.begin(), y.end() );  
  
    boost::report_errors();  
  
    return boost::detail::test_errors() != 1; // more, when more cases are added  
}  
```

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-02-11 17:26:36 UTC  
> Url: https://github.com/boostorg/core/pull/26#issuecomment-279161462  

No, the fail test shouldn't be `run-fail`, it should be `run`.

---

## Comment 6

> Username: breese  
> Created_at: 2017-02-11 17:46:48 UTC  
> Url: https://github.com/boostorg/core/pull/26#issuecomment-279162931  

@pdimov I am not sure why it should be `run`.  
  
With `run-fail` I get a `failed-as-expected`, and with `run` I get a noisy `failed testing.capture-output`.

---

## Comment 7

> Username: pdimov  
> Created_at: 2017-02-11 18:04:24 UTC  
> Url: https://github.com/boostorg/core/pull/26#issuecomment-279164111  

Well I used `!=` for a reason. We want to test that the number of failures matches what we expect.

---

## Comment 8

> Username: pdimov  
> Created_at: 2017-02-11 18:05:58 UTC  
> Url: https://github.com/boostorg/core/pull/26#issuecomment-279164238  

`return errors != 5` is the same as `return errors == 5? 0: 1`, that is, when we get 5 errors - which we expect - the test passes, and when we don't get 5 errors, it fails.

---

## Comment 9

> Username: glenfe  
> Created_at: 2017-02-11 23:54:07 UTC  
> Url: https://github.com/boostorg/core/pull/26#issuecomment-279184927  

It should probably be just implemented in terms of Input iterators instead of Forward iterators. It makes it more useful (and losing the distance check at the start to fail faster is fine).  
  
You also want to output test_output_impl(*first_it) and test_output_impl(*second_it) like the other functions do.

---

## Comment 10

> Username: breese  
> Created_at: 2017-02-12 11:14:54 UTC  
> Url: https://github.com/boostorg/core/pull/26#issuecomment-279211414  

@pdimov What confuses me is that you suggest that my failing test cases should be handled differently than the other failing lightweight_test test cases. I do not mind, and I can easily make the changes, but I just want to make sure first.

---

## Comment 11

> Username: breese  
> Created_at: 2017-02-12 12:16:57 UTC  
> Url: https://github.com/boostorg/core/pull/26#issuecomment-279214590  

@glenfe The distance check was primarily a safe-guard for `std::mismatch`, but since that is no longer used, I have changed the forward iterators to input iterators.  
  
Differing sizes are now reported when we reach the end of one of the containers.

---

## Comment 12

> Username: pdimov  
> Created_at: 2017-02-12 13:18:13 UTC  
> Url: https://github.com/boostorg/core/pull/26#issuecomment-279218000  

The traditional way would require a file per test. I should've made the other fail tests use this technique too, but that didn't occur to me at the time.

---

## Comment 13

> Username: pdimov  
> Created_at: 2017-02-12 13:22:35 UTC  
> Url: https://github.com/boostorg/core/pull/26#issuecomment-279218227  

There should probably be a limit for the number of differences that are printed, for the case where the sizes are large. Print the first 8 for example and exit the loop?

---

## Comment 14

> Username: breese  
> Created_at: 2017-02-12 14:07:01 UTC  
> Url: https://github.com/boostorg/core/pull/26#issuecomment-279220837  

@pdimov I agree about the upper limit. Patch added.

---

## Comment 15

> Username: pdimov  
> Created_at: 2017-02-13 13:48:35 UTC  
> Url: https://github.com/boostorg/core/pull/26#issuecomment-279396822  

This looks good.  
  
In the documentation, "containers" probably should say "sequences" now, and since the test is nominally positive, it might be better to call it something like `lightweight_test_all_eq_test` instead, and I think we'll be done here. Thanks Bjørn.

---

## Comment 16

> Username: pdimov  
> Created_at: 2017-02-13 16:12:59 UTC  
> Url: https://github.com/boostorg/core/pull/26#issuecomment-279438543  

Perfect. Thanks again. Will merge shortly.

---
