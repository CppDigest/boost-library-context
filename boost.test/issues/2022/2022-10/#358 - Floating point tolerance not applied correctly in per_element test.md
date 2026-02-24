# #358 - Floating point tolerance not applied correctly in per_element test [Closed]

> Username: kschlegel  
> Created at: 2022-10-05 10:18:07 UTC  
> Updated at: 2023-02-22 21:12:45 UTC  
> Closed at: 2023-02-22 21:12:45 UTC  
> Url: https://github.com/boostorg/test/issues/358  

I am working on a numerical optimization project, using MKL. When testing my computed Jacobians I found that element-wise comparison of vectors/matrices with a given tolerance isn't working when elements to be tested are small. Here is what I do:  
  
```C++  
vnl_matrix< double > Jacobian(rows, cols);  
obj_fn->gradf(Jacobian);   // Jacobian now hold the Jacobian from my to be tested code  
  
vnl_matrix< double > ExpectedJacobian(rows, cols);  
ComputeFiniteDifferences(obj_fn, ExpectedJacobian);   // ExpectedJacobian now holds a numeric approximation of the Jacobian,   
                                                      // computed by using difference quotients  
// The following test occasionally fails even at much larger tolerance than I would like to test at. When it fails the elements of the  
// Jacobian where it fails are always very small (on the order of 10^(-5) or below)  
BOOST_TEST( Jacobian == ExpectedJacobian, boost::test_tools::tolerance( 1e-3 ) << boost::test_tools::per_element() );  
// This test in turn always passes at much smaller tolerance, confirming that the Jacobian and ExpectedJacobian are indeed within  
// tolerance of each other (This is also further confirmed when looking in detail at the output of the failed test, see below)  
BOOST_TEST( (Jacobian - ExpectedJacobian).array_inf_norm(), boost::test_tools::tolerance( 1e-6 ) );  
```  
  
Here is an example output of when the test failed:  
```  
check Jacobian == ExpectedJacobian has failed  
  - mismatch at position 4210: [2.9551696555663032e-06 == 2.9700686354772188e-06] is false: relative difference exceeds tolerance [0.00504167 > 0.001]  
```  
Note that  
 - The absolute values where it fails are both very small and clearly within tolerance of each other  
 - The reported difference is completely wrong, not only is it seemingly missing the magnitude of the values themselves, manually computing the difference I get ~0.014899e-06  
 - It clearly should be passing the test

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2023-02-22 21:12:45 UTC  
> Url: https://github.com/boostorg/test/issues/358#issuecomment-1440803272  

The documentation is [here](https://www.boost.org/doc/libs/1_81_0/libs/test/doc/html/boost_test/testing_tools/extended_comparison/floating_point.html) but more importantly, the way the values are compared is [here](https://www.boost.org/doc/libs/1_81_0/libs/test/doc/html/boost_test/testing_tools/extended_comparison/floating_point/floating_points_comparison_theory.html)  
  
Coming back to the numbers:  
  
```  
In [3]: u = 2.9551696555663032e-06  
In [4]: v = 2.9700686354772188e-06  
In [5]: abs(u - v) / v  
Out[5]: 0.005016375626121412  
  
In [6]: abs(u - v) / u  
Out[6]: 0.005041666519163174  
```  
  
The value that is printed is the max between this two **relative** tolerances, because for the test to pass, that max should be within the provided tolerance range.  
If you need to compare the absolute tolerance, you should do that wrt. 0, hence taking the diff of the two matrices and perform a comparison `== 0` within the `BOOST_TEST` macro.  
  
Feel free to reopen the ticket if there is something to clarify further!
