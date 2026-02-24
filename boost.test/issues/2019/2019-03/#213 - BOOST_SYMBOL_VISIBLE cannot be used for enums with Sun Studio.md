# #213 - BOOST_SYMBOL_VISIBLE cannot be used for enums with Sun Studio [Closed]

> Username: juanalday  
> Created at: 2019-03-21 14:04:25 UTC  
> Updated at: 2019-03-29 08:10:47 UTC  
> Closed at: 2019-03-28 17:44:20 UTC  
> Url: https://github.com/boostorg/test/issues/213  

https://github.com/boostorg/test/blob/b9d74d1f55be2d70cdaee9bdb3ebe9445ce5c7b3/include/boost/test/detail/log_level.hpp#L25  
  
In sun_cc, BOOST_SYMBOL_VISIBLE expands to __global. Unfortunately, that attribute cannot be used with enums.   
According to the [documentation,](https://docs.oracle.com/cd/E19205-01/819-5267/bkaed/index.html) "The specifier, in this case, follows the struct, class, or union keyword"  
  
This change effectively breaks all builds in Sun Studio  
Tested with the following versions of SUNWspro:  
  
- 12.3  
- 12.4  
- 12.5  
- 12.6  
  
I can send a PR to either remove BOOST_SYMBOL_VISIBLE of ifdef it based on __SUNPRO_CC, whatever works best...  
  
I can't send a godbolt since it doesn't support sun_cc, but this snippet shows the problem:  
  
`enum __global foo {bar = 1};  
int main() {}`  
  
> CC main.cpp  
> "main.cpp", line 1: Error: Identifier expected instead of "__global".  
> "main.cpp", line 1: Error: "," expected instead of "{".  
> 2 Error(s) detected.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-03-22 00:17:56 UTC  
> Url: https://github.com/boostorg/test/issues/213#issuecomment-475450373  

Thanks for the report and sorry for the issue/regression. Unfortunately we do not have any regression testing for this platform.   
  
In all cases, a PR with an `ifdef` would be the best. We are pretty advanced in the release process and I am not sure this will get in for 1.70. In the meantime you will have to patch or take the next master.
