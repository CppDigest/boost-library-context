# #324 - Add hook to cancel running the tests [Open]

> Username: fsimonis  
> Created at: 2022-01-28 14:53:10 UTC  
> Updated at: 2022-03-25 12:22:11 UTC  
> Url: https://github.com/boostorg/test/issues/324  

I am working with a boost test suite, which needs to run under some conditions.  
There is currently no way of checking these conditions and exiting early, while keeping the boost test command line flags working.  
  
* If I use a custom main and exit before calling boost test, then _obviously_ no flags will be checked by boost test.  
* If I use a custom `unit_test_init()`, then flags like `--help` will work.  
  The flags `--list_content` and `--list_labels` are handled after `unit_test_init()`, so they won't work.  
  
Currently, I either have to either sacrifice the very useful `--list_content` and `--list_labels` flags, or I have to exit in the first test.  
  
I propose to add another user-definable hook, which allows the user to cancel the testsuite:  
https://github.com/boostorg/test/blob/9d863d07e864ef663e3e8573b55905099b938d3e/include/boost/test/impl/unit_test_main.ipp#L250  
  
This could be a simple predicate:  
```cpp  
typedef bool (*cancel_unit_test_func)();  
  
// For backwards compatibility  
int BOOST_TEST_DECL  
unit_test_main( init_unit_test_func init_func, int argc, char* argv[] ) {  
  return unit_test_main(init_func, nullptr, argc, argv);  
}  
  
// New main with additional cancel_unit_test_func  
int BOOST_TEST_DECL  
unit_test_main( init_unit_test_func init_func, cancel_unit_test_func cancel_func, int argc, char* argv[] ) {  
  // ...  
  
  if(cancel_func && cancel_func()) {  
    return boost::exit_cancelled;  
  }  
  
  framework::run()  
  // ...  
}  
```

---

## Comment 1

> Username: fsimonis  
> Created at: 2022-01-28 16:01:03 UTC  
> Url: https://github.com/boostorg/test/issues/324#issuecomment-1024357774  

For reference, the closest I could come up with is conditionally attaching a fixture to the master suite in my custom `unit_test_init()`:  
  
```cpp  
  if (condition) {  
    struct GoBoom {  
      GoBoom() { std::cerr << "Boom!\n"; std::exit(1); }  
    };  
    auto &master_suite = boost:unit_test::framework::master_test_suite();  
    auto &fixtures = master_suite.p_fixtures.value;  
    fixtures.insert( fixtures.begin(), boost::unit_test::test_unit_fixture_ptr(  
        new boost::unit_test::class_based_fixture<GoBoom>()));  
  }  
```  
  
This still displays the number of executed tests though:  
```  
$ ./mytest  
Running 123 test cases...  
Boom!  
```

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2022-03-03 21:46:02 UTC  
> Url: https://github.com/boostorg/test/issues/324#issuecomment-1058524101  

There are several users asking for a way to skip the unit tests from within unit test. I was thinking that this "skipping" feature, combined with a global fixture, should work for the case you are describing:  
  
* the command line arguments are available to the fixture through the master test suite object: this would let the fixture decide whether the tests should be skipped or not  
* a global fixture is like a fixture attached to the master test suite: skipping there would flag all the tests as skipped  
  
Now the question would be:  
  
* what would be for you the logic for deciding the tests should run or not? can this logic rely solely on the command line arguments or is there anything else?  
* is skipping good enough for you?  
  
NB: I believe that you can still create a global fixture that accesses the command line and fail (ctor/setup) with the conditions are met. The downside is the signal "failed" that may need to be handled outside of the test module (test runner etc).

---

## Comment 3

> Username: fsimonis  
> Created at: 2022-03-25 12:22:11 UTC  
> Url: https://github.com/boostorg/test/issues/324#issuecomment-1078977800  

After some cleanup of our test setup, I was able to implement this using a global fixture.   
I'll still describe my use-case even though it is a bit exotic.  
  
### Problem description  
  
I am using boost to test an MPI-based application. These tests need run on 4 MPI ranks, which fundamentally means that the executable is started 4 times forming a "communicator" of size 4 with ranks 0, 1, 2, and 3.  
A user who runs this executable without MPI runs into an error, as executing without MPI defaults to a communicator of size 1 and rank 0.  
```console  
$ ./test  
Error: please run with mpirun -n 4.  
$ mpirun -n 4 ./test  
Running x tests ...  
```  
  
However, a user may still want to run the executable without MPI to invoke `--help` or list all tests `--list_content`, as running using MPI results in interleaved output of 4 instances. `--list_content` is handled after the call of unit_init, so I cannot use it to stop the execution.  
  
### Downsides of a global fixture  
  
I can use a global fixture to stop the tests, but this has some downsides:  
Firstly, the "stop" fixture is not necessarily being handled first. So other global fixures can not rely on the environment being fully functional.  
  
Secondly, even though the tests will definitely not run, the output still contains a lot of misleading output.  
  
Implemented by throwing a `std::runtime_error`  
```console   
$ ./test  
Running 528 test cases...  
Entering test module "Master test suite"  
Please run on 4 rans  
unknown location(0): fatal error: in "Master test suite": std::runtime_error: Error: please run with mpirun -n 4.  
Leaving test module "Master test suite"  
  
*** The test module "Master test suite" was aborted; see standard output for details  
*** 1 failure is detected in the test module "Master test suite"  
```  
  
Implemented writing to `cerr` and calling `std::exit()`:  
```console   
$ ./test  
Running 528 test cases...  
Entering test module "Master test suite"  
Error: please run with mpirun -n 4.  
```  
  
My goal would be for the user to receive the following output:  
```console   
$ ./test  
Error: please run with mpirun -n 4.  
```  
  
### Command-line parsing in global fixtures  
  
As a side-note, the same problem applies to parsing additional command-line arguments in global fixtures. While errors could be checked before running the test suite, the output looks like a test failed inside the test suite.  
  
### Some suggestions  
  
One could add the reason to the return type of the cancel function:  
```cpp  
struct cancel_func_result {  
  bool cancel;  
  std::string reason;  
};  
```  
That way, the framework could print the reason in red or wrap it in additional text.  
  
An alternative to such a function would be to add framework fixtures, which are setup before the running the testsuite and torn down afterwards. This would be the place to parse command-line arguments and check overall availability (insufficient MPI ranks, server not reachable, not able to login to a database, ... )
