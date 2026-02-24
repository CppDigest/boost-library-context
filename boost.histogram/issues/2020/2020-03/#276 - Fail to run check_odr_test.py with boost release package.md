# #276 - Fail to run check_odr_test.py with boost release package [Closed]

> Username: yuxianch  
> Created at: 2020-03-25 11:25:59 UTC  
> Updated at: 2020-04-05 22:47:44 UTC  
> Closed at: 2020-04-05 22:47:44 UTC  
> Url: https://github.com/boostorg/histogram/issues/276  

Test check_odr_test.py is trying to check header files of histogram. In this test, the path of header files is defined as below:  
`include_path = os.path.join(this_path, "..", "include")`  
This works for this github project, but for the boost package provided in [boost.org](https://www.boost.org/users/history/version_1_72_0.html), the path of header files is changed and there are also many other header files. This differences make this test failed when we're using boost package.  
I think this test should be updated to fit for this project and also boost package. What do you think?

---

## Comment 1

> Username: HDembinski  
> Created at: 2020-04-03 13:24:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/276#issuecomment-608431582  

Thank you for reporting this bug, you are right of course. I think I can fix this by restricting the test to headers in the histogram subfolder.

---

## Comment 2

> Username: HDembinski  
> Created at: 2020-04-05 22:47:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/276#issuecomment-609497751  

Fixed in master
