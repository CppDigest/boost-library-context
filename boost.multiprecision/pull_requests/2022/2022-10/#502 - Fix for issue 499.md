# #502 Fix for issue 499 [Closed]

> Username: mborland  
> Created at: 2022-10-12 17:40:27 UTC  
> Updated at: 2023-02-16 17:02:51 UTC  
> Closed at: 2023-02-16 17:02:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/502  

Replaced boost::lexical_cast with std::stol without updating the type of exception being caught

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-10-12 17:52:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/502#issuecomment-1276536085  

Does this work?  strtoll is non-throwing.

---

## Comment 2

> Username: mborland  
> Created_at: 2022-10-12 17:58:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/502#issuecomment-1276542072  

> Does this work? strtoll is non-throwing.  
  
This uses `stol` which throws `std::invalid_argument` like the issue was seeing as opposed to returning 0 like `strtol`. The catch block was only for `bad_lexical_cast` which was causing `std::invalid_argument` to propagate to the user.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2022-10-12 18:03:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/502#issuecomment-1276547429  

Thank you for going after this, Matt (@mborland)!

---

## Comment 4

> Username: yaroslavchahovets  
> Created_at: 2022-10-14 10:01:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/502#issuecomment-1278782679  

Hello!  
I checked this fix (commit 60a40c598873c47e8e5e94d438eab1bc913741b0) and found the following.  
1. It fixes the case when input string is "wrong".  
2. It does not fix cases when input string is "12a3.4" and "1.2a34" (see https://github.com/boostorg/multiprecision/issues/499#issue-1398168644)  
3. It introduces some regress. When input string has scientific notation, for example "3.4e1a2", the expected result is throwing the std::runtime_exception with the text "Unable to parse the string \"3.4e1a2\" as a floating point value.", whereas with this fix, the test program generates no exception, result is 34.  
  
So I think this commit (60a40c5988) does a good job, but another commit(s) should be added to fix all the cases.  
Previously I proposed the idea for fix  "12a3.4" and "1.2a34" cases here: https://github.com/boostorg/multiprecision/issues/499#issuecomment-1270023503  
  
Thank you for your work, @mborland .

---

## Comment 5

> Username: mborland  
> Created_at: 2022-10-14 17:47:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/502#issuecomment-1279295078  

@yaroslavchahovets This next pair of commits should address your other cases. Thanks for the feedback.

---

## Comment 6

> Username: yaroslavchahovets  
> Created_at: 2022-10-15 11:22:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/502#issuecomment-1279724488  

Now all cases I reported previously ("wrong", "12a3.4", "1.2a34", "3.4e1a2") produce expected behavior.  
But the cases "12L3.4", "12f3.4" produce wrong behavior (no exceptions, value is 12.4).  
Also the cases "1.2L34", "1.2f34" produce wrong behavior (no exceptions, value is 1.00000002).  
  
Thank you.

---

## Comment 7

> Username: yaroslavchahovets  
> Created_at: 2022-10-16 12:24:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/502#issuecomment-1279959472  

Hello. Commit https://github.com/boostorg/multiprecision/pull/502/commits/e1abb81da102d86046e8381bd78e27a67fad7b9a fixes behavior for cases "12L3.4", "12f3.4", thanks.  
But for the 'normal' cases "1.2f", "12f", "12L" it throws an exception. I think the correct behavior for these cases is to produce values 1.2, 12, 12 respectively.

---

## Comment 8

> Username: yaroslavchahovets  
> Created_at: 2022-10-16 12:40:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/502#issuecomment-1279962007  

Or, alternatively, we can consider format with F,f,L,l suffixes illegal (as it was in boost 1.78.0 and earlier). In this case we should keep the behavior of commit https://github.com/boostorg/multiprecision/pull/502/commits/e1abb81da102d86046e8381bd78e27a67fad7b9a  and delete all the code which implements functionality of F,f,L,l suffixes.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2022-10-16 14:45:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/502#issuecomment-1279984015  

I would reasonably strongly favour keep suffixes as illegal: it's not clear what meaning they confer in a multiprecision context.

---

## Comment 10

> Username: mborland  
> Created_at: 2022-10-17 18:22:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/502#issuecomment-1281294664  

@jzmaddock This should be good for review. I left the suffixes as illegal.

---

## Comment 11

> Username: yaroslavchahovets  
> Created_at: 2022-12-04 17:54:33 UTC  
> Updated_at: 2022-12-04 17:54:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/502#issuecomment-1336478270  

@jzmaddock Please consider integrating this pull request into the main repository

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2022-12-05 17:18:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/502#issuecomment-1337770336  

Sorry for being late to review this, just checking this out, we still erroneously parse things like "23.5-3e-26"

---

## Comment 13

> Username: mborland  
> Created_at: 2022-12-21 11:27:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/502#issuecomment-1361196671  

@jzmaddock since I am a novice at regex can you take a look at this? The expression is [here](https://regex101.com/r/D5H8xT/1) and should match any valid number. I am receiving 0 matches once plugged into `std::regex_search`.

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2023-02-16 17:02:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/502#issuecomment-1433414177  

Fixed in alternate PR, closing.

---
