# #70 - Additional memory specification does not seem to consider the index for block indirect sort [Open]

> Username: vigna  
> Created at: 2023-03-09 13:38:48 UTC  
> Updated at: 2023-03-13 17:55:24 UTC  
> Url: https://github.com/boostorg/sort/issues/70  

Reading the paper about block indirect sort I was very puzzled of how one could manage the blocks indirectly without some sort of index, whose size would have been linear in the data (very small, like below 1%, but still depending on the data, not on the number of threads). Looking at the code, it seems to me that the variable `std::vector<block_pos> index` of the backbone is in fact a vector of additional `usize` values of length equal to the number of blocks. Am I correct?

---

## Comment 1

> Username: fjtapia  
> Created at: 2023-03-13 11:07:36 UTC  
> Url: https://github.com/boostorg/sort/issues/70#issuecomment-1465937993  

Hi Sebastiano,  
  
The first thing is to thank you for your interest in the algorithm.  
The idea behind block_indirect_sort is very simple. In merge algorithms, we  
need a place to store the merged result, so we need additional memory,  
equal to the size of the shuffled data, or half if we use half merge. We  
would still need an additional memory of N/2.  
  
In block indirect sort the idea is to handle the data in blocks of fixed  
length. When we mix two blocks, the first one that remains empty is used to  
store the results of the mix, but to know the correct order of the blocks  
we need a vector with the relative positions of each block, which we call a  
logical ordering. Each thread needs an auxiliary block to do the merge. The  
vector with the relative positions is the vector that you indicate, and  
have  (N / size of the block) elements  
  
The number of elements in the block depends on the size of the data to be  
ordered, for example, for 8-byte numbers, the block has a size of 4096  
elements, but for 512-byte objects, the block size is 128. If we do a  
simple calculation, with an 8-byte number a block occupies 4096 * 8 = 32768  
bytes and we need an 8-byte number in the index. The index occupies 8 /  
32768 = 0.00024 or what is the same as 0.024% of the data. In the case of  
512-byte objects, we have 512 * 128 = 65536, the index occupies 8 / 65536 =  
0.00012 or what is the same, 0.012% of the data.  
  
If we do the ordering with an Intel I3 (4 cores, 8 threads) we need 8 data  
blocks. If they are 8-byte numbers we have 4096 * 8 = 32768 8-byte numbers.  
An index with 32768 elements means 32768 blocks, which is 134217728  
elements. Only after 134 million elements does the index begin to occupy  
more than the auxiliary blocks.  
  
I hadn't seen the idea of fixed-length blocks in any sorting algorithm. I  
had seen it in other algorithms, and not used it like that.  
The interesting part of the algorithm is how to slice those merges of two  
strings of blocks. Small merges generate few pieces and large ones many.  
That allows there to be lots of little jobs to do, and you can launch all  
the threads to work on it. And from the first step there is work for all  
threads, be they many or few. For this reason, the algorithm responds well  
on small processors and servers with many cores.  
The part I'm most proud of, perhaps because I was exhausted, is the final  
part of reorganizing the blocks, to go from logical sorting by index to  
physical sorting. There are sequences of movements. Some sequences can be  
very long or others very short. You had to find the algorithm to cut them  
up, and have work for all the threads. Without that part the algorithm was  
worthless.  
  
If you have any questions, comments, or suggestions, they are always  
welcome. I have two new parallel algorithms, a stable sorting algorithm  
much simpler, and I hope, faster than block_indirect_sort, or another  
stable sorting algorithm, similar to block_indirect_sort, but I have to  
finish coding it and then do the tests. If the results are worthwhile, I  
will submit them for inclusion in Boost Sort. If you're interested, when I  
finish them, I'll let you know.  
  
Yours  
  
Francisco Jose Tapia  
***@***.***  
  
El jue, 9 mar 2023 a las 14:39, Sebastiano Vigna ***@***.***>)  
escribió:  
  
> Reading the paper about block indirect sort I was very puzzled of how one  
> could manage the blocks indirectly without some sort of index, whose size  
> would have been linear in the data (very small, like below 1%, but still  
> depending on the data, not on the number of threads). Looking at the code,  
> it seems to me that the variable std::vector<block_pos> index of the  
> backbone is in fact a vector of additional usize values of length equal  
> to the number of blocks. Am I correct?  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/70>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GEY2W2WVUPLYJN5VJLW3HMPHANCNFSM6AAAAAAVVEFDO4>  
> .  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>

---

## Comment 2

> Username: vigna  
> Created at: 2023-03-13 11:19:35 UTC  
> Url: https://github.com/boostorg/sort/issues/70#issuecomment-1465958828  

