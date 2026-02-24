# #25 Musser-Nishanov generic sequence-matching algorithm(s) [Open]

> Username: jeremy-murphy  
> Created at: 2016-09-11 16:02:57 UTC  
> Updated at: 2024-09-12 05:20:58 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25  

## Introduction  
  
In 1997, [David R. Musser](http://www.cs.rpi.edu/~musser/) and [Gor V. Nishanov](https://www.linkedin.com/in/gor-nishanov-71aa2113) wrote a hitherto unpublished paper and accompanying code, _[A Fast Generic Sequence Matching Algorithm](http://www.cs.rpi.edu/~musser/gp/gensearch1.pdf)_.   
  
It struck me as odd that this algorithm had not been widely adopted, as Musser's _introsort_ had been.  
So I contacted the authors and with their permission am attempting to bring it to a wider audience.  
  
I provide the abstract from their paper here for completeness:  
  
> A string matching—and more generally, sequence matching—algorithm is presented  
> that has a linear worst-case computing time bound, a low worst-case bound on the  
> number of comparisons (2n), and sublinear average-case behavior that is better than  
> that of the fastest versions of the Boyer-Moore algorithm. The algorithm retains its  
> efficiency advantages in a wide variety of sequence matching problems of practical  
> interest, including traditional string matching; large-alphabet problems (as in Unicode  
> strings); and small-alphabet, long-pattern problems (as in DNA searches). Since it is  
> expressed as a generic algorithm for searching in sequences over an arbitrary type T , it  
> is well suited for use in generic software libraries such as the C ++ Standard Template  
> Library. The algorithm was obtained by adding to the Knuth-Morris-Pratt algorithm  
> one of the pattern-shifting techniques from the Boyer-Moore algorithm, with provision  
> for use of hashing in this technique. In situations in which a hash function or random  
> access to the sequences is not available, the algorithm falls back to an optimized version  
> of the Knuth-Morris-Pratt algorithm.  
  
In the benchmarks that I have run, it is faster than Boyer-Moore, but equivalent to Boyer-Moore-Horspool, on 8-bit text.  
  
On '2-bit' text (DNA sequence) however, the custom search traits boosts the performance to 5-10x faster than the nearest rival. I have demonstrated this in a new search test, no. 5.  
  
What is also interesting is that a simpler, fallback algorithm is provided for corpus iterators that are not random access.  
## Design  
  
I have not changed the fundamental algorithms in a material way, they should be recognizable from the original code apart from some simplifications.  
  
What I have done personally is to restructure the interface to match the existing Boost.Algorithm searchers. The Musser-Nishanov search strategy is complicated by having two algorithms, accelerated linear (AL) and hashed accelerated linear (HAL), that are mostly but not entirely independent. If the corpus iterator is not random-access or the (static) suffix size is zero, then AL is selected at compile time. Otherwise, a HAL search object is created, but this does not guarantee that HAL will be used. If the pattern size (known at run time) is smaller than the suffix size, then the HAL search object must fall back to AL.  
  
The user-facing API is a two-part `musser_nishanov` class that does the initial static choice between AL and HAL. Since HAL uses the same data structures as AL plus one more it is easy to implement HAL in terms of AL. This justified making AL a distinct class that is inherited publicly by the non-random-access `musser_nishanov` class, and inherited privately by the HAL class so that it could be used as the fallback algorithm. The HAL class currently _is_ the `musser_nishanov` class for random access iterators, as opposed to being a distinct class.  
  
The following is a crude UML diagram of the class hierarchy, showing the private inheritance in brown and public inheritance in blue, following Doxygen style.  
![image](https://cloud.githubusercontent.com/assets/4635570/26358537/370e9cb6-4016-11e7-94e3-0f5e4c03e8e2.png)  
  
Since the HAL search object makes its final decision about algorithm at run time, I had to represent this choice somehow. The simplest, though not necessarily most efficient, way is with `bind()` and `function<>`. It also chooses a null searcher if the pattern is empty. This method of storing which   
algorithm to call might be causing a slight performance penalty, but it requires more detailed tests.  
## Selected Benchmarks  
  
I whittled the benchmark output down for the purpose of displaying here. These values should be taken with a grain of 5-10% salt: Musser-Nishanov is not consistently faster than Boyer-Moore-Horspool for example.  
### 8-bit random characters  
  
This is an edited `search_test2` output:  
  
```  
Corpus  is 2756252 entries long  
---- Middle -----  
Pattern is 105 entries long  
                       std::search 0.4848 seconds     100%        484781  
                boyer_moore_search 0.1171 seconds   24.16%        117117  
       boyer_moore_horspool_search 0.1076 seconds    22.2%        107602  
            musser_nishanov_search 0.1033 seconds   21.31%        103296  
------ End ------  
Pattern is 43 entries long  
                       std::search 0.6206 seconds     100%        620587  
                boyer_moore_search 0.2075 seconds   33.44%        207515  
       boyer_moore_horspool_search 0.1742 seconds   28.07%        174218  
            musser_nishanov_search 0.1686 seconds   27.17%        168633  
--- Not found ---  
Pattern is 91 entries long  
                       std::search  1.028 seconds     100%       1028228  
                boyer_moore_search 0.1971 seconds   19.17%        197079  
       boyer_moore_horspool_search 0.1825 seconds   17.75%        182502  
            musser_nishanov_search 0.1702 seconds   16.56%        170235  
```  
### DNA sequences  
  
And this is an edited `search_test5` output, which is '2-bit' (DNA) sequence data, for which Musser-Nishanov has a few customized search traits, but I included just one here. The number `--- here ---` is the pattern size, search is timed for finding all matches (which may be zero -- this benchmark needs some work).  
Just to be clear, there is no `musser_nishanov_dna` class, that is just shorthand for specialization.  
  
```  
Corpus  is 997532 entries long  
--- 10 ---  
matches: 2  
                       std::search  2.239 seconds     100%       2239480  
                boyer_moore object 0.6368 seconds   28.43%        636794  
       boyer_moore_horspool object  0.664 seconds   29.65%        663994  
            musser_nishanov object 0.7033 seconds    31.4%        703286  
        musser_nishanov_dna object 0.3932 seconds   17.56%        393151  
--- 20 ---  
matches: 0  
                       std::search   1.99 seconds     100%       1989855  
                boyer_moore object 0.9167 seconds   46.07%        916750  
       boyer_moore_horspool object  1.006 seconds   50.54%       1005750  
            musser_nishanov object 0.9499 seconds   47.74%        949912  
        musser_nishanov_dna object 0.1749 seconds   8.791%        174933  
--- 40 ---  
matches: 0  
                       std::search  1.881 seconds     100%       1880573  
                boyer_moore object 0.7201 seconds   38.29%        720095  
       boyer_moore_horspool object 0.8477 seconds   45.08%        847738  
            musser_nishanov object 0.8151 seconds   43.34%        815128  
        musser_nishanov_dna object 0.09294 seconds  4.942%         92943  
--- 150 ---  
matches: 11  
                       std::search  1.983 seconds     100%       1982931  
                boyer_moore object 0.6318 seconds   31.86%        631754  
       boyer_moore_horspool object 0.6806 seconds   34.32%        680592  
            musser_nishanov object 0.6594 seconds   33.25%        659393  
        musser_nishanov_dna object 0.04976 seconds  2.509%         49759  
```  
## Caveats (or, what is unfinished)  
  
I did not want to let perfect be the enemy of good, but more importantly I wanted to get a conversation about this code underway as soon as possible, so I have opened this PR well before everything is complete.  
- [ ] Documentation.  
- [ ] 'wide char' benchmark.  
- [ ] Non-random-access corpus benchmark.  
- [ ] Worst-case benchmarks.  
- [ ] The benchmark suite for search test 5 needs to be either trimmed or better utilized.

---

## Comment 1

> Username: zamazan4ik  
> Created_at: 2016-09-13 09:14:12 UTC  
> Updated_at: 2016-09-13 09:53:55 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-246622824  

It's very interesting algorithm. Now i compare it with my Aho-Corasick and suffix automata.   
  
I only look through the code, but I found one bug: callings next is ambiguous. Please use boost::next instead of next.   
  
Also will be better, if you add Range-based interface.  
  
Is there any way to find a set of patterns in corpus string with only one calling musser_nishanov?

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2016-09-13 11:19:47 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-246650754  

Thanks for picking up the `next()` ambiguity.  
  
I'll add a Range-based interface eventually.  
  
No, it's identical in semantics to Boyer-Moore, etc.

---

## Comment 3

> Username: zamazan4ik  
> Created_at: 2016-09-13 11:26:58 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-246652214  

Thanks for the fast fixing :-)   
  
In a few hours i will research your implementation more attentively. And will compare perfomance with my proposals(Ah-Corasick and suffix data structures).

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2016-09-13 11:34:00 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-246653542  

Thanks. I'm glad we're coincidentally working on related algorithms and can probably help each other out.

---

## Comment 5

> Username: zamazan4ik  
> Created_at: 2016-09-13 18:05:55 UTC  
> Updated_at: 2016-09-13 18:49:57 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-246770042  

Try to test your code on this test: corpus_string is the book "War and peace", patterns are the British dictionary. I have a segfault.

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2016-09-14 04:29:16 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-246903526  

Goodness, that's a problem, thanks for discovering it. Where exactly did you get your copy of the corpus and dictionary? I'll try with the Gutenberg project texts when I have a chance.  
  
Also, if you could tell me which line of code the segfault is on, that would be very helpful.  
Thanks, cheers.

---

## Comment 7

> Username: zamazan4ik  
> Created_at: 2016-09-14 06:17:00 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-246916759  

Later I will add tests and traces.  
  
Also I recommend write to Boost mailing list about your algorithm. There are a lot of good programmers in Boost and they can help you.

---

## Comment 8

> Username: zamazan4ik  
> Created_at: 2016-09-14 06:21:46 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-246917472  

For downloading from Guttenberg will be useful: 'wget -w 2 -m http://www.gutenberg.org/robot/harvest?filetypes[]=txt&langs[]=en'

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2016-09-14 14:46:27 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-247037186  

Fortunately, that was an easy bug to squash: I had forgotten to put the single-character-pattern edge case back in. Thanks again for picking it up.

---

## Comment 10

> Username: zamazan4ik  
> Created_at: 2016-10-26 21:30:40 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-256483022  

Jeremy, what is current status of your implementation? Is it last version of this algorithm?

---

## Comment 11

> Username: jeremy-murphy  
> Created_at: 2016-10-26 21:39:29 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-256485241  

Essentially. I'm really just waiting for a sign from Marshall that it's an  
algorithm he would accept before I do any more work on it.  
  
On 27 Oct 2016 8:30 AM, "Alexander" notifications@github.com wrote:  
  
> Jeremy, what is current status of your implementation? Is it last version  
> of this algorithm?  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/algorithm/pull/25#issuecomment-256483022,  
> or mute the thread  
> https://github.com/notifications/unsubscribe-auth/AEa7sml5sOuhtjyI8VHfc0QsmfknjBP_ks5q38aBgaJpZM4J6CcR  
> .

---

## Comment 12

> Username: zamazan4ik  
> Created_at: 2016-10-27 05:15:03 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-256548439  

Good. I will try to remind Marshall about your work. After a lot of tests i see, that Musser-Nishanov is useful algorithm.

---

## Comment 13

> Username: jeremy-murphy  
> Created_at: 2016-10-27 15:47:11 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-256683668  

Actually, that does remind me that I started benchmarking for sequences of  
types larger than char, but every algorithm was worse than std::search, at  
least on random data. MN was the least-worst and it might still be worth  
completing those benchmarks.  
  
On 27 Oct 2016 4:15 PM, "Alexander" notifications@github.com wrote:  
  
> Good. I will try to remind Marshall about your work. After a lot of tests  
> i see, that Musser-Nishanov is useful algorithm.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/algorithm/pull/25#issuecomment-256548439,  
> or mute the thread  
> https://github.com/notifications/unsubscribe-auth/AEa7sswwqJEKy_0ssFH1T0aKPfthy0TEks5q4DNXgaJpZM4J6CcR  
> .

---

## Comment 14

> Username: zamazan4ik  
> Created_at: 2016-10-27 15:53:33 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-256686540  

I found one very useful utility with a lot of algorithms, which currently i am porting to C++. This utility will help you with testing on different data sets (also this utility builds plots). https://github.com/smart-tool

---

## Comment 15

> Username: zamazan4ik  
> Created_at: 2017-02-21 22:29:37 UTC  
> Updated_at: 2017-02-21 22:30:34 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-281503173  

What is current status of Musser-Nishanov algorithm?

---

## Comment 16

> Username: zamazan4ik  
> Created_at: 2017-04-30 23:48:09 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-298265178  

@jeremy-murphy Did you test musser-nishanov algortihm with other algorithms? E.g. with Tuned B-M, KMP, Quick-Search, etc. ?

---

## Comment 17

> Username: zamazan4ik  
> Created_at: 2017-05-01 12:43:48 UTC  
> Updated_at: 2017-05-01 14:36:04 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-298328086  

@mclow my tests show that Musser-Nishanov algorithm has very high performance. Should be tested with EBOM (my tests show that EBOM faster on 4-8 length patterns with English text (Bible)) (see this paper: http://www-igm.univ-mlv.fr/~lecroq/articles/acmsurv2013.pdf ).

---

## Comment 18

> Username: mclow  
> Created_at: 2017-05-18 01:31:17 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-302275654  

(Finally) started playing with this.   Noticed that there are no NM tests for search3 and search4.  I added them, and search3 worked fine, but search4 failed, because the range overloads don't exist. See lines 225-268 of boyer_moore.hpp.  
  
*Very* impressive performance.

---

## Comment 19

> Username: jeremy-murphy  
> Created_at: 2017-05-18 14:18:40 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-302417643  

Glad to (finally) hear from you, Marshall.  
  
Ah, search test 3 and 4, yes, with strings as the search elements. I don't remember exactly why I avoided them last year, but I have uploaded a simple but experimental specialization of `search_trait` for `std::string` now. The benchmark results put it slightly ahead of BMH in performance.

---

## Comment 20

> Username: zamazan4ik  
> Created_at: 2017-07-06 23:04:10 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-313542672  

@mclow What is missing in this PR? Can we accept the PR to Boost.Algorithm? If not, what should we fix before accepting?

---

## Comment 21

> Username: jeremy-murphy  
> Created_at: 2017-07-07 04:40:42 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-313585250  

I do have a list of things at the bottom that need finishing, most  
essentially of all is the documentation, which is in progress.  
But more feedback is always appreciated.  
  
  
On 7 Jul 2017 1:04 AM, "Alexander" <notifications@github.com> wrote:  
  
> @mclow <https://github.com/mclow> What is missing in this PR? Can we  
> accept the PR to Boost.Algorithm? If not, what should we fix before  
> accepting?  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/algorithm/pull/25#issuecomment-313542672>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AEa7ssgMkZ2uHeNhif4sLZIRA8Uyy56Iks5sLWfrgaJpZM4J6CcR>  
> .  
>

---

## Comment 22

> Username: zamazan4ik  
> Created_at: 2017-07-07 07:04:13 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-313604355  

@jeremy-murphy Ohh, sorry - i missed it. If you need any help, just tell me. I want to see Musser-Nishanov algorithm in Boost.Algorithm :)

---

## Comment 23

> Username: jeremy-murphy  
> Created_at: 2017-08-25 05:38:14 UTC  
> Updated_at: 2017-08-25 05:38:25 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-324828422  

Thanks, I just really need to motivate myself to finish the documentation and trim the fat on the search test 5 data, as I suspect that is the only real hurdle to merging. Extra benchmarks would be nice but the existing ones already demonstrate that it dominates the current algorithms.

---

## Comment 24

> Username: zamazan4ik  
> Created_at: 2017-08-25 10:00:44 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-324875663  

@jeremy-murphy I know that current situation is very strange. You did a lot of hard work: implemented very fast algorithm, wrote benchmarks, etc.  
  
I try to sync with @mclow every day :-) But he has a lot work in Boost Release Team, C++ Comitee, libc++, etc.  
  
