# #82 - xml_iarchive destructor calls abort() [Closed]

> Username: kravlost  
> Created at: 2017-12-21 10:05:41 UTC  
> Updated at: 2021-11-13 04:06:26 UTC  
> Closed at: 2018-04-04 14:25:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/82  

In the following Unit Test from VS2017 15.5.2 with Boost 1.66.0, boost::archive::xml_iarchive() is successfully used to load an integer. vcpkg and Boost.org distributions show the same error. When it goes out of scope, its destructor calls abort(). Boost 1.65.1 works with no error.  
  
```  
#include "stdafx.h"  
#include "CppUnitTest.h"  
  
using namespace Microsoft::VisualStudio::CppUnitTestFramework;  
  
#include <boost/archive/archive_exception.hpp>  
#include <boost/serialization/variant.hpp>  
  
#include <boost/archive/xml_iarchive.hpp>  
#include <boost/archive/xml_oarchive.hpp>  
  
#include <boost/serialization/split_member.hpp>  
  
namespace UtilityLibTest  
{  
    class XmlArchiveTest  
    {  
    public:  
        XmlArchiveTest() { m_value = 4; }  
  
        int Value() const { return m_value; }  
        void Value(const int v) { m_value = v; }  
    private:  
        friend class boost::serialization::access;  
  
        void load(boost::archive::xml_iarchive & ar, unsigned int version)  
        {  
            ar & BOOST_SERIALIZATION_NVP(m_value);  
        }  
        void save(boost::archive::xml_oarchive & ar, unsigned int version) const  
        {  
            ar & BOOST_SERIALIZATION_NVP(m_value);  
        }  
        BOOST_SERIALIZATION_SPLIT_MEMBER()  
              
    protected:  
        int m_value;  
    };  
  
    TEST_CLASS(UnitTest1)  
    {  
    public:  
  
        TEST_METHOD(XmlArchive_SaveLoad)  
        {  
            XmlArchiveTest store;  
  
            // save block  
  
            std::stringstream	xml(std::stringstream::out);  
            boost::archive::xml_oarchive archive(xml);  
  
            archive & BOOST_SERIALIZATION_NVP(store);  
  
            xml.flush();  
  
            auto xml1 =  xml.str();  
  
            store.Value(234);  
  
            // load block  
              
            std::stringstream	xml2;  
  
            xml2 << xml1;  
  
            boost::archive::xml_iarchive archive2(xml2);  
  
            archive2 & BOOST_SERIALIZATION_NVP(store);  
  
            Assert::AreEqual(4, store.Value(), L"4 != store.Value");  
        }  
  
    };  
}  
```  
  
Call stack  
  
