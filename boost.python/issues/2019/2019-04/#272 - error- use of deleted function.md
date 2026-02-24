# #272 - error: use of deleted function [Closed]

> Username: mahuichao  
> Created at: 2019-04-10 07:22:15 UTC  
> Updated at: 2020-03-12 12:49:13 UTC  
> Closed at: 2019-08-02 16:48:59 UTC  
> Url: https://github.com/boostorg/python/issues/272  

I have a class **PhoneExtractor**,the header file like:  
`class PhoneExtractor{  
        public:  
                PhoneExtractor(){}  
                PhoneExtractor(string conf_file_name);  
                //PhoneExtractor();  
                ~PhoneExtractor();  
}`  
I use boost python,the file like:  
`using namespace boost::python;  
  
BOOST_PYTHON_MODULE(libextractor)  
{  
        class_<PhoneExtractor>("PhoneExtractor",init<std::string>())  
        .def("chainDecode", &PhoneExtractor::chainDecode)  
        ;  
}`  
  
But I got error:  
  
> /home/mahch/workspace/dep/lib/include/boost/python/object/value_holder.hpp:133:13: error: use of deleted function ‘PhoneExtractor::PhoneExtractor(const PhoneExtractor&)’  
             BOOST_PP_REPEAT_1ST(N, BOOST_PYTHON_UNFORWARD_LOCAL, nil)  
  
I don't know where I am wrong,can you help me ?

---

## Comment 1

> Username: prajwaljpj  
> Created at: 2019-08-02 16:42:37 UTC  
> Url: https://github.com/boostorg/python/issues/272#issuecomment-517768784  

@mahuichao I'm facing the same error. Did you find a way to solve this?

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2020-03-12 12:49:13 UTC  
> Url: https://github.com/boostorg/python/issues/272#issuecomment-598167133  

The code snippet above is ill-formatted and incomplete. Could you please re-post it with the correct spelling / formatting, so I don't have to second-guess what you are trying to do ?
