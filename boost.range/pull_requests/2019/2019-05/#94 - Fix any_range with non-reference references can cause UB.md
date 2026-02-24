# #94 Fix any_range with non-reference references can cause UB [Merged]

> Username: mjendruk  
> Created at: 2019-05-07 10:31:53 UTC  
> Updated at: 2020-02-13 09:36:51 UTC  
> Merged at: 2020-02-13 09:36:51 UTC  
> Closed at: 2020-02-13 09:36:51 UTC  
> Url: https://github.com/boostorg/range/pull/94  

This pull request fixes an issue with non-reference references.  
  
The issue was introduced in 79d2a66831af16f29befaa4d32461928b9431a15. In order to make `any_range` compatible with non-copyable types, a check was introduced adding a reference to the `Reference` template parameter unless it was a non-reference const type. This fixed the problem of non-copyable types but made it impossible to return value types from an `any_range` such as when using `adaptors::transformed`.   
  
The real issue however was the method `clone_reference_as_value` in `any_incrementable_iterator_interface`:  
  
```c++  
typedef typename remove_const<  
    typename remove_reference<Reference>::type  
>::type reference_as_value_type;  
  
virtual any_incrementable_iterator_interface<reference_as_value_type, Buffer>*  
    clone_reference_as_value(buffer_type& buffer) const = 0;  
```  
  
It clones the iterator and replaces its `Reference` parameter with a non-const non-reference version. This is why even though `any_range` was instantiated with a reference `Reference` parameter, the non-reference version was compiled anyway.   
  
In order to fix this, I added a check here stripping `Reference` off its referenceness unless `Reference` is non-copyable.  
  
Fixes #73 / https://svn.boost.org/trac10/ticket/10493

---

## Comment 1

> Username: andronov-alexey  
> Created_at: 2020-02-12 10:07:39 UTC  
> Url: https://github.com/boostorg/range/pull/94#issuecomment-585129440  

Seems like a useful PR, can we merge it?

---

## Comment 2

> Username: mjendruk  
> Created_at: 2020-02-13 08:46:01 UTC  
> Url: https://github.com/boostorg/range/pull/94#issuecomment-585615816  

I posted a message in the boost mailing list a while ago, this is the response I got.   
  
  
On Thu, 13 Jun 2019, 16:28 Max Jendruk via Boost, <boost@lists.boost.org> wrote:  
> Hello everyone,  
  
Hi  
  
> I submitted a bugfix on Github about a month ago that fixes an issue where passing a (non-reference) value type as Reference parameter to any_range could lead to undefined behaviour. The issue has been known for a while (5 years). I would like to know when I can expect this to be merged.   
  
I maintain Boost.Range and I frequently take longer than any of us would like due to work and family commitments.  
  
There are ways to abuse Boost.Range that lead to undefined behaviour. Some are like the reference lifetime of temporaries. All of the general changes that I have analysed have unintended consequences worse than the original issue.  
  
Boost.Range v3 fixes the issue by design. V3 is very compelling for new projects.  
  
> Also, is there a general review cycle that I'm not aware of?  
  
I don't know what you are aware of, but for fixes I generally look at the branch, run against the test matrix and sometimes test against large scale codebases with a change.  
  
> There are still a couple of other pull requests open, some of which were opened over a year ago. Just by looking at the activity on Github, it's difficult to determined how actively Boost.Range is still being maintained. What is the development/maintenance status of the library?  
  
It is actively maintained. Some issues are handled quickly, others less so.  
  
> I'm wondering whether it would be worth the time implementing a few more adaptors similar to those in the C++20 standard like take_view, join_view, and all_view, which I'd be happy to do. Are additions to the library still being accepted at this point?  
  
You are welcome to contribute. It is up to you to decide if it is worth it.  
  
I will continue to do what I can, when I can.  
  
> Best regards,  
> Max  
  
Regards,  
Neil

---
