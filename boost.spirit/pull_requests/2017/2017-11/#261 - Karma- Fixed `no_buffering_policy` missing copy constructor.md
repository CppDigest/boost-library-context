# #261 Karma: Fixed `no_buffering_policy` missing copy constructor [Merged]

> Username: Kojoley  
> Created at: 2017-11-06 17:37:48 UTC  
> Updated at: 2017-11-06 18:07:47 UTC  
> Merged at: 2017-11-06 18:05:12 UTC  
> Closed at: 2017-11-06 18:05:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/261  

It seems that `no_buffering_policy` had a copy-paste error for 8 years.  
  
Found it at compilation of `example/karma/simple_columns_directive.cpp`  
<details>  
  <summary>GCC</summary>  
  
```  
gcc.compile.c++ ../../../bin.v2/libs/spirit/example/karma/gcc-gnu-6/debug/threadapi-pthread/simple_columns_directive.o  
In file included from ../../../boost/spirit/home/karma/detail/generate.hpp:16:0,  
                 from ../../../boost/spirit/home/karma/generate.hpp:15,  
                 from ../../../boost/spirit/home/karma/generate_attr.hpp:11,  
                 from ../../../boost/spirit/include/karma_generate_attr.hpp:16,  
                 from karma/simple_columns_directive.cpp:12:  
../../../boost/spirit/home/karma/detail/output_iterator.hpp: In instantiation of ‘boost::spirit::karma::detail::output_iterator_base<Buffering, Counting, Tracking>::output_iterator_base(const boost::spirit::karma::detail::output_iterator_base<Buffering, Counting, Tracking>&) [with Buffering = boost::spirit::karma::detail::no_buffering_policy; Counting = boost::spirit::karma::detail::no_counting_policy; Tracking = boost::spirit::karma::detail::no_position_policy]’:  
../../../boost/spirit/home/karma/detail/output_iterator.hpp:442:46:   required from ‘boost::spirit::karma::detail::output_iterator<OutputIterator, Properties, Derived>::output_iterator(const boost::spirit::karma::detail::output_iterator<OutputIterator, Properties, Derived>&) [with OutputIterator = std::back_insert_iterator<std::basic_string<char> >; Properties = mpl_::int_<0>; Derived = boost::spirit::unused_type]’  
../../../boost/spirit/home/karma/detail/output_iterator.hpp:455:40:   required from ‘boost::spirit::karma::detail::output_iterator<OutputIterator, Properties, Derived> boost::spirit::karma::detail::output_iterator<OutputIterator, Properties, Derived>::operator++(int) [with OutputIterator = std::back_insert_iterator<std::basic_string<char> >; Properties = mpl_::int_<0>; Derived = boost::spirit::unused_type]’  
karma/simple_columns_directive.hpp:64:22:   required from ‘bool custom_generator::columns_delimiter<Delimiter>::final_delimit_out(OutputIterator&) const [with OutputIterator = boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::basic_string<char> >, mpl_::int_<0>, boost::spirit::unused_type>; Delimiter = boost::spirit::karma::any_space<boost::spirit::char_encoding::standard>]’  
karma/simple_columns_directive.hpp:100:57:   required from ‘bool custom_generator::simple_columns_generator<Subject>::generate(OutputIterator&, Context&, const Delimiter&, const Attribute&) const [with OutputIterator = boost::spirit::karma::detail::output_iterator<std::back_insert_iterator<std::basic_string<char> >, mpl_::int_<0>, boost::spirit::unused_type>; Context = boost::spirit::context<boost::fusion::cons<const std::vector<int>&, boost::fusion::nil_>, boost::spirit::locals<> >; Delimiter = boost::spirit::karma::any_space<boost::spirit::char_encoding::standard>; Attribute = std::vector<int>; Subject = boost::spirit::karma::kleene<boost::spirit::karma::any_int_generator<int, boost::spirit::unused_type, boost::spirit::unused_type, 10u, false> >]’  
../../../boost/spirit/home/karma/generate.hpp:161:53:   required from ‘bool boost::spirit::karma::generate_delimited(boost::spirit::karma::detail::output_iterator<OutputIterator, Derived>&, const Expr&, const Delimiter&, boost::spirit::karma::delimit_flag::enum_type, const Attribute&) [with OutputIterator = std::back_insert_iterator<std::basic_string<char> >; Properties = mpl_::int_<0>; Expr = boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<custom_generator::tag::columns>, 0l>&, const boost::proto::exprns_::expr<boost::proto::tagns_::tag::dereference, boost::proto::argsns_::list1<const boost::spirit::terminal<boost::spirit::tag::int_>&>, 1l>&>, 2l>; Delimiter = boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::space, boost::spirit::char_encoding::standard> >, 0l>; Attribute = std::vector<int>]’  
../../../boost/spirit/home/karma/generate.hpp:185:41:   required from ‘bool boost::spirit::karma::generate_delimited(OutputIterator&, const Expr&, const Delimiter&, boost::spirit::karma::delimit_flag::enum_type, const Attribute&) [with OutputIterator = std::back_insert_iterator<std::basic_string<char> >; Expr = boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<custom_generator::tag::columns>, 0l>&, const boost::proto::exprns_::expr<boost::proto::tagns_::tag::dereference, boost::proto::argsns_::list1<const boost::spirit::terminal<boost::spirit::tag::int_>&>, 1l>&>, 2l>; Delimiter = boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::space, boost::spirit::char_encoding::standard> >, 0l>; Attribute = std::vector<int>]’  
../../../boost/spirit/home/karma/generate.hpp:212:41:   required from ‘bool boost::spirit::karma::generate_delimited(OutputIterator&, const Expr&, const Delimiter&, const Attribute&) [with OutputIterator = std::back_insert_iterator<std::basic_string<char> >; Expr = boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<custom_generator::tag::columns>, 0l>&, const boost::proto::exprns_::expr<boost::proto::tagns_::tag::dereference, boost::proto::argsns_::list1<const boost::spirit::terminal<boost::spirit::tag::int_>&>, 1l>&>, 2l>; Delimiter = boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::space, boost::spirit::char_encoding::standard> >, 0l>; Attribute = std::vector<int>]’  
karma/simple_columns_directive.cpp:34:53:   required from here  
../../../boost/spirit/home/karma/detail/output_iterator.hpp:337:77: error: call of overloaded ‘no_buffering_policy(const boost::spirit::karma::detail::output_iterator_base<boost::spirit::karma::detail::no_buffering_policy, boost::spirit::karma::detail::no_counting_policy, boost::spirit::karma::detail::no_position_policy>&)’ is ambiguous  
           : buffering_policy(rhs), counting_policy(rhs), tracking_policy(rhs)  
                                                                             ^  
../../../boost/spirit/home/karma/detail/output_iterator.hpp:307:9: note: candidate: boost::spirit::karma::detail::no_buffering_policy::no_buffering_policy(const boost::spirit::karma::detail::no_counting_policy&)  
         no_buffering_policy(no_counting_policy const&) {}  
         ^~~~~~~~~~~~~~~~~~~  
../../../boost/spirit/home/karma/detail/output_iterator.hpp:304:12: note: candidate: boost::spirit::karma::detail::no_buffering_policy::no_buffering_policy(const boost::spirit::karma::detail::no_buffering_policy&)  
     struct no_buffering_policy  
            ^~~~~~~~~~~~~~~~~~~  
```  
  
