# #838 - Binding custom function fails to compile if function has more than three arguments [Open]

> Username: rgreenblatt  
> Created at: 2019-06-11 22:26:27 UTC  
> Updated at: 2019-06-14 13:43:40 UTC  
> Url: https://github.com/boostorg/compute/issues/838  

For instance adding the following to test/test_functional_bind.cpp causes a compiler error:  
```  
BOOST_AUTO_TEST_CASE(bind_more_than_four)  
{  
    int data[] = { 1, 2, 3, 4 };  
    compute::vector<int> vector(data, data + 4, queue);  
  
    BOOST_COMPUTE_FUNCTION(int, add_four_nums,   
                           (int first, int second, int third, int fourth),  
    {  
        return first + second + third + fourth;  
    });  
  
    compute::transform(  
        vector.begin(), vector.end(), vector.begin(),  
        compute::bind(add_four_nums, _1, 2, 3, 4),  
        queue  
    );  
}  
```  
The full error and build log can be found here: https://pastebin.com/MnHi2WZs. I am using ubuntu 18.04 with libboost1.65-dev and g++ 7.4. This reproduces for a variety of different argument types etc.  
  
As far as I know, the best work around is to adapt a struct which contains to arguments.

---

## Comment 1

> Username: rgreenblatt  
> Created at: 2019-06-13 19:20:09 UTC  
> Updated at: 2019-06-13 19:20:31 UTC  
> Url: https://github.com/boostorg/compute/issues/838#issuecomment-501843972  

This occurs because in [this section](https://github.com/boostorg/compute/blob/master/include/boost/compute/functional/bind.hpp#L250), the number of arguments only go up to three. I will make a pr to  address this.

---

## Comment 2

> Username: jszuppe  
> Created at: 2019-06-13 20:16:48 UTC  
> Url: https://github.com/boostorg/compute/issues/838#issuecomment-501862541  

Can you try enabling C++11 for Boost.Compute by defining `BOOST_COMPUTE_USE_CPP11`? This should enable the variadic template version from line 221. If you need C++03 version you can create PR to add versions with more arguments, or do in your code.

---

## Comment 3

> Username: rgreenblatt  
> Created at: 2019-06-14 13:43:40 UTC  
> Url: https://github.com/boostorg/compute/issues/838#issuecomment-502114458  

I was incorrect about the code causing this issue. The actual issue is in this section: https://github.com/boostorg/compute/blob/master/include/boost/compute/functional/bind.hpp#L145. The issue isn't resolved by enabling c++11 because there is no variadic template version of apply_invoked_bound_function. I don't know how to use variadic templates to generalize apply_invoked_bound_function, but I would be happy to make a pr to add additional versions of the function up to some larger number of arguments.
