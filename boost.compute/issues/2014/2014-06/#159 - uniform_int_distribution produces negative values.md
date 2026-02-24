# #159 - uniform_int_distribution produces negative values [Closed]

> Username: kylelutz  
> Created at: 2014-06-26 05:42:44 UTC  
> Updated at: 2014-09-28 23:08:06 UTC  
> Closed at: 2014-09-28 23:08:06 UTC  
> Url: https://github.com/boostorg/compute/issues/159  

The `uniform_int_distribution` class produces negative values even when the range is entirely positive.  
  
For example, this code:  
  
```  
// generate random numbers between 1 and 10  
compute::default_random_engine rng(queue);  
compute::uniform_int_distribution<int> d(1, 10);  
d.generate(input.begin(), input.end(), rng, queue);  
  
// print random numbers  
compute::copy(  
    input.begin(), input.end(), std::ostream_iterator<int>(std::cout, ", "), queue  
);  
```  
  
Produced the following values:  
  
```  
-3, 3, -1, 0, 5, -4, -6, 6, -7, 4, 6, 0, 1, 7, -6, -5,  
```  
  
@roshanr95 Can you look into this? Also it would probably be good to add this example as a test case and ensure that all the numbers generated are between 1 and 10.

---

## Comment 1

> Username: roshanrags  
> Created at: 2014-06-27 22:51:06 UTC  
> Updated at: 2014-06-27 22:51:27 UTC  
> Url: https://github.com/boostorg/compute/issues/159#issuecomment-47408382  

There's some range problem with casting the type of the generator to the resulting type. It works if you replace `int` with `uint`. I'll look into making it work in general...

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-06-28 02:15:54 UTC  
> Updated at: 2014-06-28 02:16:24 UTC  
> Url: https://github.com/boostorg/compute/issues/159#issuecomment-47415507  

Cool, thanks! This would be good to have fixed for the next release (`0.3`, hopefully in a week or two).

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-09-28 23:08:06 UTC  
> Url: https://github.com/boostorg/compute/issues/159#issuecomment-57103950  

Fixed in PR #265
