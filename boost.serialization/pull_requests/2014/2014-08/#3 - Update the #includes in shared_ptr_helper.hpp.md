# #3 Update the #includes in shared_ptr_helper.hpp [Closed]

> Username: mirams  
> Created at: 2014-08-11 13:48:08 UTC  
> Updated at: 2014-12-01 16:01:11 UTC  
> Closed at: 2014-12-01 16:01:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/3  

We were getting errors like this during compilation:  
  
```  
In file included from /home/lofty/boost_1_56/include/boost/serialization/shared_ptr.hpp:28,  
                 from linalg/src/LinearSystem.hpp:50,  
                 from ./linalg/test/TestChebyshevIteration.hpp:40,  
                 from linalg/build/debug_hostconfig,boost=1-56/TestChebyshevIterationRunner.cpp:21:  
/home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp: In static member function static const boost::serialization::extended_type_info* boost::serialization::shared_ptr_helper<SPT>::non_polymorphic::get_object_type(U&)':  
/home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:107: error: 'singleton' is not a member of 'boost::serialization'  
/home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:110: error: expected '(' before '>' token  
/home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:110: error: '::get_const_instance' has not been declared  
/home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp: In static member function 'static const boost::serialization::extended_type_info* boost::serialization::shared_ptr_helper<SPT>::polymorphic::get_object_type(U&)':  
/home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:117: error: 'singleton' is not a member of 'boost::serialization'  
/home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:120: error: expected '(' before '>' token  
/home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:120: error: '::get_const_instance' has not been declared  
/home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp: In member function 'void boost::serialization::shared_ptr_helper<SPT>::reset(SPT<T>&, T*)':  
/home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:152: error: invalid use of incomplete type 'const struct boost::serialization::extended_type_info'  
/home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:42: error: forward declaration of 'const struct boost::serialization::extended_type_info'  
/home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:167: error: invalid use of incomplete type 'const struct boost::serialization::extended_type_info'  
/home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:42: error: forward declaration of 'const struct boost::serialization::extended_type_info'  
/home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:168: error: invalid use of incomplete type 'const struct boost::serialization::extended_type_info'  
/home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:42: error: forward declaration of 'const struct boost::serialization::extended_type_info'  
scons: *** [linalg/build/debug_hostconfig,boost=1-56/TestChebyshevIterationRunner.o] Error 1  
```

---

## Comment 1

> Username: mirams  
> Created_at: 2014-08-12 13:15:12 UTC  
> Url: https://github.com/boostorg/serialization/pull/3#issuecomment-51911663  

I opened a ticket about this on the boost trac: https://svn.boost.org/trac/boost/ticket/10348

---

## Comment 2

> Username: robertramey  
> Created_at: 2014-08-12 16:12:32 UTC  
> Url: https://github.com/boostorg/serialization/pull/3#issuecomment-51937218  

I would be curious about your testing against previous versions of boost serialization going back to 1.40.  
  
tell me more about it.  
  
What test programs do you run.  
Which archives do you test.  
Do you test your own app or the serialization test suite.  
Do you keep archives from previous versions and verify that you can load them all - or what?  
  
I would like to test backward compatibility with all archive types going back to boost 1.40…  
I’m not sure what you’re actually testing here - but I’m interested to know more.  
  
Robert Ramey  
Robert Ramey Software Development  
(805)569-3793  
ramey@rrsd.com  
www.rrsd.com  
  
On Aug 11, 2014, at 6:48 AM, Gary Mirams notifications@github.com wrote:  
  
> We were getting errors like this during compilation:  
>   
> In file included from /home/lofty/boost_1_56/include/boost/serialization/shared_ptr.hpp:28,  
>                  from linalg/src/LinearSystem.hpp:50,  
>                  from ./linalg/test/TestChebyshevIteration.hpp:40,  
>                  from linalg/build/debug_hostconfig,boost=1-56/TestChebyshevIterationRunner.cpp:21:  
> /home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp: In static member function static const boost::serialization::extended_type_info\* boost::serialization::shared_ptr_helper<SPT>::non_polymorphic::get_object_type(U&)':  
> /home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:107: error: 'singleton' is not a member of 'boost::serialization'  
> /home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:110: error: expected '(' before '>' token  
> /home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:110: error: '::get_const_instance' has not been declared  
> /home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp: In static member function 'static const boost::serialization::extended_type_info\* boost::serialization::shared_ptr_helper<SPT>::polymorphic::get_object_type(U&)':  
> /home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:117: error: 'singleton' is not a member of 'boost::serialization'  
> /home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:120: error: expected '(' before '>' token  
> /home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:120: error: '::get_const_instance' has not been declared  
> /home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp: In member function 'void boost::serialization::shared_ptr_helper<SPT>::reset(SPT<T>&, T_)':  
> /home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:152: error: invalid use of incomplete type 'const struct boost::serialization::extended_type_info'  
> /home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:42: error: forward declaration of 'const struct boost::serialization::extended_type_info'  
> /home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:167: error: invalid use of incomplete type 'const struct boost::serialization::extended_type_info'  
> /home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:42: error: forward declaration of 'const struct boost::serialization::extended_type_info'  
> /home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:168: error: invalid use of incomplete type 'const struct boost::serialization::extended_type_info'  
> /home/lofty/boost_1_56/include/boost/serialization/shared_ptr_helper.hpp:42: error: forward declaration of 'const struct boost::serialization::extended_type_info'  
> scons: *_\* [linalg/build/debug_hostconfig,boost=1-56/TestChebyshevIterationRunner.o] Error 1  
> You can merge this Pull Request by running  
>   
>   git pull https://github.com/mirams/serialization master  
> Or view, comment on, or merge it at:  
>   
>   https://github.com/boostorg/serialization/pull/3  
>   
> Commit Summary  
>   
> Update the #includes in shared_ptr_helper.hpp to avoid 'singleton' is not a member of 'boost::serialization' errors on compilation  
> File Changes  
>   
> M include/boost/serialization/shared_ptr_helper.hpp (2)  
> Patch Links:  
>   
> https://github.com/boostorg/serialization/pull/3.patch  
> https://github.com/boostorg/serialization/pull/3.diff  
> —  
> Reply to this email directly or view it on GitHub.