</details>  
<details>  
  <summary>Clang</summary>  
  
```  
clang-linux.compile.c++.without-pth ../../../bin.v2/libs/spirit/example/karma/clang-gnu-linux-5.0/debug/threadapi-pthread/simple_columns_directive.o  
In file included from karma/simple_columns_directive.cpp:12:  
In file included from ../../../boost/spirit/include/karma_generate_attr.hpp:16:  
In file included from ../../../boost/spirit/home/karma/generate_attr.hpp:11:  
In file included from ../../../boost/spirit/home/karma/generate.hpp:15:  
In file included from ../../../boost/spirit/home/karma/detail/generate.hpp:16:  
../../../boost/spirit/home/karma/detail/output_iterator.hpp:337:13: error: call to constructor of 'boost::spirit::karma::detail::no_buffering_policy' is ambiguous  
          : buffering_policy(rhs), counting_policy(rhs), tracking_policy(rhs)  
            ^                ~~~  
../../../boost/spirit/home/karma/detail/output_iterator.hpp:442:13: note: in instantiation of member function 'boost::spirit::karma::detail::output_iterator_base<boost::spirit::karma::detail::no_buffering_policy, boost::spirit::karma::detail::no_counting_policy, boost::spirit::karma::detail::no_position_policy>::output_iterator_base' requested here  
          : base_iterator(rhs), sink(rhs.sink)  
            ^  
../../../boost/spirit/home/karma/detail/output_iterator.hpp:455:33: note: in instantiation of member function 'boost::spirit::karma::detail::output_iterator<std::__1::back_insert_iterator<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>::output_iterator' requested here  
                output_iterator t(*this);  
                                ^  
karma/simple_columns_directive.hpp:54:22: note: in instantiation of member function 'boost::spirit::karma::detail::output_iterator<std::__1::back_insert_iterator<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>::operator++' requested here  
                *sink++ = '\n';  
                     ^  
../../../boost/spirit/home/karma/delimit_out.hpp:25:18: note: in instantiation of function template specialization 'custom_generator::columns_delimiter<boost::spirit::karma::any_space<boost::spirit::char_encoding::standard> >::generate<boost::spirit::karma::detail::output_iterator<std::__1::back_insert_iterator<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>, const boost::spirit::unused_type, boost::spirit::unused_type, boost::spirit::unused_type>' requested here  
        return d.generate(sink, unused, unused, unused);  
                 ^  
../../../boost/spirit/home/karma/numeric/int.hpp:242:20: note: in instantiation of function template specialization 'boost::spirit::karma::delimit_out<boost::spirit::karma::detail::output_iterator<std::__1::back_insert_iterator<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>, custom_generator::columns_delimiter<boost::spirit::karma::any_space<boost::spirit::char_encoding::standard> > >' requested here  
                   delimit_out(sink, d);      // always do post-delimiting  
                   ^  
../../../boost/spirit/home/karma/detail/fail_function.hpp:36:31: note: in instantiation of function template specialization 'boost::spirit::karma::any_int_generator<int, boost::spirit::unused_type, boost::spirit::unused_type, 10, false>::generate<boost::spirit::karma::detail::output_iterator<std::__1::back_insert_iterator<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>, boost::spirit::context<boost::fusion::cons<const std::__1::vector<int, std::__1::allocator<int> > &, boost::fusion::nil_>, boost::spirit::locals<mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na> >, custom_generator::columns_delimiter<boost::spirit::karma::any_space<boost::spirit::char_encoding::standard> >, int>' requested here  
            return !component.generate(sink, ctx, delim, attr);  
                              ^  
../../../boost/spirit/home/karma/detail/pass_container.hpp:318:34: note: in instantiation of function template specialization 'boost::spirit::karma::detail::fail_function<boost::spirit::karma::detail::output_iterator<std::__1::back_insert_iterator<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>, boost::spirit::context<boost::fusion::cons<const std::__1::vector<int, std::__1::allocator<int> > &, boost::fusion::nil_>, boost::spirit::locals<mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na> >, custom_generator::columns_delimiter<boost::spirit::karma::any_space<boost::spirit::char_encoding::standard> > >::operator()<boost::spirit::karma::any_int_generator<int, boost::spirit::unused_type, boost::spirit::unused_type, 10, false>, int>' requested here  
            if (!is_at_end() && !f(component, traits::deref(this->iter)))  
                                 ^  
../../../boost/spirit/home/karma/detail/pass_container.hpp:365:20: note: in instantiation of function template specialization 'boost::spirit::karma::detail::pass_container<boost::spirit::karma::detail::fail_function<boost::spirit::karma::detail::output_iterator<std::__1::back_insert_iterator<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>, boost::spirit::context<boost::fusion::cons<const std::__1::vector<int, std::__1::allocator<int> > &, boost::fusion::nil_>, boost::spirit::locals<mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na> >, custom_generator::columns_delimiter<boost::spirit::karma::any_space<boost::spirit::char_encoding::standard> > >, std::__1::vector<int, std::__1::allocator<int> >, boost::spirit::karma::detail::indirect_iterator<std::__1::__wrap_iter<const int *> >, mpl_::bool_<false> >::dispatch_container<boost::spirit::karma::any_int_generator<int, boost::spirit::unused_type, boost::spirit::unused_type, 10, false> >' requested here  
            return dispatch_container(component, predicate());  
                   ^  
../../../boost/spirit/home/karma/detail/pass_container.hpp:381:20: note: in instantiation of function template specialization 'boost::spirit::karma::detail::pass_container<boost::spirit::karma::detail::fail_function<boost::spirit::karma::detail::output_iterator<std::__1::back_insert_iterator<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>, boost::spirit::context<boost::fusion::cons<const std::__1::vector<int, std::__1::allocator<int> > &, boost::fusion::nil_>, boost::spirit::locals<mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na> >, custom_generator::columns_delimiter<boost::spirit::karma::any_space<boost::spirit::char_encoding::standard> > >, std::__1::vector<int, std::__1::allocator<int> >, boost::spirit::karma::detail::indirect_iterator<std::__1::__wrap_iter<const int *> >, mpl_::bool_<false> >::dispatch_attribute<boost::spirit::karma::any_int_generator<int, boost::spirit::unused_type, boost::spirit::unused_type, 10, false> >' requested here  
            return dispatch_attribute(component, predicate());  
                   ^  
../../../boost/spirit/home/karma/operator/kleene.hpp:53:23: note: in instantiation of function template specialization 'boost::spirit::karma::detail::pass_container<boost::spirit::karma::detail::fail_function<boost::spirit::karma::detail::output_iterator<std::__1::back_insert_iterator<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>, boost::spirit::context<boost::fusion::cons<const std::__1::vector<int, std::__1::allocator<int> > &, boost::fusion::nil_>, boost::spirit::locals<mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na> >, custom_generator::columns_delimiter<boost::spirit::karma::any_space<boost::spirit::char_encoding::standard> > >, std::__1::vector<int, std::__1::allocator<int> >, boost::spirit::karma::detail::indirect_iterator<std::__1::__wrap_iter<const int *> >, mpl_::bool_<false> >::operator()<boost::spirit::karma::any_int_generator<int, boost::spirit::unused_type, boost::spirit::unused_type, 10, false> >' requested here  
            bool r = !f(subject);  
                      ^  
../../../boost/spirit/home/karma/operator/kleene.hpp:126:22: note: in instantiation of function template specialization 'boost::spirit::karma::base_kleene<boost::spirit::karma::any_int_generator<int, boost::spirit::unused_type, boost::spirit::unused_type, 10, false>, mpl_::bool_<false>, boost::spirit::karma::kleene<boost::spirit::karma::any_int_generator<int, boost::spirit::unused_type, boost::spirit::unused_type, 10, false> > >::generate_subject<boost::spirit::karma::detail::pass_container<boost::spirit::karma::detail::fail_function<boost::spirit::karma::detail::output_iterator<std::__1::back_insert_iterator<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>, boost::spirit::context<boost::fusion::cons<const std::__1::vector<int, std::__1::allocator<int> > &, boost::fusion::nil_>, boost::spirit::locals<mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na> >, custom_generator::columns_delimiter<boost::spirit::karma::any_space<boost::spirit::char_encoding::standard> > >, std::__1::vector<int, std::__1::allocator<int> >, boost::spirit::karma::detail::indirect_iterator<std::__1::__wrap_iter<const int *> >, mpl_::bool_<false> >, std::__1::vector<int, std::__1::allocator<int> > >' requested here  
                if (!generate_subject(pass, attr, Strict()))  
                     ^  
karma/simple_columns_directive.hpp:100:28: note: in instantiation of function template specialization 'boost::spirit::karma::base_kleene<boost::spirit::karma::any_int_generator<int, boost::spirit::unused_type, boost::spirit::unused_type, 10, false>, mpl_::bool_<false>, boost::spirit::karma::kleene<boost::spirit::karma::any_int_generator<int, boost::spirit::unused_type, boost::spirit::unused_type, 10, false> > >::generate<boost::spirit::karma::detail::output_iterator<std::__1::back_insert_iterator<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>, boost::spirit::context<boost::fusion::cons<const std::__1::vector<int, std::__1::allocator<int> > &, boost::fusion::nil_>, boost::spirit::locals<mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na> >, custom_generator::columns_delimiter<boost::spirit::karma::any_space<boost::spirit::char_encoding::standard> >, std::__1::vector<int, std::__1::allocator<int> > >' requested here  
            return subject.generate(sink, ctx, d, attr) && d.final_delimit_out(sink);  
                           ^  
../../../boost/spirit/home/karma/generate.hpp:161:13: note: in instantiation of function template specialization 'custom_generator::simple_columns_generator<boost::spirit::karma::kleene<boost::spirit::karma::any_int_generator<int, boost::spirit::unused_type, boost::spirit::unused_type, 10, false> > >::generate<boost::spirit::karma::detail::output_iterator<std::__1::back_insert_iterator<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, mpl_::int_<0>, boost::spirit::unused_type>, boost::spirit::context<boost::fusion::cons<const std::__1::vector<int, std::__1::allocator<int> > &, boost::fusion::nil_>, boost::spirit::locals<mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na> >, boost::spirit::karma::any_space<boost::spirit::char_encoding::standard>, std::__1::vector<int, std::__1::allocator<int> > >' requested here  
            generate(sink, context, delimiter_, attr);  
            ^  
../../../boost/spirit/home/karma/generate.hpp:185:23: note: in instantiation of function template specialization 'boost::spirit::karma::generate_delimited<std::__1::back_insert_iterator<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, mpl_::int_<0>, boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<custom_generator::tag::columns>, 0> &, const boost::proto::exprns_::expr<boost::proto::tagns_::tag::dereference, boost::proto::argsns_::list1<const boost::spirit::terminal<boost::spirit::tag::int_> &>, 1> &>, 2>, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::space, boost::spirit::char_encoding::standard> >, 0>, std::__1::vector<int, std::__1::allocator<int> > >' requested here  
        return karma::generate_delimited(sink, expr, delimiter, pre_delimit, attr);  
                      ^  
../../../boost/spirit/home/karma/generate.hpp:212:23: note: in instantiation of function template specialization 'boost::spirit::karma::generate_delimited<std::__1::back_insert_iterator<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<custom_generator::tag::columns>, 0> &, const boost::proto::exprns_::expr<boost::proto::tagns_::tag::dereference, boost::proto::argsns_::list1<const boost::spirit::terminal<boost::spirit::tag::int_> &>, 1> &>, 2>, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::space, boost::spirit::char_encoding::standard> >, 0>, std::__1::vector<int, std::__1::allocator<int> > >' requested here  
        return karma::generate_delimited(sink, expr, delimiter  
                      ^  
karma/simple_columns_directive.cpp:33:26: note: in instantiation of function template specialization 'boost::spirit::karma::generate_delimited<std::__1::back_insert_iterator<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >, boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript, boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<custom_generator::tag::columns>, 0> &, const boost::proto::exprns_::expr<boost::proto::tagns_::tag::dereference, boost::proto::argsns_::list1<const boost::spirit::terminal<boost::spirit::tag::int_> &>, 1> &>, 2>, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::space, boost::spirit::char_encoding::standard> >, 0>, std::__1::vector<int, std::__1::allocator<int> > >' requested here  
    bool result = karma::generate_delimited(  
                         ^  
../../../boost/spirit/home/karma/detail/output_iterator.hpp:304:12: note: candidate is the implicit copy constructor  
    struct no_buffering_policy  
           ^  
../../../boost/spirit/home/karma/detail/output_iterator.hpp:307:9: note: candidate constructor  
        no_buffering_policy(no_counting_policy const&) {}  
        ^  
1 error generated.  
```  
  
</details>

---

## Comment 1

> Username: hkaiser  
> Created_at: 2017-11-06 18:05:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/261#issuecomment-342233979  

Nice catch! Thanks!

---
