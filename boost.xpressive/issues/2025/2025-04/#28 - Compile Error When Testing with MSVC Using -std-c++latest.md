# #28 - Compile Error When Testing with MSVC Using /std:c++latest [Open]

> Username: NEIL-smtg  
> Created at: 2025-04-11 08:06:20 UTC  
> Updated at: 2025-04-11 08:11:23 UTC  
> Url: https://github.com/boostorg/xpressive/issues/28  

Hi, I work on Microsoft Visual C++ testing, where we regularly build popular open-source projects, including Boost, with development builds of our compiler and libraries with some particular CL options such as `/std:c++latest`.  
  
Feel free to close this issue if there is no plan of supporting `/std:c++latest` in the future. This change in `/std:c++latest` will be released soon in next VS update.  
``  
While building and testing Boost from source using `/std:c++latest`, I encountered the following error in the `xpressive` test:  
```c++  
libs\xpressive\test\test_actions.cpp(187): error C2676: binary '[': 'const boost::proto::exprns_::expr<Tag,Args,2>' does not define this operator or a conversion to a type acceptable to the predefined operator  
        with  
        [  
            Tag=boost::proto::tagns_::tag::shift_right,  
            Args=boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const char &>,0>,boost::xpressive::basic_regex<std::_String_const_iterator<std::_String_val<std::_Simple_types<char>>>> &>  
        ]  
```  
  
This error was encountered in:  
[test\test_actions.cpp](https://github.com/boostorg/xpressive/blob/develop/test/test_actions.cpp#L187) (Line 187, 194, 203, 210)  
  
  
In C++23, the definition of a subscript-expression was changed so that it now takes an expression-list so now a top-level ',' is a separator. **The fix is to wrap the expression in parentheses**. For example in `test_actions.cpp`:  
```c++  
    term        = factor >> *(  
                                ('*' >> factor)  
                                    [ right = top(stack)  
                                    , pop(stack)  
                                    , left = top(stack)  
                                    , pop(stack)  
                                    , push(stack, left * right)  
                                    ]  
                              | ('/' >> factor)  
                                    [ right = top(stack)  
                                    , pop(stack)  
                                    , left = top(stack)  
                                    , pop(stack)  
                                    , push(stack, left / right)  
                                    ]  
                             );  
    expression  = term >> *(  
                                ('+' >> term)  
                                    [ right = top(stack)  
                                    , pop(stack)  
                                    , left = top(stack)  
                                    , pop(stack)  
                                    , push(stack, left + right)  
                                    ]  
                              | ('-' >> term)  
                                    [ right = top(stack)  
                                    , pop(stack)  
                                    , left = top(stack)  
                                    , pop(stack)  
                                    , push(stack, left - right)  
                                    ]  
                             );  
```  
  
should be changed to:  
```c++  
    term        = factor >> *(  
                                ('*' >> factor)  
                                    [ (right = top(stack)  // added opening parenthesis  
                                    , pop(stack)  
                                    , left = top(stack)  
                                    , pop(stack)  
                                    , push(stack, left * right))  // added closing parenthesis  
                                    ]  
                              | ('/' >> factor)  
                                    [ (right = top(stack)  
                                    , pop(stack)  
                                    , left = top(stack)  
                                    , pop(stack)  
                                    , push(stack, left / right))  
                                    ]  
                             );  
    expression  = term >> *(  
                                ('+' >> term)  
                                    [ (right = top(stack)  
                                    , pop(stack)  
                                    , left = top(stack)  
                                    , pop(stack)  
                                    , push(stack, left + right))  
                                    ]  
                              | ('-' >> term)  
                                    [ (right = top(stack)  
                                    , pop(stack)  
                                    , left = top(stack)  
                                    , pop(stack)  
                                    , push(stack, left - right))  
                                    ]  
                             );  
```  
  
Test log:   
[boost_xpressive_test_log.txt](https://github.com/user-attachments/files/19701276/boost_xpressive_test_log.txt)