---

## Comment 3

> Username: mirams  
> Created_at: 2014-08-12 17:28:49 UTC  
> Updated_at: 2014-08-12 17:31:38 UTC  
> Url: https://github.com/boostorg/serialization/pull/3#issuecomment-51947593  

We have tests for our own application Chaste (www.cs.ox.ac.uk/chaste) which have used boost serialization for about 7 years, so we try to maintain backwards compatibility.  
  
We've created various wrappers to deal with changes in the serialization library over the years, and keep a consistent interface in all the classes in our C++ library, most of the wrappers are here:  
https://chaste.cs.ox.ac.uk/trac/browser/trunk/global/src/checkpointing  
Our internal guide to using boost serialization via our wrappers:  
https://chaste.cs.ox.ac.uk/trac/wiki/ChasteGuides/BoostSerialization  
  
We currently load up a boost 1.40 archive (of a fairly complicated collection of templated classes, abstract classes, shared pointers, and singletons etc. that run simulations of intestinal crypts), and test that loads and runs OK on boost versions up to and including 1.55. For each other version of boost we check that archives can be saved and loaded on the same version.  
(Except ones where checkpointing had known bugs: https://chaste.cs.ox.ac.uk/trac/wiki/InstallGuides/DependencyVersions).   
  
Although I think all the structures we have would support back to 1.33.1, but we don't have any machines running that any more, we just recently dropped testing for 1.37.   
  
@jonc125 is our boost serialization guru who can probably tell you more!

---

## Comment 4

> Username: robertramey  
> Created_at: 2014-08-12 18:06:57 UTC  
> Url: https://github.com/boostorg/serialization/pull/3#issuecomment-51953009  

On Aug 12, 2014, at 10:28 AM, Gary Mirams notifications@github.com wrote:  
  
> We have tests for our own application Chaste (www.cs.ox.ac.uk/chaste) which have used boost serialization for about 7 years, so we try to maintain backwards compatibility.  
>   
> We've created various wrappers to deal with changes in the serialization library over the years, and keep a consistent interface in all the classes in our C++ library, most of the wrappers are here:  
> https://chaste.cs.ox.ac.uk/trac/browser/trunk/global/src/checkpointing  
> Our internal guide to using boost serialization via our wrappers:  
> https://chaste.cs.ox.ac.uk/trac/wiki/ChasteGuides/BoostSerialization  
  
GREAT Document - care to make a version to add to the boost serialization documentation?  
  
> We currently load up a boost 1.40 archive (of a fairly complicated collection of templated classes, abstract classes, shared pointers, and singletons etc. that run simulations of intestinal crypts), and test that loads and runs OK on boost versions up to and including 1.55. (Except ones where checkpointing had known bugs, we test ones listed here: https://chaste.cs.ox.ac.uk/trac/wiki/InstallGuides/DependencyVersions)  
>   
> Although I think all the structures we have would support back to 1.33.1, but we don't have any machines running that any more, we just recently dropped testing for 1.37.  
>   
> @jonc125 is our boost serialization guru who can probably tell you more!  
>   
> —  
> Reply to this email directly or view it on GitHub.

---

## Comment 5

> Username: jonc125  
> Created_at: 2014-08-20 13:40:43 UTC  
> Url: https://github.com/boostorg/serialization/pull/3#issuecomment-52779033  

I'm unlikely to have time in the near future, but if you could give a bit more detail on how you'd like it edited for the Boost documentation, I'll see what I can do!

---

## Comment 6

> Username: matthieu637  
> Created_at: 2014-09-09 09:50:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/3#issuecomment-54947361  

I'd got the same problem when I updated from boost-1.55.0-6 to 1.56.0-1.  
The two includes fixed the problem.  
Thank you.

---

## Comment 7

> Username: mirams  
> Created_at: 2014-12-01 16:01:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/3#issuecomment-65085928  

This was pulled in.

---
