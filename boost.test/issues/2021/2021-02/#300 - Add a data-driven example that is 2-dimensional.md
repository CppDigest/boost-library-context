# #300 - Add a data-driven example that is 2-dimensional [Open]

> Username: eyal0  
> Created at: 2021-02-10 19:26:52 UTC  
> Updated at: 2022-02-27 19:46:18 UTC  
> Url: https://github.com/boostorg/test/issues/300  

https://github.com/boostorg/test/blob/9d863d07e864ef663e3e8573b55905099b938d3e/doc/examples/dataset_example67.run.cpp#L41  
  
I got confused when I read the docs about data-driven testing because I made a `vector` of `pair` and each element was treated as a single parameter to the macro.  But when I needed to add another parameter, I switched to tuple and got a long compiler error that didn't explain my problem well.  
  
For example, this will work:  
  
```c++  
BOOST_DATA_TEST_CASE(fib_test, bdata::make(std::vector<std::pair<int,int>>{}), sample) {  
...  
}  
```  
  
and each `sample` will be a `pair`.  But if you now want to add a third argument, so you switch `pair` to `tuple`.  It will now fail because you now need **three** parameters.  The `sample` **won't** be a `tuple`.  (I suspect that boost is using the `std::tuple` internally when generating test cases.)  
  
Please add an example how tuple can be used to make a static table.  For example:  
  
```c++  
BOOST_DATA_TEST_CASE(fib_test,  
                     bdata::make(  
                         std::vector<std::tuple<int,string,string>>{  
                           {0, "zero", "cero"},  
                           {1, "one", "uno"},  
                           {6, "six", "seis"}}),  
                     number, english, spanish) {  
  BOOST_CHECK_EQUAL(to_english(number), english);  
  BOOST_CHECK_EQUAL(to_spanish(number), spanish);  
}  
```  
  
Something like that to show off how to make a table.
