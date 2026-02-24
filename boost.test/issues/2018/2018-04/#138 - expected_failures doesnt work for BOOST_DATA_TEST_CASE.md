# #138 - expected_failures doesnt work for BOOST_DATA_TEST_CASE [Closed]

> Username: k15tfu  
> Created at: 2018-04-04 11:20:47 UTC  
> Updated at: 2019-03-15 07:30:06 UTC  
> Closed at: 2019-02-18 21:20:51 UTC  
> Url: https://github.com/boostorg/test/issues/138  

```  
BOOST_TEST_DECORATOR(*boost::unit_test::decorator::expected_failures(5))  
BOOST_DATA_TEST_CASE(test1, boost::unit_test::data::make({1}), digit) {  
    BOOST_CHECK(0);  
}  
```  
  
  
Output:  
```  
Running 1 test case...  
  
0%   10   20   30   40   50   60   70   80   90   100%  
|----|----|----|----|----|----|----|----|----|----|  
path/to/file.cpp(206): error: in "test1/_0": check 0 has failed  
Failure occurred in a following context:  
    digit = 1;  
***************************************************  
  
*** 1 failure is detected (11 failures are expected) in the test module "Common"  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-04-05 05:13:28 UTC  
> Url: https://github.com/boostorg/test/issues/138#issuecomment-378822325  

What happens if you have `*boost::unit_test::decorator::expected_failures(1)` instead?

---

## Comment 2

> Username: k15tfu  
> Created at: 2018-10-02 18:01:02 UTC  
> Url: https://github.com/boostorg/test/issues/138#issuecomment-426372313  

Well..  with `expected_failures(1)` it prints the same thing:  
  
```  
Running 1 test case...  
/path/yo/file(26): error: in "test1/_0": check 0 has failed  
Failure occurred in a following context:  
    digit = 1;  
  
*** 1 failure is detected (5 failures are expected) in the test module "Common"  
```  
  
P.S. 5 failures are expected -- is inherited from other test cases of the project.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-02-12 00:28:14 UTC  
> Url: https://github.com/boostorg/test/issues/138#issuecomment-462554115  

Working on a fix, sorry for the delay

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2019-02-13 16:35:30 UTC  
> Url: https://github.com/boostorg/test/issues/138#issuecomment-463269834  

I believe this is a duplicate of #133 and #204 as the decorators are not propagated properly from the parent test-suite. You can try the branch `topic/GH-138-decorator-dataset-testcase` and let me know if that works for you.

---

## Comment 5

> Username: k15tfu  
> Created at: 2019-03-14 12:31:01 UTC  
> Url: https://github.com/boostorg/test/issues/138#issuecomment-472832753  

@raffienficiaud Yes, it works! Thank you again. Will it be released in boost-1.70.0?

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2019-03-15 07:30:06 UTC  
> Url: https://github.com/boostorg/test/issues/138#issuecomment-473185911  

Very good! yes it is part of 1.70. The beta is available from the website, I am not planning to make any changes apart for the documentation for the final release.
