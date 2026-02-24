# #153 - Conditionally skipping a test case of a test suite causes failure of the test run [Closed]

> Username: madebr  
> Created at: 2018-08-28 16:36:49 UTC  
> Updated at: 2018-08-28 19:18:31 UTC  
> Closed at: 2018-08-28 19:12:33 UTC  
> Url: https://github.com/boostorg/test/issues/153  

When conditionally skipping a test case, which is a member of a test suite, the suite fails.  
A skipped test should just increment the skip count but not fail the test run.  
  
  
  
## Reproducer  
  
shell:  
```  
mkdir build && cd build && cmake .. && make  
# The next test run succeeds: all test cases are ran  
./disable_test_case  
# The next test run fails: some test cases are skipped  
./disable_test_case -- --disable  
```  
  
CMakeLists.txt:  
```  
cmake_minimum_required(VERSION 3.8)  
project(boost_test_disable_suite)  
  
find_package(Boost COMPONENTS unit_test_framework program_options)  
  
add_executable(disable_test_case  
    main.cpp  
    )  
  
target_link_libraries(disable_test_case  
    PUBLIC  
        Boost::unit_test_framework  
        Boost::program_options  
    )  
  
target_compile_definitions(disable_test_case  
    PUBLIC  
        BOOST_ALL_DYN_LINK  
    )  
  
```  
  
main.cpp:  
```  
#define BOOST_TEST_MODULE disable_test_suite  
#include <boost/test/unit_test.hpp>  
#include <boost/test/results_collector.hpp>  
  
#include <boost/program_options.hpp>  
  
#include <iostream>  
  
namespace utf = boost::unit_test;  
namespace tt = boost::test_tools;  
  
static bool globalFixtureRan = false;  
static bool enableTests = true;  
  
class GlobalFixture {  
public:  
    GlobalFixture() {  
        int argc = utf::framework::master_test_suite().argc;  
        char** argv= utf::framework::master_test_suite().argv;  
        parse_args(argc, argv);  
  
        globalFixtureRan = true;  
    }  
private:  
    void parse_args(int argc, char** argv) {  
        namespace po = boost::program_options;  
        po::options_description test_options("Test options");  
        test_options.add_options()(  
            "disable", "Disable some tests")(  
            "help", "Show this help message");  
  
        po::variables_map vm;  
        try {  
            po::store(po::parse_command_line(argc, argv, test_options), vm);  
            po::notify(vm);  
        } catch (po::error& ex) {  
            std::cout << "Error parsing arguments: " << ex.what() << std::endl;  
            std::cerr << test_options;  
            _exit(EXIT_FAILURE);  
        }  
        if (vm.count("help")) {  
            std::cout << test_options;  
            _exit(EXIT_SUCCESS);  
        }  
  
        if (vm.count("disable")) {  
            enableTests = false;  
        }  
    }  
};  
  
BOOST_TEST_GLOBAL_FIXTURE(GlobalFixture);  
  
struct enable_test {  
    tt::assertion_result operator()(utf::test_unit_id id) {  
        tt::assertion_result ans(enableTests);  
        if (!enableTests) {  
            auto& master = utf::framework::master_test_suite();  
            master.remove(id);  
        }  
        return enableTests;  
    }  
};  
  
BOOST_AUTO_TEST_SUITE(SecondSuccessSuiteWithConditionalTestCase)  
BOOST_AUTO_TEST_CASE(conditional_success,  
                     * utf::precondition(enable_test{})) {  
    BOOST_REQUIRE(true);  
}  
BOOST_AUTO_TEST_CASE(unconditional_success) {  
    BOOST_REQUIRE(true);  
}  
BOOST_AUTO_TEST_SUITE_END()  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-08-28 18:52:24 UTC  
> Url: https://github.com/boostorg/test/issues/153#issuecomment-416700178  

Thanks for the report. This is with the last version 1.68 right?

---

## Comment 2

> Username: madebr  
> Created at: 2018-08-28 18:57:00 UTC  
> Updated at: 2018-08-28 18:59:35 UTC  
> Url: https://github.com/boostorg/test/issues/153#issuecomment-416701633  

No, this is with 1.66.0. The most recent version with fedora 28. I'm sorry for the possible confusion.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2018-08-28 18:59:34 UTC  
> Url: https://github.com/boostorg/test/issues/153#issuecomment-416702481  

Thanks for the quick reply. One bug was solved in 1.68 that I think relates to this one: https://svn.boost.org/trac10/ticket/12095 . I believe this addresses the issue here. Would you be kind enough to try?

---

## Comment 4

> Username: madebr  
> Created at: 2018-08-28 19:12:33 UTC  
> Url: https://github.com/boostorg/test/issues/153#issuecomment-416706497  

Yup, 1.68.0 fixes my problem. Btw, I didn't know building boost was this easy.

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2018-08-28 19:18:31 UTC  
> Url: https://github.com/boostorg/test/issues/153#issuecomment-416708255  

Very good!
