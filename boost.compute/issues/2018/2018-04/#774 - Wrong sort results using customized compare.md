# #774 - Wrong sort results using customized compare [Closed]

> Username: antibes0415  
> Created at: 2018-04-16 08:55:24 UTC  
> Updated at: 2018-12-20 08:33:33 UTC  
> Closed at: 2018-12-20 08:33:26 UTC  
> Url: https://github.com/boostorg/compute/issues/774  

Hi. I want to sort 64-bit long variates according to their upper 32 bit value.  
Here is the code:  
  
`    
BOOST_COMPUTE_FUNCTION(bool, compareSampleKeys, (cl_ulong a, cl_ulong b), {  
    return (a >> 32) < (b >> 32);  
  });  
  // cl::Buffer *m_dev_sampleKeys;  
  boost::compute::buffer cbSampleKeys((*m_dev_sampleKeys)());  
  
  boost::compute::buffer_iterator<cl_ulong> iter =  
    boost::compute::make_buffer_iterator<cl_ulong>(cbSampleKeys, 0);  
  
  boost::compute::sort(  
    iter,  
    iter + m_nbSamples,  
    compareSampleKeys,  
    m_compute_queue  
  );  
`  
  
Don't know why lots values are modified after sorting. In my case, all numbers are unique and there turn out to be many repetitive numbers after sorting. I tried not using customized compare, and that won't modify the values.

---

## Comment 1

> Username: rosenrodt  
> Created at: 2018-12-18 10:11:21 UTC  
> Updated at: 2018-12-18 10:44:25 UTC  
> Url: https://github.com/boostorg/compute/issues/774#issuecomment-448167646  

I have the same issue with compute::sort() too. I tried a vector of custom struct consisting key & value pair. Unique values in, duplicate values out.

---

## Comment 2

> Username: rosenrodt  
> Created at: 2018-12-19 08:25:31 UTC  
> Url: https://github.com/boostorg/compute/issues/774#issuecomment-448509350  

@antibes0415 I have managed to hack together the fix. Runs fine on my GTX1060. It is located in rosenrodt/compute@528eb799f0564b8144b16f2eca5d50b1ade15c69. In the meantime, I am testing on machines available to me to see if all tests pass

---

## Comment 3

> Username: jszuppe  
> Created at: 2018-12-20 08:33:26 UTC  
> Url: https://github.com/boostorg/compute/issues/774#issuecomment-448915782  

Fixed in #803
