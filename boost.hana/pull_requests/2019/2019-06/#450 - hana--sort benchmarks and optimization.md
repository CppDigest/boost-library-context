# #450 hana::sort benchmarks and optimization  [Merged]

> Username: qchateau  
> Created at: 2019-06-11 22:37:06 UTC  
> Updated at: 2019-09-24 19:54:43 UTC  
> Merged at: 2019-09-24 12:47:23 UTC  
> Closed at: 2019-09-24 12:47:23 UTC  
> Url: https://github.com/boostorg/hana/pull/450  

# Intro  
  
Hello, and thanks for the awsome library. I hope I can help making it tiny bit better :)  
  
This PR attempts to fix issue #256 and only contains two commits. The first adds some basic benchmarks to the sort algorithm. The second reworks the sort algorithm as a merge sort.  
  
Please let me know if this is acceptable, or what to change to make it so.  
  
# Benchmarks  
  
The benchmark is very basic, it only benchmarks `hana::tuple`, for a few different  
lengths, up to 100 elements. I decided not to go higher than 100 to avoid very long benchmarks and high RAM usage during benchmarks.  
  
A few basic test case are benmarked:  
- sorted tuple  
- reverse-sorted tuple  
- randomize tuple  
- sorted tuple except first element  
- sorted tuple except last element  
  
I'm sure we all have many more ideas for other test cases but I'm not sure there is actually a point in benchmarking more.  
  
# Sort algorithm  
  
The current implementation is an insersion sort, which performs well for reverse-sorted tuples, but is not exceptional for any other case.  
  
I implemented the sort algorith as a merge sort, which performs surprisingly well.  
  
## Time  
  
