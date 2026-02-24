# #50 - compute::transform over zip iterators [Closed]

> Username: ddemidov  
> Created at: 2014-01-09 08:08:16 UTC  
> Updated at: 2014-01-23 19:14:16 UTC  
> Closed at: 2014-01-23 19:06:51 UTC  
> Url: https://github.com/boostorg/compute/issues/50  

Is it possible to transform a zip iterator in a general way? As an example, rotate 2D points, where components are stored in individual vectors:  
  
```  
y0 = cos(a) * x0 - sin(a) * x1;  
y1 = sin(a) * x0 + cos(a) * x1;  
```  
  
I want to implement this along the lines of  
  
```  
bc::vector<double> x0(n), x1(n), y0(n), y1(n);  
  
bc::transform(  
  bc::make_zip_iterator( boost::make_tuple( x0.begin(), x1.begin() ) ),  
  bc::make_zip_iterator( boost::make_tuple( x0.end(), x1.end() ) ),  
  bc::make_zip_iterator( boost::make_tuple( y0.begin(), y1.begin() ) ),  
  ??? // what to write here?  
);  
```  
  
If the output is just a vector, one could use `bc::get<I>()` functionals, but what to use when output is a zip iterator?

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-01-09 15:56:18 UTC  
> Updated at: 2014-01-09 15:56:41 UTC  
> Url: https://github.com/boostorg/compute/issues/50#issuecomment-31945660  

It expects a function that takes a `boost::tuple<double, double>`.  
  
Check out the `price_cross` example: https://github.com/kylelutz/compute/blob/master/example/price_cross.cpp#L52  
  
You should also be able to use a lambda expression.  
  
Check out the `lambda_get_zip_iterator` test: https://github.com/kylelutz/compute/blob/master/test/test_lambda.cpp#L276

---

## Comment 2

> Username: ddemidov  
> Created at: 2014-01-09 16:00:27 UTC  
> Url: https://github.com/boostorg/compute/issues/50#issuecomment-31946171  

Is it also possible for the function to return a tuple?

---

## Comment 3

> Username: ddemidov  
> Created at: 2014-01-09 16:02:28 UTC  
> Url: https://github.com/boostorg/compute/issues/50#issuecomment-31946396  

And with lambda function, is it possible to combine several expressions? E.g. `( get<0>() = get<1>(), get<1> = get<0> )`?

---

## Comment 4

> Username: ddemidov  
> Created at: 2014-01-09 16:13:41 UTC  
> Url: https://github.com/boostorg/compute/issues/50#issuecomment-31947661  

Another problem with lambda expressions: it seems that `compute::get<I>()` does not provide overloaded arithmetic operators. So the following results in compile error:  
  
``` .cpp  
// extract second component from (input1, input2, input1)  
compute::transform(  
    compute::make_zip_iterator(  
        boost::make_tuple(input1.begin(), input2.begin(), input1.begin())  
    ),  
    compute::make_zip_iterator(  
        boost::make_tuple(input1.end(), input2.end(), input1.end())  
    ),  
    output.begin(),  
    1 * compute::get<1>(), // no match for ‘operator*’ (operand types are ‘int’ and ‘boost::compute::get<1ul>’)  
    queue  
);  
```

---

## Comment 5

> Username: kylelutz  
> Created at: 2014-01-09 16:25:44 UTC  
> Updated at: 2014-01-09 16:34:42 UTC  
> Url: https://github.com/boostorg/compute/issues/50#issuecomment-31949031  

Hmm, let me think about that. Theoretically that all should be possible. I'll have to look into what's currently implemented and what needs to be done.

---

## Comment 6

> Username: kylelutz  
> Created at: 2014-01-09 16:30:50 UTC  
> Updated at: 2014-01-09 16:34:09 UTC  
> Url: https://github.com/boostorg/compute/issues/50#issuecomment-31949630  

For your last example you should be using `compute::lambda::get` instead of just `compute::get`. That will allow you to use arithmetic operators.  
  
For example, the `lambda_get_zip_iterator` test could be modified to multiply the first value of a tuple by 2 then write it to the second value:  
  