I think the algorithm is absolutely brilliant (in particular, as you note, the moving phase) and, yes, please, let me know about these new variants.  
  
But I'm sorting 100 billion 64-bit integers using 40 threads, and while your documentation claims the additional memory is 4096 * 8 * 40 = 1310720 bytes, I see an allocation of 800000000000 / (4096 * 8) * 8 = 195312500 bytes, that is, almost 150 times more than what declared in the documentation.  
  
As I said, it is a small amount of memory with respect to the memory I'm sorting, but the documentation needs to be fixed. The declared amount of additional memory is not correct.

---

## Comment 3

> Username: fjtapia  
> Created at: 2023-03-13 17:38:11 UTC  
> Url: https://github.com/boostorg/sort/issues/70#issuecomment-1466604921  

Hi Sebastiano,  
  
You are right. I will change it, and explain in more detail.  
  
If you are interested in more details about the algorithm, such as the  
strategy to make the partitions, the relation with the optimal size of the  
parts and the cache memory, etc. Contact me and we will talk about it.  
It's a rare pleasure to talk about algorithms in deep. But perhaps it will  
be better to write to my email directly.  
  
Yours  
  
Francisco José Tapia  
***@***.***  
  
  
El lun, 13 mar 2023 a las 12:19, Sebastiano Vigna ***@***.***>)  
escribió:  
  
> I think the algorithm is absolutely brilliant (in particular, as you note,  
> the moving phase) and, yes, please, let me know about these new variants.  
>  
> But I'm sorting 100 billion 64-bit integers using 40 threads, and while  
> your documentation claims the additional memory is 4096 * 8 * 40 = 1310720  
> bytes, I see an allocation of 800000000000 / (4096 * 8) * 8 = 195312500  
> bytes, that is, almost 150 times more than what declared in the  
> documentation.  
>  
> As I said, it is a small amount of memory with respect to the memory I'm  
> sorting, but the documentation needs to be fixed. The declared amount of  
> additional memory is not correct.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/70#issuecomment-1465958828>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GBU5OBNBOQHW7WLGC3W337FHANCNFSM6AAAAAAVVEFDO4>  
> .  
> You are receiving this because you were assigned.Message ID:  
> ***@***.***>  
>

---

## Comment 4

> Username: vigna  
> Created at: 2023-03-13 17:41:29 UTC  
> Url: https://github.com/boostorg/sort/issues/70#issuecomment-1466609624  

> On 13 Mar 2023, at 17:38, Francisco Tapia ***@***.***> wrote:  
>   
>   
> Hi Sebastiano,  
>   
> You are right. I will change it, and explain in more detail.  
>   
> If you are interested in more details about the algorithm, such as the  
> strategy to make the partitions, the relation with the optimal size of the  
> parts and the cache memory, etc. Contact me and we will talk about it.  
> It's a rare pleasure to talk about algorithms in deep. But perhaps it will  
> be better to write to my email directly.  
  
That would be nice, but I don't have your email 🤷🏻‍♂️. You can write me at ***@***.*** ***@***.***>   
  
Ciao,  
  
                                                         seba

---

## Comment 5

> Username: fjtapia  
> Created at: 2023-03-13 17:55:24 UTC  
> Url: https://github.com/boostorg/sort/issues/70#issuecomment-1466632557  

github clean all the e-mails  
  
But we can do it another way. (We are checking if they are smart)  F J T  
A P I A  P O I N T  G M A I L P O I N T C O M  
If the system deletes this, at the page https://github.com/boostorg/sort,  
at the bottom of the page, you can find the e-mail of the three authors of  
the library  
  
Francisco  
  
El lun, 13 mar 2023 a las 18:41, Sebastiano Vigna ***@***.***>)  
escribió:  
  
>  
> > On 13 Mar 2023, at 17:38, Francisco Tapia ***@***.***> wrote:  
> >  
> >  
> > Hi Sebastiano,  
> >  
> > You are right. I will change it, and explain in more detail.  
> >  
> > If you are interested in more details about the algorithm, such as the  
> > strategy to make the partitions, the relation with the optimal size of  
> the  
> > parts and the cache memory, etc. Contact me and we will talk about it.  
> > It's a rare pleasure to talk about algorithms in deep. But perhaps it  
> will  
> > be better to write to my email directly.  
>  
> That would be nice, but I don't have your email 🤷🏻‍♂️. You can write me  
> at ***@***.*** ***@***.***>  
>  
> Ciao,  
>  
> seba  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/70#issuecomment-1466609624>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GEDDQTB7E4FAYHYL43W35L5LANCNFSM6AAAAAAVVEFDO4>  
> .  
> You are receiving this because you were assigned.Message ID:  
> ***@***.***>  
>