Here are the results of the benchmarks using gcc 8.2.0 for a 100-elements tuple (don't mind the performances of my poor laptop). You can find the complete JSON benchmarks results at the end of this message.  
  
Test case        | Sorted | Reversed | Random | Sorted but first | Sorted but last  
---------------- | ------ | -------- | ------ | ---------------- | ---------------  
Insersion sort   | 8.261  | 0.632    | 3.189  | 8.500            | 8.506  
Merge sort       | 0.883  | 1.013    | 1.259  | 1.081            | 1.124  
  
Merge sort perform almost always better. Notably, it has less variation between its best and worst case. Also, its best case is the already-sorted tuple, which is a plus in my option.  
  
## Memory usage and template recursion depth  
  
No formal benchmark here, but some custom tests using gcc's `-ftime-report` and `-ftemplate-depth`. I compiled a `hana::sort` for a randomized 100-elements tuple and a randomized 50-elements tuple. Note that the tuples were identical for both the insersion sort and the merge sort.  
  
RAM usage      | 50-elements | 100-elements  
-------------- | ----------- | ------------  
Insersion sort | 116027 kB   | 274654 kB  
Merge sort     | 97620 kB    | 152039 kB  
  
Minimum -ftemplate-depth | 50-elements | 100-elements  
------------------------ | ----------- | ------------  
Insersion sort           | 99          | 188  
Merge sort               | 56          | 107  
  
The merge sort requires less memory and less template recursion depth. The evolution of both these metric related to the tuple length also seems better.  
  
# Raw benchmark results  
  
insersion sort (current algorithm):  
```  
{  
  "title": {  
    "text": "Compile-time behavior of sort"  
  },  
  "series": [  
    {  
      "name": "hana::tuple::sorted",  
      "data": [[0, 0.12189327700070862], [1, 0.12702687800083368], [2, 0.13320911899972998], [5, 0.14640919399971608], [10, 0.17684753899993666], [20, 0.28853733600044507], [50, 1.2496988319999218], [100, 8.261367244998837]]  
    },  
    {  
      "name": "hana::tuple::reversed",  
      "data": [[0, 0.13518574299996544], [1, 0.13135910799974226], [2, 0.1403258989994356], [5, 0.14755949700156634], [10, 0.16125590599949646], [20, 0.1943193400002201], [50, 0.32196244499937166], [100, 0.6322877889997471]]  
    },  
    {  
      "name": "hana::tuple::rand",  
      "data": [[0, 0.12381545400057803], [1, 0.12871033900046314], [2, 0.13587248499970883], [5, 0.15021091699964018], [10, 0.17622353799924895], [20, 0.23890691499946115], [50, 0.7043842670009326], [100, 3.1888558009995904]]  
    },  
    {  
      "name": "hana::tuple::sorted_but_first",  
      "data": [[0, 0.12633763700068812], [1, 0.13862444699952903], [2, 0.14123353699869767], [5, 0.1601948329989682], [10, 0.19120241999917198], [20, 0.31753956099964853], [50, 1.32555256500018], [100, 8.49972412200077]]  
    },  
    {  
      "name": "hana::tuple::sorted_but_last",  
      "data": [[0, 0.13040008999996644], [1, 0.1387038000011671], [2, 0.13980583700140414], [5, 0.15381494100074633], [10, 0.18541962200106354], [20, 0.3065099300001748], [50, 1.3008124890002364], [100, 8.505800036999062]]  
    }  
  ]  
}  
```  
  
merge sort (proposed algorithm):  
```  
  
  
{  
  "title": {  
    "text": "Compile-time behavior of sort"  
  },  
  "series": [  
    {  
      "name": "hana::tuple::sorted",  
      "data": [[0, 0.12493470600020373], [1, 0.1553466599998501], [2, 0.15194974800033378], [5, 0.14653714599990053], [10, 0.17987262700080464], [20, 0.23612296799910837], [50, 0.4784531200002675], [100, 0.8831240349991276]]  
    },  
    {  
      "name": "hana::tuple::reversed",  
      "data": [[0, 0.17491978799989738], [1, 0.1380396650001785], [2, 0.15627997199953825], [5, 0.17984615299974394], [10, 0.19803481999952055], [20, 0.24726633699901868], [50, 0.497444736000034], [100, 1.0125894479988347]]  
    },  
    {  
      "name": "hana::tuple::rand",  
      "data": [[0, 0.12181186999987403], [1, 0.12788815700150735], [2, 0.16966947299988533], [5, 0.15471644199897128], [10, 0.17313473399917712], [20, 0.25020471300013014], [50, 0.5536038049995113], [100, 1.2589463830008754]]  
    },  
    {  
      "name": "hana::tuple::sorted_but_first",  
      "data": [[0, 0.13644047399975534], [1, 0.14535208699999203], [2, 0.1550784580012987], [5, 0.2969821180013241], [10, 0.21241696499964746], [20, 0.2675821630000428], [50, 0.4959278879996418], [100, 1.0816410260013072]]  
    },  
    {  
      "name": "hana::tuple::sorted_but_last",  
      "data": [[0, 0.13001319499926467], [1, 0.15044038199994247], [2, 0.16996815000129573], [5, 0.15973293499882857], [10, 0.18661433799934457], [20, 0.24066195599880302], [50, 0.4494423630003439], [100, 1.1236133000002155]]  
    }  
  ]  
}  
```

---

## Comment 1

> Username: qchateau  
> Created_at: 2019-08-01 07:31:42 UTC  
> Url: https://github.com/boostorg/hana/pull/450#issuecomment-517159560  

@ldionne   
I rebased on the latest develop all CI now complete successfully

---

## Review 2 [Commented]

> Username: ldionne  
> Created_at: 2019-08-05 19:13:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/450#pullrequestreview-270954452  

📁 include/boost/hana/sort.hpp

```diff
 214 |+             using sequence = std::index_sequence<seq...>;
 215 |+             using sp = split<
 216 |+                 sequence::size() / 2,
```

> Username: ldionne  
> Created_at: 2019-08-05 19:13:29 UTC  
> Updated_at: 2019-08-14 12:39:32 UTC  
> Url: https://github.com/boostorg/hana/pull/450#discussion_r310751547  

Why not use `sizeof...(seq)`?

> Username: qchateau  
> Created_at: 2019-08-14 12:24:32 UTC  
> Updated_at: 2019-08-14 12:39:32 UTC  
> Url: https://github.com/boostorg/hana/pull/450#discussion_r313845779  

I can't remember a specific reason for this, but using `sequence::size()`, the `using sp =` statement only depends on `sequence`.  
  
Do you see any reason I should use `sizeof` ?


---

## Review 3 [Commented]

> Username: ldionne  
> Created_at: 2019-08-05 19:15:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/450#pullrequestreview-270955394  

📁 include/boost/hana/sort.hpp

```diff
 190 |+             std::index_sequence<l...>,
 191 |+             std::index_sequence<r0 ,r...>,
 192 |+             typename std::enable_if<Nr!=0>::type
```

> Username: ldionne  
> Created_at: 2019-08-05 19:15:38 UTC  
> Updated_at: 2019-08-14 12:39:32 UTC  
> Url: https://github.com/boostorg/hana/pull/450#discussion_r310752292  

You need the `enable_if` because otherwise, when `Nr == 0` AND the second `std::index_sequence` is empty, you get an ambiguity. Is that right?

> Username: qchateau  
> Created_at: 2019-08-07 06:31:48 UTC  
> Updated_at: 2019-08-14 12:39:32 UTC  
> Url: https://github.com/boostorg/hana/pull/450#discussion_r311387443  

The problem is that these two specializations are not strictly ordered because:  
- Template 1 cannot match N==0 AND empty second sequence, matched by template 2  
- Template 2 cannot match N!=0, matched by template 1  
  
Adding enable_if makes sure they will never be both valid candidates in an overload resolution.  
  
As you stated, an ambiguity would occur if N==0 and the second sequence is *not* empty (I'm assuming there is a typo in your comment).

> Username: keryell  
> Created_at: 2019-08-07 09:13:28 UTC  
> Updated_at: 2019-08-14 12:39:32 UTC  
> Url: https://github.com/boostorg/hana/pull/450#discussion_r311448834  

Feel free to add this in the comments, so this information is not lost...

> Username: qchateau  
> Created_at: 2019-08-14 15:00:57 UTC  
> Updated_at: 2019-08-14 15:00:58 UTC  
> Url: https://github.com/boostorg/hana/pull/450#discussion_r313924117  

Done


---

## Comment 4

> Username: ldionne  
> Created_at: 2019-08-05 19:15:59 UTC  
> Url: https://github.com/boostorg/hana/pull/450#issuecomment-518362945  

Thanks a lot for working on this! This looks very promising.

---

## Comment 5

> Username: qchateau  
> Created_at: 2019-09-20 18:06:13 UTC  
> Url: https://github.com/boostorg/hana/pull/450#issuecomment-533655501  

Up, is there anything I can do ?

---

## Comment 6

> Username: ldionne  
> Created_at: 2019-09-24 12:47:42 UTC  
> Url: https://github.com/boostorg/hana/pull/450#issuecomment-534542180  

Thanks _a lot_ for working on that, and for bearing with my slow response times! This is great.

---

## Comment 7

> Username: qchateau  
> Created_at: 2019-09-24 17:38:45 UTC  
> Url: https://github.com/boostorg/hana/pull/450#issuecomment-534667519  

Ah, don't worry, I had fun with this !

---