Don't worry! I am trying to solve your problem. Just wait (yeah, open source is so slow...)

---

## Comment 25

> Username: zamazan4ik  
> Created_at: 2017-11-01 21:29:07 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-341248697  

@mclow I remind you about this search algorithm :-)

---

## Comment 26

> Username: zamazan4ik  
> Created_at: 2018-02-08 21:21:19 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-364252177  

@mclow can you check this PR please?

---

## Comment 27

> Username: jeremy-murphy  
> Created_at: 2018-02-12 04:00:42 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-364823932  

@ZaMaZaN4iK it's OK, I'll call for Marshall's attention when it's finished. The list of caveats/unfinished things is real; I'll tick them off or remove them when they're done or remove them completely if I change my mind about them. No need to keep drawing his attention until then, but I'm always ready to engage in a conversation about what is currently here.

---

## Comment 28

> Username: mclow  
> Created_at: 2020-12-15 01:33:28 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-744972033  

Very, very odd.  I *just* got an email about three new commits. But when I come here, I see they were made 16 days ago.

---

## Comment 29

> Username: jeremy-murphy  
> Created_at: 2020-12-15 01:36:37 UTC  
> Url: https://github.com/boostorg/algorithm/pull/25#issuecomment-744976368  

> Very, very odd. I _just_ got an email about three new commits. But when I come here, I see they were made 16 days ago.  
  
Some of the commits might be that old, I only just pushed for the first time in ages.

---