```  
compute::for_each(  
    compute::make_zip_iterator(  
        boost::make_tuple(input.begin(), output.begin())  
    ),  
    compute::make_zip_iterator(  
        boost::make_tuple(input.end(), output.end())  
    ),  
    get<1>(_1) = 2.0 * get<0>(_1),  
    queue  
);  
```

---

## Comment 7

> Username: ddemidov  
> Created at: 2014-01-09 17:01:39 UTC  
> Updated at: 2014-01-09 17:06:34 UTC  
> Url: https://github.com/boostorg/compute/issues/50#issuecomment-31953284  

This part does work, except that `make_zip_iterator` does not accept tuples with more than three elements. The following test refuses to compile:  
  
``` .cpp  
BOOST_AUTO_TEST_CASE(lambda_get_zip_iterator)  
{  
    using boost::compute::_1;  
    using boost::compute::lambda::get;  
  
    float data[] = { 1.2f, 2.3f, 3.4f, 4.5f, 5.6f, 6.7f, 7.8f, 9.0f };  
    compute::vector<float> input(8, context);  
    compute::copy(data, data + 8, input.begin());  
  
    compute::vector<float> output(8, context);  
  
    compute::for_each(  
        compute::make_zip_iterator(  
            boost::make_tuple(input.begin(), output.begin(), input.begin(), input.begin()) // one element too many  
        ),  
        compute::make_zip_iterator(  
            boost::make_tuple(input.end(), output.end(), input.end(), input.end())  
        ),  
        get<1>(_1) = get<0>(_1)  
    );  
    CHECK_RANGE_EQUAL(float, 8, output,  
        (1.2f, 2.3f, 3.4f, 4.5f, 5.6f, 6.7f, 7.8f, 9.0f)  
    );  
}  
```  
  
What is left is how to form an expression that is able to fill a zipped sequence.

---

## Comment 8

> Username: kylelutz  
> Created at: 2014-01-09 17:08:45 UTC  
> Url: https://github.com/boostorg/compute/issues/50#issuecomment-31954018  

Yeah, the three element max needs to be fixed. I need to go add some BOOST_PP macros to generate the `tuple`/`zip_iterator` stuff instead of hard-coding it (laziness on my part).  
  
After that I think a `lambda::make_tuple` function which takes lambda expressions and returns a `boost::tuple` will solve the second issue.  
  
I'll try to find some time to work on this.

---

## Comment 9

> Username: ddemidov  
> Created at: 2014-01-09 17:11:49 UTC  
> Updated at: 2014-01-09 17:13:28 UTC  
> Url: https://github.com/boostorg/compute/issues/50#issuecomment-31954341  

I think I could replace [these](https://github.com/kylelutz/compute/blob/master/include/boost/compute/iterator/zip_iterator.hpp#L314-L369) and [these](https://github.com/kylelutz/compute/blob/master/include/boost/compute/iterator/zip_iterator.hpp#L95-L170) with a boost preprocessor macro.  If `lambda::make_tuple` is already available, that would scratch my itch. Or do you mean you need to implement that as well?

---

## Comment 10

> Username: kylelutz  
> Created at: 2014-01-09 17:24:35 UTC  
> Url: https://github.com/boostorg/compute/issues/50#issuecomment-31955689  

Yeah, if you could make those preprocessor generated that would be great! My general idea was to use boost preprocessor for C++98/03 and variadics for C++11. I already started a little bit (see `types/tuple.hpp` around the `BOOST_COMPUTE_DETAIL_NO_VARIADIC_TEMPLATES` code).  
  
`lambda::make_tuple` still needs to be implemented :-(.

---

## Comment 11

> Username: ddemidov  
> Created at: 2014-01-23 19:06:51 UTC  
> Updated at: 2014-01-23 19:14:16 UTC  
> Url: https://github.com/boostorg/compute/issues/50#issuecomment-33156953  

`compute::for_each` combined with `zip_iterator` and `lambda::make_tuple` solves the problem, thanks! I'd also like to keep a reference to the following thread of discussion: https://github.com/kylelutz/compute/commit/dc20f09d92acb5c103170a50c7289ac5a2160094#commitcomment-5072621.