```  
>	ucrtbased.dll!issue_debug_notification(const wchar_t * const message) Line 28	C++	Non-user code. Symbols loaded.  
 	ucrtbased.dll!__acrt_report_runtime_error(const wchar_t * message) Line 154	C++	Non-user code. Symbols loaded.  
 	ucrtbased.dll!abort() Line 51	C++	Non-user code. Symbols loaded.  
 	ucrtbased.dll!terminate() Line 59	C++	Non-user code. Symbols loaded.  
 	vcruntime140d.dll!FindHandler(EHExceptionRecord * pExcept, EHRegistrationNode * pRN, _CONTEXT * pContext, void * pDC, const _s_FuncInfo * pFuncInfo, unsigned char recursive, int CatchDepth, EHRegistrationNode * pMarkerRN) Line 627	C++	Non-user code. Symbols loaded.  
 	vcruntime140d.dll!__InternalCxxFrameHandler(EHExceptionRecord * pExcept, EHRegistrationNode * pRN, _CONTEXT * pContext, void * pDC, const _s_FuncInfo * pFuncInfo, int CatchDepth, EHRegistrationNode * pMarkerRN, unsigned char recursive) Line 347	C++	Non-user code. Symbols loaded.  
 	vcruntime140d.dll!__CxxFrameHandler(EHExceptionRecord * pExcept, EHRegistrationNode * pRN, void * pContext, void * pDC) Line 219	C++	Non-user code. Symbols loaded.  
 	ntdll.dll!ExecuteHandler2@20()	Unknown	Non-user code. Symbols loaded.  
 	ntdll.dll!ExecuteHandler@20()	Unknown	Non-user code. Symbols loaded.  
 	ntdll.dll!_KiUserExceptionDispatcher@8()	Unknown	Non-user code. Symbols loaded.  
 	KernelBase.dll!_RaiseException@16()	Unknown	Non-user code. Symbols loaded.  
 	vcruntime140d.dll!_CxxThrowException(void * pExceptionObject, const _s__ThrowInfo * pThrowInfo) Line 136	C++	Non-user code. Symbols loaded.  
 	UnitTestsUtilityLib.dll!boost::serialization::throw_exception<boost::archive::archive_exception>(const boost::archive::archive_exception & e) Line 37	C++	Symbols loaded.  
 	UnitTestsUtilityLib.dll!boost::archive::basic_xml_grammar<char>::my_parse(std::basic_istream<char,std::char_traits<char> > & is, const boost::spirit::classic::rule<boost::spirit::classic::scanner<std::_String_iterator<std::_String_val<std::_Simple_types<char> > >,boost::spirit::classic::scanner_policies<boost::spirit::classic::iteration_policy,boost::spirit::classic::match_policy,boost::spirit::classic::action_policy> >,boost::spirit::classic::nil_t,boost::spirit::classic::nil_t> & rule_, const char delimiter) Line 194	C++	Symbols loaded.  
 	UnitTestsUtilityLib.dll!boost::archive::basic_xml_grammar<char>::windup(std::basic_istream<char,std::char_traits<char> > & is) Line 465	C++	Symbols loaded.  
 	UnitTestsUtilityLib.dll!boost::archive::xml_iarchive_impl<boost::archive::xml_iarchive>::~xml_iarchive_impl<boost::archive::xml_iarchive>() Line 197	C++	Symbols loaded.  
 	UnitTestsUtilityLib.dll!boost::archive::xml_iarchive::~xml_iarchive() Line 129	C++	Symbols loaded.  
 	UnitTestsUtilityLib.dll!UtilityLibTest::UnitTest1::XmlArchive_SaveLoad() Line 72	C++	Symbols loaded.  
```  
  
