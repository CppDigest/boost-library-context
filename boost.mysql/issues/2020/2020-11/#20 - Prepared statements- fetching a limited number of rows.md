# #20 - Prepared statements: fetching a limited number of rows [Closed]

> Username: anarthal  
> Created at: 2020-11-29 17:39:44 UTC  
> Updated at: 2024-08-10 16:17:21 UTC  
> Closed at: 2024-08-10 16:17:21 UTC  
> Url: https://github.com/boostorg/mysql/issues/20  

When issuing a text query, the server immediately sends all rows to the client. This is not avoidable.  
However, there is a mechanism in the protocol to prevent this in binary resultsets. Make use of this feature so the user can decide whether to fetch immediately all the rows or fetch them progressively.

---

## Comment 1

> Username: anarthal  
> Created at: 2022-05-19 09:29:32 UTC  
> Url: https://github.com/boostorg/mysql/issues/20#issuecomment-1131463112  

When implementing this, it is important to provide performance benchmarks in the docs, comparing this cursor approach vs. the "read everything in one go" approach. Suggested:  
* Time to first row  
* Overall time to consume the entire resultset  
* Time elapsed if resultset should be discarded before being complete

---

## Comment 2

> Username: anarthal  
> Created at: 2024-08-10 16:17:21 UTC  
> Url: https://github.com/boostorg/mysql/issues/20#issuecomment-2282198689  

I don't think anyone needs this, given the protocol architecture. The postgres feature makes sense, but MySQL's does not. Please comment if you strongly feel otherwise.
