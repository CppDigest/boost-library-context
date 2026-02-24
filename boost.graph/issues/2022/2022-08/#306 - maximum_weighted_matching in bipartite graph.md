# #306 - maximum_weighted_matching in bipartite graph [Closed]

> Username: zuliani99  
> Created at: 2022-08-11 15:44:21 UTC  
> Updated at: 2023-06-05 03:35:35 UTC  
> Closed at: 2023-05-28 20:28:41 UTC  
> Url: https://github.com/boostorg/graph/issues/306  

I'm trying to make a comparison of the maximum_weighted_matching and an implementation of the [auction algorithm](https://www.mit.edu/~dimitrib/Auction_Trans.pdf). The problem is that when I run the MWM on a not fully connected bipartite graph like the example bellow it does not solve correctly the assignemt problem whereas the AU does.  
  
```  
Generation of a Bipartite Graph with 4 vertices per part: done  
  
graph G {  
0;  
1;  
2;  
3;  
4;  
5;  
6;  
7;  
0--5  [weight=21];  
1--4  [weight=5];  
1--5  [weight=34];  
1--6  [weight=46];  
2--7  [weight=42];  
2--6  [weight=10];  
3--6  [weight=36];  
3--7  [weight=37];  
}  
  
Execution of Maximum Weighted Matching... Finished  
The matching is: (1,1)(2,3)(3,2)  
It took: 50.300000us, with total cost: 112  
  
Execution of Auction Algorithm... Finished  
The matching is: (0,1)(1,0)(2,3)(3,2)  
|Bidder:1|Best item:0|Value first best item:5.000000|Value second best item:-1.000000|  
|Bidder:0|Best item:1|Value first best item:21.000000|Value second best item:-1.000000|  
|Bidder:3|Best item:2|Value first best item:23.750000|Value second best item:4.750000|  
|Bidder:2|Best item:3|Value first best item:42.000000|Value second best item:10.000000|  
|Item:0|Cost:6.250000|Higher bidder:1|Higher bid:6.250000|  
|Item:1|Cost:89.000000|Higher bidder:0|Higher bid:22.250000|  
|Item:2|Cost:70.000000|Higher bidder:3|Higher bid:19.250000|  
|Item:3|Cost:129.000000|Higher bidder:2|Higher bid:32.250000|  
It took: 6.800000us, with total cost: 104.000000 and 4 iterations  
```

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2022-08-12 01:41:00 UTC  
> Url: https://github.com/boostorg/graph/issues/306#issuecomment-1212650287  

Yeah, unfortunately this algorithm appears to have some major bugs. If you look in the list of issues, you'll find it mentioned several times.

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2022-08-12 01:48:17 UTC  
> Url: https://github.com/boostorg/graph/issues/306#issuecomment-1212652925  

@yi-ji

---

## Comment 3

> Username: yi-ji  
> Created at: 2022-08-14 15:13:22 UTC  
> Url: https://github.com/boostorg/graph/issues/306#issuecomment-1214398655  

sorry about that. I'll find time to see how I can get it fixed

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2022-08-15 00:01:14 UTC  
> Url: https://github.com/boostorg/graph/issues/306#issuecomment-1214478049  

@yi-ji , thanks. I recommend start by adding new unit tests based on the failures reported here in the issues, open a PR with those failing tests and mention it here. I'll try to have a quick look over the implementation to see if anything obviously wrong stands out.

---

## Comment 5

> Username: yi-ji  
> Created at: 2023-05-28 05:17:58 UTC  
> Updated at: 2023-05-28 05:25:12 UTC  
> Url: https://github.com/boostorg/graph/issues/306#issuecomment-1565892338  

@zuliani99 Thank you for using the algorithm!  
With all due respect, the issue you said could not be reproduced. (The algorithm does have some glitches, I've started looking into it lately; but the algorithm solved your example correctly).   
  
The algorithm output a maximum weighted matching of weight sum `112`:   
```  
{1, 5} // weight = 34  
{2, 7} // weight = 42  
{3, 6} // weight = 36  
```  
I've manually verified that is correct, and the brute-force search also gave the same result.  
Here is a test code: https://gist.github.com/yi-ji/0d4d149a52f8137495bb7339e7c55435  
  
Please check the implementation of your Auction algorithm (your output matching and the weight sum `104` is not the maximum weighted matching in this example), and let me know if you have any questions, or we can close this as not a bug. Thank you!

---

## Comment 6

> Username: jeremy-murphy  
> Created at: 2023-06-05 03:35:35 UTC  
> Url: https://github.com/boostorg/graph/issues/306#issuecomment-1575989212  

Thanks for looking into it, @yi-ji !
