# #22 - Undefined reference [Closed]

> Username: marcown  
> Created at: 2017-02-03 10:02:03 UTC  
> Updated at: 2017-02-03 15:57:07 UTC  
> Closed at: 2017-02-03 15:57:07 UTC  
> Url: https://github.com/boostorg/histogram/issues/22  

Hey there,  
  
I want to to use your project for an c++ university project. I'm running arch linux.  
  
Two Issues:  
  
1) Following:  
```  
git clone https://github.com/HDembinski/histogram.git  
mkdir build && cd build  
cmake ../histogram/build  
make install # (or just 'make' to run the tests)  
  
```  
  
give the following output:  
  
```  
`/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyString_Size'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyUnicodeUCS4_FromEncodedObject'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyFile_FromString'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyString_Type'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyInt_Type'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyString_FromString'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyUnicodeUCS4_AsWideChar'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyString_FromStringAndSize'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `Py_InitModule4_64'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyString_FromFormat'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyNumber_Divide'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyNumber_InPlaceDivide'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyInt_AsLong'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyString_InternFromString'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyClass_Type'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyString_AsString'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyInt_FromLong'  
/usr/lib/gcc/x86_64-pc-linux-gnu/6.3.1/../../../../lib/libboost_python.so: undefined reference to `PyFile_AsFile'  
collect2: Fehler: ld gab 1 als Ende-Status zurück  
make[2]: *** [CMakeFiles/axis_test.dir/build.make:102: axis_test] Fehler 1  
make[1]: *** [CMakeFiles/Makefile2:68: CMakeFiles/axis_test.dir/all] Fehler 2  
make: *** [Makefile:139: all] Fehler 2  
`  
  
  
  
  
  
**2) Then I tried to just use the c++ headers yielding to**  
  
  
/tmp/ccB9sU5c.o: In function `main':  
main.cpp:(.text+0x57): undefined reference to `boost::histogram::regular_axis::regular_axis(int, double, double, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, bool)'  
/tmp/ccB9sU5c.o: In function `boost::histogram::detail::nstore::~nstore()':  
main.cpp:(.text._ZN5boost9histogram6detail6nstoreD2Ev[_ZN5boost9histogram6detail6nstoreD5Ev]+0x14): undefined reference to `boost::histogram::detail::nstore::destroy()'  
/tmp/ccB9sU5c.o: In function `boost::histogram::histogram::histogram(boost::variant<boost::histogram::regular_axis, boost::histogram::polar_axis, boost::histogram::variable_axis, boost::histogram::category_axis, boost::histogram::integer_axis> const&)':  
main.cpp:(.text._ZN5boost9histogram9histogramC2ERKNS_7variantINS0_12regular_axisEJNS0_10polar_axisENS0_13variable_axisENS0_13category_axisENS0_12integer_axisEEEE[_ZN5boost9histogram9histogramC5ERKNS_7variantINS0_12regular_axisEJNS0_10polar_axisENS0_13variable_axisENS0_13category_axisENS0_12integer_axisEEEE]+0x2c): undefined reference to `boost::histogram::basic_histogram::field_count() const'  
main.cpp:(.text._ZN5boost9histogram9histogramC2ERKNS_7variantINS0_12regular_axisEJNS0_10polar_axisENS0_13variable_axisENS0_13category_axisENS0_12integer_axisEEEE[_ZN5boost9histogram9histogramC5ERKNS_7variantINS0_12regular_axisEJNS0_10polar_axisENS0_13variable_axisENS0_13category_axisENS0_12integer_axisEEEE]+0x47): undefined reference to `boost::histogram::detail::nstore::nstore(unsigned long, boost::histogram::detail::nstore::depth_type)'  
/tmp/ccB9sU5c.o: In function `boost::detail::variant::make_initializer_node::apply<boost::mpl::pair<boost::detail::variant::initializer_root, mpl_::int_<0> >, boost::mpl::l_iter<boost::mpl::list5<boost::histogram::regular_axis, boost::histogram::polar_axis, boost::histogram::variable_axis, boost::histogram::category_axis, boost::histogram::integer_axis> > >::initializer_node::initialize(void*, boost::histogram::regular_axis&&)':  
main.cpp:(.text._ZN5boost6detail7variant21make_initializer_node5applyINS_3mpl4pairINS1_16initializer_rootEN4mpl_4int_ILi0EEEEENS4_6l_iterINS4_5list5INS_9histogram12regular_axisENSD_10polar_axisENSD_13variable_axisENSD_13category_axisENSD_12integer_axisEEEEEE16initializer_node10initializeEPvOSE_[_ZN5boost6detail7variant21make_initializer_node5applyINS_3mpl4pairINS1_16initializer_rootEN4mpl_4int_ILi0EEEEENS4_6l_iterINS4_5list5INS_9histogram12regular_axisENSD_10polar_axisENSD_13variable_axisENSD_13category_axisENSD_12integer_axisEEEEEE16initializer_node10initializeEPvOSE_]+0x44): undefined reference to `boost::histogram::regular_axis::regular_axis(boost::histogram::regular_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::copy_into::internal_visit<boost::histogram::regular_axis>(boost::histogram::regular_axis const&, int) const':  
main.cpp:(.text._ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram12regular_axisEEEvRKT_i[_ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram12regular_axisEEEvRKT_i]+0x3f): undefined reference to `boost::histogram::regular_axis::regular_axis(boost::histogram::regular_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::copy_into::internal_visit<boost::histogram::regular_axis>(boost::detail::variant::backup_holder<boost::histogram::regular_axis> const&, long) const':  
main.cpp:(.text._ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram12regular_axisEEEvRKNS1_13backup_holderIT_EEl[_ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram12regular_axisEEEvRKNS1_13backup_holderIT_EEl]+0x4b): undefined reference to `boost::histogram::regular_axis::regular_axis(boost::histogram::regular_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::copy_into::internal_visit<boost::histogram::polar_axis>(boost::histogram::polar_axis const&, int) const':  
main.cpp:(.text._ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram10polar_axisEEEvRKT_i[_ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram10polar_axisEEEvRKT_i]+0x3f): undefined reference to `boost::histogram::polar_axis::polar_axis(boost::histogram::polar_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::copy_into::internal_visit<boost::histogram::polar_axis>(boost::detail::variant::backup_holder<boost::histogram::polar_axis> const&, long) const':  
main.cpp:(.text._ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram10polar_axisEEEvRKNS1_13backup_holderIT_EEl[_ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram10polar_axisEEEvRKNS1_13backup_holderIT_EEl]+0x4b): undefined reference to `boost::histogram::polar_axis::polar_axis(boost::histogram::polar_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::copy_into::internal_visit<boost::histogram::variable_axis>(boost::histogram::variable_axis const&, int) const':  
main.cpp:(.text._ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram13variable_axisEEEvRKT_i[_ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram13variable_axisEEEvRKT_i]+0x3f): undefined reference to `boost::histogram::variable_axis::variable_axis(boost::histogram::variable_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::copy_into::internal_visit<boost::histogram::variable_axis>(boost::detail::variant::backup_holder<boost::histogram::variable_axis> const&, long) const':  
main.cpp:(.text._ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram13variable_axisEEEvRKNS1_13backup_holderIT_EEl[_ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram13variable_axisEEEvRKNS1_13backup_holderIT_EEl]+0x4b): undefined reference to `boost::histogram::variable_axis::variable_axis(boost::histogram::variable_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::copy_into::internal_visit<boost::histogram::category_axis>(boost::histogram::category_axis const&, int) const':  
main.cpp:(.text._ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram13category_axisEEEvRKT_i[_ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram13category_axisEEEvRKT_i]+0x3f): undefined reference to `boost::histogram::category_axis::category_axis(boost::histogram::category_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::copy_into::internal_visit<boost::histogram::category_axis>(boost::detail::variant::backup_holder<boost::histogram::category_axis> const&, long) const':  
main.cpp:(.text._ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram13category_axisEEEvRKNS1_13backup_holderIT_EEl[_ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram13category_axisEEEvRKNS1_13backup_holderIT_EEl]+0x4b): undefined reference to `boost::histogram::category_axis::category_axis(boost::histogram::category_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::copy_into::internal_visit<boost::histogram::integer_axis>(boost::histogram::integer_axis const&, int) const':  
main.cpp:(.text._ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram12integer_axisEEEvRKT_i[_ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram12integer_axisEEEvRKT_i]+0x3f): undefined reference to `boost::histogram::integer_axis::integer_axis(boost::histogram::integer_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::copy_into::internal_visit<boost::histogram::integer_axis>(boost::detail::variant::backup_holder<boost::histogram::integer_axis> const&, long) const':  
main.cpp:(.text._ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram12integer_axisEEEvRKNS1_13backup_holderIT_EEl[_ZNK5boost6detail7variant9copy_into14internal_visitINS_9histogram12integer_axisEEEvRKNS1_13backup_holderIT_EEl]+0x4b): undefined reference to `boost::histogram::integer_axis::integer_axis(boost::histogram::integer_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::move_into::internal_visit<boost::histogram::regular_axis>(boost::histogram::regular_axis&, int) const':  
main.cpp:(.text._ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram12regular_axisEEEvRT_i[_ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram12regular_axisEEEvRT_i]+0x4a): undefined reference to `boost::histogram::regular_axis::regular_axis(boost::histogram::regular_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::move_into::internal_visit<boost::histogram::regular_axis>(boost::detail::variant::backup_holder<boost::histogram::regular_axis>&, long) const':  
main.cpp:(.text._ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram12regular_axisEEEvRNS1_13backup_holderIT_EEl[_ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram12regular_axisEEEvRNS1_13backup_holderIT_EEl]+0x53): undefined reference to `boost::histogram::regular_axis::regular_axis(boost::histogram::regular_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::move_into::internal_visit<boost::histogram::polar_axis>(boost::histogram::polar_axis&, int) const':  
main.cpp:(.text._ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram10polar_axisEEEvRT_i[_ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram10polar_axisEEEvRT_i]+0x4a): undefined reference to `boost::histogram::polar_axis::polar_axis(boost::histogram::polar_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::move_into::internal_visit<boost::histogram::polar_axis>(boost::detail::variant::backup_holder<boost::histogram::polar_axis>&, long) const':  
main.cpp:(.text._ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram10polar_axisEEEvRNS1_13backup_holderIT_EEl[_ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram10polar_axisEEEvRNS1_13backup_holderIT_EEl]+0x53): undefined reference to `boost::histogram::polar_axis::polar_axis(boost::histogram::polar_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::move_into::internal_visit<boost::histogram::variable_axis>(boost::histogram::variable_axis&, int) const':  
main.cpp:(.text._ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram13variable_axisEEEvRT_i[_ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram13variable_axisEEEvRT_i]+0x4a): undefined reference to `boost::histogram::variable_axis::variable_axis(boost::histogram::variable_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::move_into::internal_visit<boost::histogram::variable_axis>(boost::detail::variant::backup_holder<boost::histogram::variable_axis>&, long) const':  
main.cpp:(.text._ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram13variable_axisEEEvRNS1_13backup_holderIT_EEl[_ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram13variable_axisEEEvRNS1_13backup_holderIT_EEl]+0x53): undefined reference to `boost::histogram::variable_axis::variable_axis(boost::histogram::variable_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::move_into::internal_visit<boost::histogram::category_axis>(boost::histogram::category_axis&, int) const':  
main.cpp:(.text._ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram13category_axisEEEvRT_i[_ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram13category_axisEEEvRT_i]+0x4a): undefined reference to `boost::histogram::category_axis::category_axis(boost::histogram::category_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::move_into::internal_visit<boost::histogram::category_axis>(boost::detail::variant::backup_holder<boost::histogram::category_axis>&, long) const':  
main.cpp:(.text._ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram13category_axisEEEvRNS1_13backup_holderIT_EEl[_ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram13category_axisEEEvRNS1_13backup_holderIT_EEl]+0x53): undefined reference to `boost::histogram::category_axis::category_axis(boost::histogram::category_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::move_into::internal_visit<boost::histogram::integer_axis>(boost::histogram::integer_axis&, int) const':  
main.cpp:(.text._ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram12integer_axisEEEvRT_i[_ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram12integer_axisEEEvRT_i]+0x4a): undefined reference to `boost::histogram::integer_axis::integer_axis(boost::histogram::integer_axis const&)'  
/tmp/ccB9sU5c.o: In function `void boost::detail::variant::move_into::internal_visit<boost::histogram::integer_axis>(boost::detail::variant::backup_holder<boost::histogram::integer_axis>&, long) const':  
main.cpp:(.text._ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram12integer_axisEEEvRNS1_13backup_holderIT_EEl[_ZNK5boost6detail7variant9move_into14internal_visitINS_9histogram12integer_axisEEEvRNS1_13backup_holderIT_EEl]+0x53): undefined reference to `boost::histogram::integer_axis::integer_axis(boost::histogram::integer_axis const&)'  
collect2: Fehler: ld gab 1 als Ende-Status zurück  
```  
  
Are there any solutions? Thank you in advance!

---

## Comment 1

> Username: HDembinski  
> Created at: 2017-02-03 12:04:31 UTC  
> Url: https://github.com/boostorg/histogram/issues/22#issuecomment-277231893  

Dear marcown,  
  
thanks for using histogram, I will try my best to help you. :)  
  
I can see from your error message, that you have undefined references. "Undefined reference" means that during linking, some pre-compiled code is not found, which is used by the software. In your case, these are functions from the Python library, notably stuff related to strings. This suggests that cmake is not finding the right PythonLib. I suppose that you use Python3, while your boost was linked to Python2. The main difference between the two from the API point of view are the string functions.  
  
Try running  
  
    ccmake .  
  
in your build directory. Look out what is used for PYTHON_INCLUDE_DIR and PYTHON_LIBRARY. Those should point to python2.7, not python3.x. You can manually change those paths, and try to recompile.  
  
If this doesn't work, please attach your CMakeCache.txt, so that I can have a look myself.  
  
If you don't need Python support, you might also be able to fix this by turning it off during compilation. In your build directory, do  
  
    cmake <source-dir> -DUSE_PYTHON=OFF  
  
I hope this helps,  
Hans

---

## Comment 2

> Username: HDembinski  
> Created at: 2017-02-03 12:07:35 UTC  
> Url: https://github.com/boostorg/histogram/issues/22#issuecomment-277232406  

PS: If you are feeling adventurous, you could also try the "static" branch, which I am currently developing. It contains an improved version of the histogram library, which is even faster and header-only, if you don't use the Python module, which has to be compiled.

---

## Comment 3

> Username: marcown  
> Created at: 2017-02-03 15:57:03 UTC  
> Url: https://github.com/boostorg/histogram/issues/22#issuecomment-277285127  

Thank you for your fast answer.  
  
I just used the header-only version of the static branch it works well.  
  
Marc
