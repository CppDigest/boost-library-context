# #65 - URL comparison [Closed]

> Username: vinniefalco  
> Created at: 2021-10-13 15:39:21 UTC  
> Updated at: 2022-03-11 19:37:56 UTC  
> Closed at: 2022-03-11 19:37:56 UTC  
> Url: https://github.com/boostorg/url/issues/65  

`url_view` needs to be equality comparable using the RFC algorithm. We might also consider a lexicographic comparison for containers.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-02-22 06:20:14 UTC  
> Url: https://github.com/boostorg/url/issues/65#issuecomment-1047463386  

First we need to research the subject and figure out what a conforming implementation looks like (see rfc3986). We should also check what other URL libraries are doing in terms of comparison. The notes can be collected here in this issue.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-03-01 01:38:35 UTC  
> Url: https://github.com/boostorg/url/issues/65#issuecomment-1054889309  

So it seems like this issue and #8 are almost the same issues. Each normalization strategy represents a comparison strategy. The main difference for us, because we care about memory allocation, is that we probably want normalization algorithms and comparison algorithms to work _as if_ the underlying strings were normalized, instead of reusing the algorithms.  
  
## Boost.URL design (TL;DR)  
  
The final choice here is between String comparison and Syntax-Based normalization. For `url::normalize`, Syntax-Based normalization is the only possibility that makes sense.  
  
For `url_view::operator==`, Syntax-Based normalization also seems like the best alternative to enable containers of URLs and lead to a syntax that's self-explanatory, because we can always perform a string comparison with:  
  
```cpp  
assert( u1.string() == u2.string() );  
```  
  
and Syntax-Based comparison with:  
  
```cpp  
assert( u1 == u2 );  
```  
  
The only difference between normalization and comparison is one of them acts _as if_ they are normalized. This has a cost when the URLs are different but it's always constant on code-point and does not require any memory allocations from the heap.  
  
## Other libs  
  
Other libraries, such as folly and apache, don't include normalization. Javascript's URL library doesn't include normalization but there are some famous libraries such as [sindresorhus/normalize-url](https://github.com/sindresorhus/normalize-url) people seem to use.   
  
[sindresorhus/normalize-url](https://github.com/sindresorhus/normalize-url), however, has options that are not very related to what the RFC 3986 describes as normalization. It implicitly assumes URLs are always http, doesn't include RFC normalization rules, and includes lots of rules that would go beyond even http normalization because normalized URLs would point to different HTTP resources. In practice, they are at most some useful conversion functions for URLs, and Boost.URL provides alternatives for each of these functions.  
  
## Methods  
  
These are the normalization/comparison strategies by their probability of false negatives and cost.  
- String comparison  
- Syntax-Based normalization  
- Scheme-Based Normalization  
- Protocol-Based Normalization  
  
Only the first two make sense for the level of abstraction of Boost.URL. Scheme-Based Normalization fits better in Boost.HTTP.  Protocol-Based Normalization makes sense in some web spiders.   
  
## Trade-offs  
  
False negatives (two URLs to the same resource being considered different) can never be completely eliminated because they depend a lot on the context. Example: the same website served from two servers: Comparison will return false for the same resource.  
  
We can only eliminate false positives with rules augmented by the scheme, protocol, and contextual rules. Minimizing false negatives also has an extra cost for each normalization.  
  
So the goal is to _minimize_ false negatives and _completely eliminate_ false positives.   
  
## Note on Relative references  
  
In applications, relative references should not be compared directly by applications to identify resources. Fragments should often be ignored when compared to select a network action. A positive example is HTML anchors, which represent the same resource. A negative example is a Git commit tag, which represents different resources.  
  
## More details on each method:  
  
Comparison Methods and Normalizations by the probability of false negatives and cost:  
  
- String comparison:   
	- No direct relationship with resource  
	- False negatives are mostly caused by URI aliases  
		- Example: two strings pointing to the same resource: http://myothersite/user/65 != http://myothersite/user/bob  
	- Implementations will, in their own best interest, be consistent in providing URI references though  
- Syntax-Based normalization:  
	- Used by Web user agents, such as browsers, to determine if a cached response is available  
	- Uses definitions provided by RFC3986 itself  
	- Example: "example://a/b/c/%7Bfoo%7D" == "eXAMPLE://a/./b/../b/%63/%7bfoo%7d"  
	- Case normalization:   
		- the hexadecimal digits are case insensitive: normalize to uppercase  
		- scheme and host are case-insensitive: normalize to lowercase  
		- The other generic syntax components case-sensitive  
	- Percent-encoding normalization:  
		- Replace percent-encode octets that do not require percent-encoding (unreserved characters 2.3)  
	- Path Segment Normalization:  
		- Keep "." and ".." only in relative references (4.1)  
		- Remove them in non-relative paths (5.2)  
		- Use the remove_dot_segments algorithm (5.2.4)  
- Scheme-Based Normalization:  
	- Example: HTTP default port 80, empty path == "/", so these are the same:  
			- http://example.com, http://example.com/, http://example.com:/, and http://example.com:80/ are equivalent  
	- Other common normalizations:  
		- Normalize HTTP empty path to "/"  
		- Remove HTTP port 80 or empty port  
		- Normalize empty host to localhost or error  
		- Normalize empty host to localhost, a default, or error  
		- Don't remove host if userinfo or port are not empty  
		- Lowercase other components  
- Protocol-Based Normalization:  
	- Example: identifying that always "http://example.com/data" redirects to "http://example.com/data/"  
	- Usually requires actually accessing resources at least once

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-03-01 16:27:27 UTC  
> Url: https://github.com/boostorg/url/issues/65#issuecomment-1055623705  

Some updates:  
  
I've implemented the Syntax-Based comparison _as if_ the components were normalized to avoid allocating memory. The problem we have is the `remove_dot_segments` algorithm (5.2.4) requires a stack of some form. Creating the stack would allocate memory but not creating the stack would make the algorithm `O(n^2)`.  
  
In practice, I think this is rarely going to be `Θ(n^2)`   
- because not many paths will have many ".."s specifically meant to make it `Θ(n^2)`, and  
- we can always create a logical stack buffer that goes up to a point, and  
- URLs themselves are limited to ~2000 chars  
  
So I'm still exploring the solution with no allocations and then we can later include some of these optimizations.
