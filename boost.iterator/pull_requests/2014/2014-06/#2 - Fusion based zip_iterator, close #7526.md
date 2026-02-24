# #2 Fusion based zip_iterator, close #7526 [Merged]

> Username: Flast  
> Created at: 2014-06-14 07:13:47 UTC  
> Updated at: 2017-03-15 12:07:32 UTC  
> Merged at: 2015-08-24 03:37:38 UTC  
> Closed at: 2015-08-24 03:37:38 UTC  
> Url: https://github.com/boostorg/iterator/pull/2  

I reimplement zip_iterator with Boost.Fusion to support other tuple implementations.  
And backward compatibility for Boost.Tuple should be appeared.  
  
Notes for release manager: This change depends on boostorg/fusion#7 (already merged into develop, but not merged yet into master/release).  
Ticket: https://svn.boost.org/trac/boost/ticket/7526

---

## Comment 1

> Username: MauricioCarneiro  
> Created_at: 2014-07-11 18:36:57 UTC  
> Url: https://github.com/boostorg/iterator/pull/2#issuecomment-48766967  

:+1: to this patch, any plans to merge it in? I'd love to be able to use zip_iterator with std::tuples.

---

## Comment 2

> Username: claashk  
> Created_at: 2015-03-26 07:45:03 UTC  
> Url: https://github.com/boostorg/iterator/pull/2#issuecomment-86377498  

:+1:  from me as well. We have been waiting for this feature for too long already.

---

## Comment 3

> Username: Flast  
> Created_at: 2015-03-26 07:53:54 UTC  
> Url: https://github.com/boostorg/iterator/pull/2#issuecomment-86381113  

> Notes for release manager: This change depends on boostorg/fusion#7 (already merged into develop, but not merged yet into master/release).  
  
Note again, next release (i.e. 1.58.0) includes boostorg/fusion#7.

---

## Comment 4

> Username: eldiener  
> Created_at: 2015-08-22 04:22:47 UTC  
> Url: https://github.com/boostorg/iterator/pull/2#issuecomment-133633705  

I have tested this out and it is working fine.   
  
The docs for zip_iterator need to be updated to explain what the tuple type may be. I am willing to do this myself. Am I correct that the tuple type for zip_iterator may be a std::tuple, a boost::tuple, or any fusion sequence ? Or are we limited in any way by the fusion sequence we specify ?

---

## Comment 5

> Username: eldiener  
> Created_at: 2015-08-23 03:30:48 UTC  
> Url: https://github.com/boostorg/iterator/pull/2#issuecomment-133778014  

My tests show that the implementation is failing if the tuple is a boost::fusion::deque. The failing is occurring when the code attempts to dereference a zip_iterator. The failing code, without the appropriate header files, is:  
  
```  
std::vector<double> vect1(3);  
vect1[0] = 42.;  
vect1[1] = 43.;  
vect1[2] = 44.;  
  
std::set<int> intset;  
intset.insert(52);  
intset.insert(53);  
intset.insert(54);  
//  
  
typedef  
boost::zip_iterator<  
    boost::fusion::deque<  
        std::set<int>::iterator  
      , std::vector<double>::iterator  
    >  
> zit_mixed;  
  
zit_mixed zip_it_mixed = zit_mixed(  
  boost::fusion::make_deque(  
      intset.begin()  
    , vect1.begin()  
  )  
);  
  
boost::fusion::deque<int, double> val_tuple(  
    *zip_it_mixed);  
```  
  
Once the dereference of *zip_it_mixed occurs we get the errors:  
  
