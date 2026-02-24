# #6 Publish some of the implementation details [Merged]

> Username: Lastique  
> Created at: 2014-07-19 18:31:06 UTC  
> Updated at: 2014-08-23 20:20:38 UTC  
> Merged at: 2014-08-23 20:08:11 UTC  
> Closed at: 2014-08-23 20:08:11 UTC  
> Url: https://github.com/boostorg/iterator/pull/6  

Some of the implementation details of Boost.Iterator are used in other libraries - Boost.Tokenizer and Boost.Range. This pull request moves some of these components to the public namespace. These components are:  
- minimum_category trait, which is used by token_iterator  
- pure_traversal_tag trait, which is used by demote_iterator_traversal_tag in Boost.Range  
  
Also added tests for minimum_category and added pure_iterator_traversal trait which is based on pure_traversal_tag but accepts an iterator instead of traversal as input.  
  
This pull request will help to resolve Boost.Range breakage after the previous pull request https://github.com/boostorg/iterator/pull/5.

---
