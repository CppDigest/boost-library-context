# #426 - __qualname__ on embedded/inner classes is wrong [Closed]

> Username: gabrielbocek  
> Created at: 2023-12-04 13:02:29 UTC  
> Updated at: 2024-09-17 21:07:46 UTC  
> Closed at: 2024-09-17 21:07:46 UTC  
> Url: https://github.com/boostorg/python/issues/426  

Hi,  
  
the __qualname__ on embedded classes and enum returns just the embedded class/enum class. The outer class is not included. Here is a simple example:  
  
```  
BOOST_PYTHON_MODULE(TestModule)  
{  
	class TestClass  
	{  
	public:  
		enum class EmbeddedEnum  
		{  
			eOff,  
			eRepeat,  
			eMirror  
		};  
  
		class EmbeddedClass  
		{  
			EmbeddedClass() = default;  
		};  
  
		TestClass() = default;  
	};  
  
	boost::python::class_<TestClass, boost::shared_ptr<TestClass>, boost::noncopyable> boostTestClass("TestClass", boost::python::no_init);  
	boost::python::scope boostTestClassScope = boostTestClass;  
  
	boost::python::enum_<TestClass::EmbeddedEnum> boostTestClassEmbeddedEnum("EmbeddedEnum");  
	boostTestClassEmbeddedEnum.value("off", TestClass::EmbeddedEnum::eOff);  
	boostTestClassEmbeddedEnum.value("repeat", TestClass::EmbeddedEnum::eRepeat);  
	boostTestClassEmbeddedEnum.value("mirror", TestClass::EmbeddedEnum::eMirror);  
	boostTestClassEmbeddedEnum.export_values();  
  
	boost::python::class_<TestClass::EmbeddedClass, boost::shared_ptr<TestClass::EmbeddedClass>, boost::noncopyable> boostEmbeddedTestClass("EmbeddedClass", boost::python::no_init);  
}  
```  
  
`TestModule.TestClass.EmbeddedClass.__qualname__` returns just `EmbeddedClass`. Similarly the `TestModule.TestClass.EmbeddedEnum` returns `EmbeddedEnum`.  
  
It should be `TestClass.EmbeddedClass` and `TestClass.EmbeddedEnum` respectively.