```  
In file included from ..\..\../boost/fusion/container/deque/convert.hpp:12:0,  
                 from ..\..\../boost/fusion/container/deque.hpp:14,  
                 from ..\..\../boost/fusion/include/deque.hpp:11,  
                 from zip_iterator_test2_fusion_deque.cpp:3:  
..\..\../boost/fusion/container/deque/detail/convert_impl.hpp: In instantiation of 'static          boost::fusion::extension::convert_impl<boost::fusion::deque_tag>::apply<Sequence>::type boost::fusion::extension::convert_impl<boost::fusion::deque_tag>::apply<Sequence>::call(Sequence&) [with Sequence = boost::fusion::transform_view<const boost::fusion::deque<std::_Rb_tree_const_iterator<int>, __gnu_cxx::__normal_iterator<double*, std::vector<double> > >, boost::iterators::detail::dereference_iterator, boost::fusion::void_>; boost::fusion::extension::convert_impl<boost::fusion::deque_tag>::apply<Sequence>::type = boost::fusion::deque<const int&, double&, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_>]':  
..\..\../boost/fusion/sequence/convert.hpp:39:25:   required from 'typename boost::fusion::result_of::convert<Tag, Sequence>::type boost::fusion::convert(Sequence&) [with Tag = boost::fusion::deque_tag; Sequence = boost::fusion::transform_view<const boost::fusion::deque<std::_Rb_tree_const_iterator<int>, __gnu_cxx::__normal_iterator<double*, std::vector<double> > >, boost::iterators::detail::dereference_iterator, boost::fusion::void_>; typename boost::fusion::result_of::convert<Tag, Sequence>::type = boost::fusion::deque<const int&, double&, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_>]'  
..\..\../boost/iterator/zip_iterator.hpp:214:40:   required from 'static reference boost::iterators::detail::converter<reference>::call(Seq) [with Seq = boost::fusion::transform_view<const boost::fusion::deque<std::_Rb_tree_const_iterator<int>, __gnu_cxx::__normal_iterator<double*, std::vector<double> > >, boost::iterators::detail::dereference_iterator, boost::fusion::void_>; reference = boost::fusion::deque<const int&, double&, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_>]'  
..\..\../boost/iterator/zip_iterator.hpp:287:25:   required from 'typename boost::iterators::detail::zip_iterator_base<IteratorTuple>::type::reference boost::iterators::zip_iterator<IteratorTuple>::dereference() const [with IteratorTuple = boost::fusion::deque<std::_Rb_tree_const_iterator<int>, __gnu_cxx::__normal_iterator<double*, std::vector<double> > >; typename boost::iterators::detail::zip_iterator_base<IteratorTuple>::type::reference = boost::fusion::deque<const int&, double&, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_>]'  
..\..\../boost/iterator/iterator_facade.hpp:549:32:   required from 'static typename Facade::reference boost::iterators::iterator_core_access::dereference(const Facade&) [with Facade = boost::iterators::zip_iterator<boost::fusion::deque<std::_Rb_tree_const_iterator<int>, __gnu_cxx::__normal_iterator<double*, std::vector<double> > > >; typename Facade::reference = boost::fusion::deque<const int&, double&, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_>]'  
..\..\../boost/iterator/iterator_facade.hpp:655:53:   required from 'boost::iterators::detail::iterator_facade_base<Derived, Value, CategoryOrTraversal, Reference, Difference, false, false>::reference boost::iterators::detail::iterator_facade_base<Derived, Value, CategoryOrTraversal, Reference, Difference, false, false>::operator*() const [with Derived = boost::iterators::zip_iterator<boost::fusion::deque<std::_Rb_tree_const_iterator<int>, __gnu_cxx::__normal_iterator<double*, std::vector<double> > > >; Value = boost::fusion::deque<const int&, double&, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_>; CategoryOrTraversal = boost::iterators::bidirectional_traversal_tag; Reference = boost::fusion::deque<const int&, double&, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_>; Difference = int; boost::iterators::detail::iterator_facade_base<Derived, Value, CategoryOrTraversal, Reference, Difference, false, false>::reference = boost::fusion::deque<const int&, double&, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_, boost::fusion::void_>]'  
detail/zip_iterator_test_original.ipp:137:8:   required from here  
..\..\../boost/fusion/container/deque/detail/convert_impl.hpp:44:37: error: 'call' is not a member of 'boost::fusion::extension::convert_impl<boost::fusion::deque_tag>::apply<boost::fusion::transform_view<const boost::fusion::deque<std::_Rb_tree_const_iterator<int>, __gnu_cxx::__normal_iterator<double*, std::vector<double> > >, boost::iterators::detail::dereference_iterator, boost::fusion::void_> >::gen {aka boost::fusion::result_of::as_deque<boost::fusion::transform_view<const boost::fusion::deque<std::_Rb_tree_const_iterator<int>, __gnu_cxx::__normal_iterator<double*, std::vector<double> > >, boost::iterators::detail::dereference_iterator, boost::fusion::void_> >}'  
                     return gen::call(seq);  
```  
  
