# #14 - Why custom error object can not be handled when leaf::result is lay in the std::vector [Closed]

> Username: pwrliang  
> Created at: 2020-11-09 03:15:38 UTC  
> Updated at: 2020-11-09 07:54:23 UTC  
> Closed at: 2020-11-09 07:54:23 UTC  
> Url: https://github.com/boostorg/leaf/issues/14  

Hi there,  
  
  I'd like to use std::vector to store a series of leaf::result, because both of a valid value or an error can be represent by leaf::result. Like, `std::vector<boost::leaf::result<int>> result_vector`.  
  
Assuming I have an error object `MyError`:  
```cpp  
struct MyError {  
  MyError(int ecode, std::string emsg)  
      : error_code(ecode), error_msg(std::move(emsg)) {}  
  int error_code;  
  std::string error_msg;  
};  
```  
  
I'm trying to push_back a normal value and an error object into the vector, then iterate the vector. I expect MyError object can be handled, but it hits the unmatched branch.  
  
```cpp  
  std::vector<boost::leaf::result<int>> result;  
  result.emplace_back(123);  
  result.emplace_back(boost::leaf::new_error(MyError(-1, "Invalid number")));  
  
  boost::leaf::try_handle_all(  
      [&result]() -> boost::leaf::result<int> {  
        for (auto& e : result) {  
          BOOST_LEAF_AUTO(val, e);  
          std::cout << "Val: " << val << std::endl;  
        }  
        return 0;  
      },  
      [](const MyError& e) {  
        std::cerr << "Found error: " << e.error_code << ", " << e.error_msg  
                  << std::endl;  
        return 1;  
      },  
      [](const boost::leaf::error_info& unmatched) {  
        std::cerr << "Unmatched error: " << unmatched << std::endl;  
        return 1;  
      });  
```  
  
The output:  
```  
Val: 123  
Unmatched error: leaf::error_info: Error ID = 1  
```  
  
Thanks,  
Liang

---

## Comment 1

> Username: zajo  
> Created at: 2020-11-09 07:54:23 UTC  
> Url: https://github.com/boostorg/leaf/issues/14#issuecomment-723834839  

Yes, this is by design. In case of success, a `leaf::result<T>` contains a `T` object, but in case of an error, by default, it does not contain error objects -- they get transported directly to the error handling scope.  
  
If you need to collect results and then handle errors later, you need to make a `leaf::polymorphic_context`, which can then be stored in a `leaf::result`. This example is similar to your use case, except that the `leaf::result` objects are computed asynchronously by worker threads: https://github.com/boostorg/leaf/blob/master/examples/capture_in_result.cpp.  
  
Tutorial: https://boostorg.github.io/leaf/#tutorial-async_result.