(This is a copy of [#13354](https://svn.boost.org/trac10/ticket/13354) in the Boost Trac, I wasn't sure if it would be better reported here.)

---

## Comment 1

> Username: kravlost  
> Created at: 2018-04-04 14:25:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/82#issuecomment-378618598  

It's caused by badly-formed input.

---

## Comment 2

> Username: jenokizm  
> Created at: 2018-05-10 16:08:05 UTC  
> Updated at: 2018-05-10 16:23:31 UTC  
> Url: https://github.com/boostorg/serialization/issues/82#issuecomment-388101351  

Hi, I have the same thing. the input data is correct. I do not know what to do with this?  
while the data is deserialized into the object successfully, and after the program falls.  
ps im use boost + boost_serialization-vc141 packages version 1.66.0.0 or (same 1.67.0.0) from nuget.

---

## Comment 3

> Username: jenokizm  
> Created at: 2018-05-10 17:21:51 UTC  
> Url: https://github.com/boostorg/serialization/issues/82#issuecomment-388122720  

im downgrage to 1.65.1.0 and it work now!

---

## Comment 4

> Username: kravlost  
> Created at: 2018-05-11 09:09:09 UTC  
> Url: https://github.com/boostorg/serialization/issues/82#issuecomment-388307594  

@jenokizm I fixed it by checking to see that the XML ends with `</boost_serialization>`, and I add it if it doesn't. 1.66.0 and 1.67.0 works fine for me now - plus I make sure that the XML is created properly in the first place!

---

## Comment 5

> Username: jenokizm  
> Created at: 2018-05-11 09:32:15 UTC  
> Updated at: 2018-05-11 09:32:51 UTC  
> Url: https://github.com/boostorg/serialization/issues/82#issuecomment-388313067  

Then why does the serializer 1.66.0 and 1.67.0 itself not add to the end </boost_serialization>

---

## Comment 6

> Username: kravlost  
> Created at: 2018-05-12 17:26:22 UTC  
> Url: https://github.com/boostorg/serialization/issues/82#issuecomment-388570356  

You have to let the xml_oarchive object go out of scope to flush the last bit of data - see the answer to a similar question here: https://stackoverflow.com/questions/2205404/how-to-flush-file-buffers-when-using-boostserialization

---

## Comment 7

> Username: jenokizm  
> Created at: 2018-05-12 18:44:13 UTC  
> Url: https://github.com/boostorg/serialization/issues/82#issuecomment-388574886  

@steronydh I did close() before. now tried to flush() both in that council but it did not help either. The last line still does not add.

---

## Comment 8

> Username: robertramey  
> Created at: 2018-07-23 17:44:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/82#issuecomment-407142285  

I'm really confused about the current status of this.  
  
then serialization end tag should be added when the destructor is called.  If the archive doesn't go out of scope the restructure isn't called and this could be a problem.  
  
Is there a bug in here?  has anyone pointed it out and perhaps suggested a fix?  
  
It hasn't occurred to me how to catch the situation where the destructor isn't called.

---

## Comment 9

> Username: SeanFarrow  
> Created at: 2019-02-08 07:35:38 UTC  
> Url: https://github.com/boostorg/serialization/issues/82#issuecomment-461715993  

@robertramey,   
I can confirm that this bug is still present in version 1.69.0.  
I have a fix that is just about to be deployed in to a product that I'm involved in, so let me find a way to contribute this back to boost.

---

## Comment 10

> Username: fmauger  
> Created at: 2019-04-28 23:44:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/82#issuecomment-487425461  

I do not consider this issue as a bug even if Boost versions prior to 1.66 gave the impression things were working well. An archive must be used to store a consistent data set, typically the snapshot of some instances which are linked together in memory. That means a partial reading/parsing does not make sense and we should always save/restore the whole set of data. In the case of XML archives, the presence of start/end serialization tags gives a strong guarantee about the consistence of the serialized data. It is thus an error if the end tag is missing and this should been detected and signaled.  
  
The use of the archive **should** be enclosed in a specific scope:  
```  
  std::stringstream xml(std::stringstream::out);  
  {  
     boost::archive::xml_oarchive archive(xml); // implies the serialization header and start tag  
     archive & BOOST_SERIALIZATION_NVP(store);  
  } // guarantee the serialization end tag  
  auto xml1 = xml.str();   
```  
and  
```  
  std::stringstream xml2;  
  xml2 << xml1;  
  {  
     boost::archive::xml_iarchive archive(xml2); // decode the serialization header and start tag  
     archive & BOOST_SERIALIZATION_NVP(store);  
  } // request the serialization end tag   
```  
However, you may follow [https://github.com/BxCppDev/BxBoostSerializationTests/tree/master/test1](https://github.com/BxCppDev/BxBoostSerializationTests/tree/master/test1) where this behavior of the library and some consequences are tested.

---

## Comment 11

> Username: hajokirchhoff  
> Created at: 2020-09-10 08:37:01 UTC  
> Url: https://github.com/boostorg/serialization/issues/82#issuecomment-690083887  

This is definitely a bug: xml_iarchive cannot read malformed archives. That is no problem by itself. But throwing an exception inside a destructor will abort() the program. That is standard C++ behaviour: It is never allowed to throw inside a destructor.  
  
So the bug is this: When xml_iarchive tries to read a malformed archive, the application will abort() - close itself without any further message or chance by the program to do something about it.  
  
This manifests itself as follows: any application works fine until a user selects a defective file, where the last few bytes have been truncated or clobbered. This causes the application to terminate, loosing any work the user might have done.  
  
The correct way should be to have at least a try catch statement inside the destructor.  
```  
try {  
    if(0 == (this->get_flags() & no_header)){  
        gimpl->windup(is);  
    }  
}  
catch (...) {  
// This is too bad, but there is nothing we can do about a malformed closing tag.  
// Still it is much better to silently discard the error than terminating the application.  
}  
```

---

## Comment 12

> Username: calvincramer  
> Created at: 2021-11-13 00:52:34 UTC  
> Updated at: 2021-11-13 01:03:58 UTC  
> Url: https://github.com/boostorg/serialization/issues/82#issuecomment-967748304  

I agree with @hajokirchhoff. Exceptions in destructors is very awkward behavior.  
  
Here is another example of the intended usability (from a failing property_tree test case from MSCV, apparently):  
https://github.com/boostorg/property_tree/commit/0c3b65e243ff83bc1635170263907e707b8329a0

---

## Comment 13

> Username: correaa  
> Created at: 2021-11-13 04:06:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/82#issuecomment-967775722  

@calvincramer There are few discussions about this, for example: https://github.com/boostorg/serialization/issues/220  
I used to think the same, but now I think that there are legitimate uses of destructors that throw.  
It is basically not a problem if the object is not expected to be an element of a container (i.e. it is not a proper value type).  
(Of course the destructor has to be marked explicitly `nothrow(false)` for it to have a change of working.)  
Streams fall in this category for good reasons because streams can be structural parts of code, you don't put them in containers and such.
