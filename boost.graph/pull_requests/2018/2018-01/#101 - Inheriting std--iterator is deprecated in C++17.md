# #101 Inheriting std::iterator is deprecated in C++17 [Merged]

> Username: DanielaE  
> Created at: 2018-01-02 15:17:08 UTC  
> Updated at: 2018-10-15 07:34:54 UTC  
> Merged at: 2018-10-15 07:34:53 UTC  
> Closed at: 2018-10-15 07:34:53 UTC  
> Url: https://github.com/boostorg/graph/pull/101  

Boost's iterator.hpp is deprecated, too. Therefore get rid of all of that and replace inheritance by lifting std::iterator's members into the derived class.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: davydden  
> Created_at: 2018-03-31 06:30:06 UTC  
> Url: https://github.com/boostorg/graph/pull/101#issuecomment-377670398  

this should also fix https://svn.boost.org/trac10/ticket/13505   
  
@ericniebler @Belcourt ping.

---

## Comment 2

> Username: davydden  
> Created_at: 2018-04-04 12:38:28 UTC  
> Url: https://github.com/boostorg/graph/pull/101#issuecomment-378585113  

@DanielaE please rebase and see https://github.com/boostorg/graph/pull/104#issuecomment-378578619

---

## Comment 3

> Username: DanielaE  
> Created_at: 2018-04-04 14:47:48 UTC  
> Url: https://github.com/boostorg/graph/pull/101#issuecomment-378626801  

Done 😄

---

## Review 4 [Commented]

> Username: morinmorin  
> Created_at: 2018-04-04 17:36:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/101#pullrequestreview-109428219  

📁 include/boost/graph/detail/array_binary_tree.hpp

```diff
  46 |+       typedef std::bidirectional_iterator_tag iterator_category;
  47 |+       typedef ArrayBinaryTreeNode value_type;
  48 |+       typedef size_type difference_type;
```

> Username: morinmorin  
> Created_at: 2018-04-04 17:36:46 UTC  
> Updated_at: 2018-04-14 16:16:24 UTC  
> Url: https://github.com/boostorg/graph/pull/101#discussion_r179224203  

```  
typedef ArrayBinaryTreeNode::difference_type difference_type;  
typedef ArrayBinaryTreeNode* pointer;  
```  
would be more readable?


---

## Comment 5

> Username: anadon  
> Created_at: 2018-08-31 17:38:10 UTC  
> Url: https://github.com/boostorg/graph/pull/101#issuecomment-417738491  

I'm helping out with the PR backlog. Looks like you have a code change.  I need to look into how this will affect backwards compatibility with C++ standards. This is to let you know and help me prioritize PR's.

---

## Comment 6

> Username: DanielaE  
> Created_at: 2018-08-31 17:51:43 UTC  
> Url: https://github.com/boostorg/graph/pull/101#issuecomment-417742107  

It doesn't change it in any respect.

---

## Comment 7

> Username: anadon  
> Created_at: 2018-08-31 18:49:14 UTC  
> Url: https://github.com/boostorg/graph/pull/101#issuecomment-417757799  

It uses tags/traits, which are not something necessarily safe for C++03.  I'm going to do some additional testing to satisfy myself that this is either a safe change, or going to be pushed off.

---

## Comment 8

> Username: DanielaE  
> Created_at: 2018-09-01 05:03:40 UTC  
> Url: https://github.com/boostorg/graph/pull/101#issuecomment-417833466  

Well, no - it does not.

---

## Comment 9

> Username: anadon  
> Created_at: 2018-09-01 05:53:42 UTC  
> Url: https://github.com/boostorg/graph/pull/101#issuecomment-417835576  

OK, I'm mixing up 2a/2z stuff with 03.  
Suggested by http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2003/n1424.htm  
This will be put into the second batch of merges.

---

## Comment 10

> Username: DanielaE  
> Created_at: 2018-09-01 06:03:59 UTC  
> Url: https://github.com/boostorg/graph/pull/101#issuecomment-417835960  

Graph is one of the 5 last users of boost::iterator, the empty shell around std::iterator which itself is deprecated in C++17 [http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0174r2.html#2.1](url) (with all it's consequences) and about to be removed from the language.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2018-10-12 17:32:53 UTC  
> Url: https://github.com/boostorg/graph/pull/101#issuecomment-429402076  

Ooops, that reference was to the wrong PR :(

---

## Comment 12

> Username: anadon  
> Created_at: 2018-10-12 18:15:40 UTC  
> Url: https://github.com/boostorg/graph/pull/101#issuecomment-429414033  

@jzmaddock Can you trigger a build on this?

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2018-10-12 18:23:41 UTC  
> Url: https://github.com/boostorg/graph/pull/101#issuecomment-429416241  

Closing and re-opening to trigger a CI build.

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2018-10-14 07:50:44 UTC  
> Url: https://github.com/boostorg/graph/pull/101#issuecomment-429604429  

Looks good to me, but I'll wait for Appveyor to catch up before merging...

---

## Comment 15

> Username: anadon  
> Created_at: 2018-10-15 03:45:32 UTC  
> Url: https://github.com/boostorg/graph/pull/101#issuecomment-429699799  

@jzmaddock All clear.

---
