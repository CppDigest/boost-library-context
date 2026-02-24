# #60 - Test failures on Cygwin g++ [Closed]

> Username: pdimov  
> Created at: 2017-11-22 01:35:29 UTC  
> Updated at: 2017-11-22 17:06:34 UTC  
> Closed at: 2017-11-22 17:06:34 UTC  
> Url: https://github.com/boostorg/filesystem/issues/60  

```  
temp_directory_path_tests...  
test\operations_test.cpp(1981): test 'p == tmp_path' failed in function 'void {anonymous}::temp_directory_path_tests()': '"op-test-674d-dc9b\123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890#"' != '"C:\Users\PETERD~1\AppData\Local\Temp"'  
test\operations_test.cpp(1991): test 'tmp_path == fs::initial_path()' failed in function 'void {anonymous}::temp_directory_path_tests()': '"C:\Users\PETERD~1\AppData\Local\Temp"' != '"C:\Projects\boost-git\boost\libs\filesystem"'  
Fallback test, temp_directory_path() returned "C:\Users\PETERD~1\AppData\Local\Temp"  
test\operations_test.cpp(2095): test 'equivalent(test_temp_dir, ph)' failed in function 'void {anonymous}::temp_directory_path_tests()'  
test\operations_test.cpp(2100): test 'equivalent(test_temp_dir, ph)' failed in function 'void {anonymous}::temp_directory_path_tests()'  
test\operations_test.cpp(2107): test 'equivalent(test_temp_dir/L"Temp", ph)' failed in function 'void {anonymous}::temp_directory_path_tests()'  
temp_directory_path() returned "C:\Users\PETERD~1\AppData\Local\Temp"  
test\operations_test.cpp(2113): test 'equivalent(test_temp_dir/L"Temp", ph)' failed in function 'void {anonymous}::temp_directory_path_tests()'  
temp_directory_path() returned "C:\Users\PETERD~1\AppData\Local\Temp"  
```  
  
It looks like `guarded_env_var` doesn't work.

---

## Comment 1

> Username: pdimov  
> Created at: 2017-11-22 15:53:26 UTC  
> Url: https://github.com/boostorg/filesystem/issues/60#issuecomment-346391912  

Attempted fix at https://github.com/boostorg/filesystem/commit/03eb5df380aa3df743041bd4fb33d0e60d9c17c4.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-11-22 17:06:34 UTC  
> Url: https://github.com/boostorg/filesystem/issues/60#issuecomment-346414186  

Merged to develop; Travis and Appveyor need to be all green to be useful for testing pull requests.
