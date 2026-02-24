# #513 - gather algorithm with zip iterator [Closed]

> Username: jpola  
> Created at: 2015-09-16 22:18:51 UTC  
> Updated at: 2015-09-21 00:59:36 UTC  
> Closed at: 2015-09-21 00:59:29 UTC  
> Url: https://github.com/boostorg/compute/issues/513  

Hi   
please check following code, according to it gather does not support zip_iterator, it just doesn't change the output. What i want to do is to shuffle the (ci, cf) according to permutation cp and save result in (oi, of).   
  
``` c++  
BOOST_AUTO_TEST_CASE(gather_zip)  
{  
    float input_f [] = {5, 4, 3, 2, 1 };  
    int   input_i [] = {5, 4, 3, 2, 1 };  
  
    int permutation [] = {4, 3, 2, 1, 0};  
  
    compute::vector<int> ci(input_i, input_i + 5, queue);  
        compute::vector<float> cf(input_f, input_f + 5, queue);  
  
    compute::vector<int> cp(permutation, permutation + 5, queue);  
  
    compute::vector<int> oi(5, context);  
    compute::vector<float> of(5, context);  
  
    compute::iota(oi.begin(), oi.end(), -6, queue);  
    compute::iota(of.begin(), of.end(), -10.f, queue);  
  
    compute::gather(cp.begin(), cp.end(),   
        compute::make_zip_iterator(  
            boost::make_tuple(  
                ci.begin(),  
                cf.begin()  
            )  
        ),  
        compute::make_zip_iterator(  
            boost::make_tuple(  
                oi.begin(),  
                of.begin()  
            )  
        ),  
        queue);  
  
  
    std::vector<int> hoi(5);  
    compute::copy(oi.begin(), oi.end(), hoi.begin(), queue);  
  
    std::vector<float> hof(5);  
    compute::copy(of.begin(), of.end(), hof.begin(), queue);  
  
    for (int i = 0; i < 5; i++)  
    {  
        std::cout << hoi[i] << "\t" << hof[i] << std::endl;  
    }     
}  
```

---

## Comment 1

> Username: jszuppe  
> Created at: 2015-09-17 10:49:00 UTC  
> Url: https://github.com/boostorg/compute/issues/513#issuecomment-141041337  

Yes, you're right. Currently you have to run `boost::compute::gather` for each input vector separately.   
  
When `zip_iterator` is used lines  
  
``` cpp  
*this << "const uint i = get_global_id(0);\n"   
      << result[expr<uint_>("i")] << "=" << input[first[expr<uint_>("i")]] << ";\n";  
```  
  
in gather algorithm meta_kernel are translated into  
  
``` cpp  
(boost_tuple_int_float_t){ _buf0[i], _buf1[i]}   
    = (boost_tuple_int_float_t){ _buf2[_buf3[i]], _buf4[_buf3[i]]};  
```  
  
and that obviously does not work. I'm sure this is not the only algorithm with that issue.

---

## Comment 2

> Username: jpola  
> Created at: 2015-09-17 10:52:04 UTC  
> Url: https://github.com/boostorg/compute/issues/513#issuecomment-141041841  

Thank you, for your help.

---

## Comment 3

> Username: kylelutz  
> Created at: 2015-09-21 00:59:04 UTC  
> Updated at: 2015-09-21 00:59:27 UTC  
> Url: https://github.com/boostorg/compute/issues/513#issuecomment-141851780  

Yeah, unfortunately you can't assign values through a `zip_iterator`, it is more or less "read-only".
