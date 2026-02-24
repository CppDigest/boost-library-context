# #356 - test tree is empty when run the example of boost 1.80 [Closed]

> Username: kwarehit  
> Created at: 2022-09-29 00:57:09 UTC  
> Updated at: 2022-12-18 08:39:01 UTC  
> Closed at: 2022-12-18 08:39:01 UTC  
> Url: https://github.com/boostorg/test/issues/356  

hi all,  
  The following code was copied from [https://www.boost.org/doc/libs/1_80_0/libs/test/doc/html/boost_test/runtime_config/custom_command_line_arguments.html](url), but output results is "test tree is empty" when run it by used command line "--log_level=all  -- --test-file test_file.txt":  
`Test setup error: test tree is empty `  
The contents of test_file.txt:  
```  
10.2 30.4  
10.3 30.2  
15.987984 15.9992  
15.997984 15.9962  
```  
code is here:  
```  
#define BOOST_TEST_MODULE runtime_configuration4  
  
#include <boost/test/included/unit_test.hpp>  
#include <boost/test/data/test_case.hpp>  
  
#include <iostream>  
#include <functional>  
#include <sstream>  
#include <fstream>  
  
// this dataset loads a file that contains a list of strings  
// this list is used to create a dataset test case.  
class file_dataset  
{  
private:  
    std::string m_filename;  
    std::size_t m_line_start;  
    std::size_t m_line_end;  
  
public:  
    static const int arity = 2;  
  
public:  
    file_dataset(std::size_t line_start = 0, std::size_t line_end = std::size_t(-1))  
    : m_line_start(line_start)  
    , m_line_end(line_end)  
    {  
      int argc = boost::unit_test::framework::master_test_suite().argc;  
      char** argv = boost::unit_test::framework::master_test_suite().argv;  
  
      if(argc != 3)  
        throw std::logic_error("Incorrect number of arguments");  
      if(std::string(argv[1]) != "--test-file")  
        throw std::logic_error("First argument != '--test-file'");  
      if(!(m_line_start < std::size_t(-1)))  
        throw std::logic_error("Incorrect line start/end");  
  
      m_filename = argv[2];  
  
      std::ifstream file(m_filename);  
      if(!file.is_open())  
        throw std::logic_error("Cannot open the file '" + m_filename + "'");  
      std::size_t nb_lines = std::count_if(  
        std::istreambuf_iterator<char>(file),  
        std::istreambuf_iterator<char>(),  
        [](char c){ return c == '\n';});  
  
      m_line_end = (std::min)(nb_lines, m_line_end);  
      if(!(m_line_start <= m_line_end))  
        throw std::logic_error("Incorrect line start/end");  
    }  
  
    struct iterator {  
        iterator(std::string const& filename, std::size_t line_start)  
        : file(filename, std::ios::binary) {  
          if(!file.is_open())  
            throw std::runtime_error("Cannot open the file");  
          for(std::size_t i = 0; i < line_start; i++) {  
            getline(file, m_current_line);  
          }  
        }  
  
        auto operator*() const -> std::tuple<float, float> {  
          float a, b;  
          std::istringstream istr(m_current_line);  
          istr >> a >> b;  
          return std::tuple<float, float>(a, b);  
        }  
  
        void operator++() {  
          getline(file, m_current_line);  
        }  
    private:  
        std::ifstream file;  
        std::string m_current_line;  
    };  
  
    // size of the DS  
    boost::unit_test::data::size_t size() const {  
      return m_line_end - m_line_start;  
    }  
  
    // iterator over the lines of the file  
    iterator begin() const   {  
      return iterator(m_filename, m_line_start);  
    }  
};  
  
namespace boost { namespace unit_test { namespace data {  
  
namespace monomorphic {  
  template <>  
  struct is_dataset<file_dataset> : boost::mpl::true_ {};  
}  
}}}  
  
BOOST_DATA_TEST_CASE(command_line_test_file,  
    boost::unit_test::data::make_delayed<file_dataset>( 3, 10 ),  
    input, expected) {  
    BOOST_TEST(input <= expected);  
}  
```  
Compiler is: vs2022.17.3 which default C++ standard is 14, boost version is 1.80  
Looking forward to your reply, thanks

---

## Comment 1

> Username: kwarehit  
> Created at: 2022-09-29 07:08:18 UTC  
> Url: https://github.com/boostorg/test/issues/356#issuecomment-1261858476  

I found root cause is that calling of ```boost::unit_test::data::make_delayed``` dose not set value to m_dataset which is inside member of class ```boost::unit_test::data::monomorphic::delayed_dataset```, so the workflow will not do generate ```test_unit```, the key code snippet is there:  
```  
template<typename DataSet, typename Action>  
inline typename std::enable_if<monomorphic::is_dataset<DataSet>::value,void>::type  
for_each_sample( DataSet const &    samples,  
                 Action const&      act,  
                 data::size_t       number_of_samples = BOOST_TEST_DS_INFINITE_SIZE )  
{  
    data::size_t size = (std::min)( samples.size(), number_of_samples );  
    .....  
}  
```   
in above code snippet, the variable ```size``` is ```0```, so there is no test case to be run  
  
I think member function ```file_dataset::size() ``` on example should be call in above parameter ```samples```

---

## Comment 2

> Username: kwarehit  
> Created at: 2022-09-29 07:37:48 UTC  
> Updated at: 2022-09-29 07:46:36 UTC  
> Url: https://github.com/boostorg/test/issues/356#issuecomment-1261887035  

Final, the reason is content in the test_file.txt, because these are absence of new character '\n', if add a new line, the case will be run.  
```  
10.2 30.4  
10.3 30.2  
15.987984 15.9992  
15.997984 15.9962  
<= this is a new line  
```   
I think the error prompt of boost.test should be more details but not generic message "Test setup error: test tree is empty", Is this room for improvement?

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2022-09-29 09:54:11 UTC  
> Url: https://github.com/boostorg/test/issues/356#issuecomment-1262046041  

Thanks for this investigation!