I need some explanation for this or some resolution of this before I can push the PR. I think the PR has great merit but it either needs to work in all relevant cases or the reason it will not work with the fusion container boost::fusion::deque needs to be explained in the documentation. It does work with both boost::fusion::vector and boost::fusion::list as the tuple for the tuple of iterators in your implementation.

---

## Comment 6

> Username: eldiener  
> Created_at: 2015-08-24 00:26:33 UTC  
> Url: https://github.com/boostorg/iterator/pull/2#issuecomment-133972212  

I discovered that the problem with boost::fusion::deque is in the fusion library and submitted a trac ticket against that problem at https://svn.boost.org/trac/boost/ticket/11572.

---

## Comment 7

> Username: Flast  
> Created_at: 2015-08-24 04:50:19 UTC  
> Url: https://github.com/boostorg/iterator/pull/2#issuecomment-134034761  

> The docs for zip_iterator need to be updated to explain what the tuple type may be. I am willing to do this myself.  
  
Thank you.  
  
> Am I correct that the tuple type for zip_iterator may be a std::tuple, a boost::tuple, or any fusion sequence ? Or are we limited in any way by the fusion sequence we specify ?   
  
It requires at least [Forward Sequence](http://www.boost.org/doc/libs/1_59_0/libs/fusion/doc/html/fusion/sequence/concepts/forward_sequence.html), i.e. any fusion sequence should be accepted.  
  
> I discovered that the problem with boost::fusion::deque is in the fusion library and submitted a trac ticket against that problem at https://svn.boost.org/trac/boost/ticket/11572.  
  
Thanks, I will investigate and fix it until next release.

---

## Comment 8

> Username: eldiener  
> Created_at: 2015-08-24 12:04:39 UTC  
> Url: https://github.com/boostorg/iterator/pull/2#issuecomment-134166822  

I updated the iterator tests in order to test out specific fusion sequences as the tuple iterator for the zip_iterator. I added a test for the fusion deque sequence but commented it out in the jamfile. When a fix appears for trac ticket 11572, I will uncomment that test.  
  
Are you helping to maintain Boost fusion ? If so fusion really needs tests for the generic sequence 'convert' functionality. I think the reason that the bug for trac ticket 11572 exists is that there are no fusion tests for the generic sequence 'convert' functionality, else the bug would have been found much earlier.

---

## Comment 9

> Username: Flast  
> Created_at: 2015-08-25 15:25:35 UTC  
> Url: https://github.com/boostorg/iterator/pull/2#issuecomment-134625499  

> If so fusion really needs tests for the generic sequence 'convert' functionality. I think the reason that the bug for trac ticket 11572 exists is that there are no fusion tests for the generic sequence 'convert' functionality, else the bug would have been found much earlier.  
  
Definitely.  
  
Now, I open a PR to fix ticket 11572 and added tests for converting each other.

---

## Comment 10

> Username: morinmorin  
> Created_at: 2017-03-11 04:23:14 UTC  
> Url: https://github.com/boostorg/iterator/pull/2#issuecomment-285840997  

Is this PR related to Trac ticket 12895?  
- VS 2012: zip_iterator dereference causes a compiler error  
  https://svn.boost.org/trac/boost/ticket/12895

---

## Comment 11

> Username: morinmorin  
> Created_at: 2017-03-15 12:07:32 UTC  
> Url: https://github.com/boostorg/iterator/pull/2#issuecomment-286722747  

Daniel submitted PR #22 for trac ticket 12895.  
(PR #23 is a testcase for it.)  
  
Could someone merge them?

---
