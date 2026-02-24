# #111 Add 'boost::intersperse' string algorithm [Open]

> Username: denzor200  
> Created at: 2022-11-14 09:28:54 UTC  
> Updated at: 2023-01-31 11:32:25 UTC  
> Url: https://github.com/boostorg/algorithm/pull/111  

Analague of `ranges::views::intersperse` from range-v3 library.  
```  
const std::string input{"foobarbaxbat"};  
std::cout << boost::intersperse_copy(input, ',') << '\n';      // Outputs: f,o,o,b,a,r,b,a,x,b,a,t  
```

---

## Review 1 [Commented]

> Username: jgopel  
> Created_at: 2022-11-15 17:34:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/111#pullrequestreview-1181296222  

📁 string/test/intersperse_test.cpp

```diff
  90 |+     string test = "test";
  91 |+     CharGenerator g1, g2;
  92 |+     BOOST_CHECK(intersperse_generate_copy(test, g1) == "t1e2s3t");
```

> Username: jgopel  
> Created_at: 2022-11-15 17:34:18 UTC  
> Url: https://github.com/boostorg/algorithm/pull/111#discussion_r1023082755  

An important property of this is only implicitly verified here - `test` should not be modified by `*_copy`. In the other tests this is assured because the inputs are `const`, but that's not the case here.


---

## Review 2 [Commented]

> Username: jgopel  
> Created_at: 2022-11-17 18:27:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/111#pullrequestreview-1184845770  

📁 string/test/intersperse_test.cpp

```diff
  92 |+     BOOST_CHECK(intersperse_generate_copy(input, g1) == "t1e2s3t");
  93 | 
  94 |+     string test = "test";
```

> Username: jgopel  
> Created_at: 2022-11-17 18:27:28 UTC  
> Updated_at: 2022-11-17 18:27:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/111#discussion_r1025554408  

I like this - maybe just a naming nit but you could do something like `const string immutable_input = ...` and `string mutable_input = ...`


---

## Comment 3

> Username: jgopel  
> Created_at: 2022-11-17 19:09:34 UTC  
> Url: https://github.com/boostorg/algorithm/pull/111#issuecomment-1319081693  

👍 LGTM - @mclow will have to approve.

---

## Comment 4

> Username: denzor200  
> Created_at: 2023-01-31 11:32:25 UTC  
> Url: https://github.com/boostorg/algorithm/pull/111#issuecomment-1410195601  

@mclow hello, would you like to review this PR?

---
