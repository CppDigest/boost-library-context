# #59 - Lockfree queue delivers data out of order [Open]

> Username: daveboutcher  
> Created at: 2020-06-03 13:03:56 UTC  
> Updated at: 2022-08-05 11:11:19 UTC  
> Url: https://github.com/boostorg/lockfree/issues/59  

This change,  https://github.com/boostorg/lockfree/commit/3d45c000b2cc0f0291e5caf7e67ed759a9917ea9, introduced in boost 1.71.0 to fix valgrind issues, actually introduced a very subtle ordering issue in the queue implementation which also exists in 1.72 and 1.73.  
  
Specifically, the code previously did NOT initialize the `tagged_node_handle` `next` on construction. which works beautifully when a node is allocated/freed/allocated  
```diff  
-            data(v)//, next(tagged_node_handle(0, 0))  
+            data(v), next(tagged_node_handle(null_handle, 0))  
```  
By initializing the next pointer, the change introduces a classic ABA problem (from Wikipedia, "when a location is read twice, has the same value for both reads, and 'value is the same' is used to indicate 'nothing has changed'. However, another thread can execute between the two reads and change the value, do other work, then change the value back, thus fooling the first thread into thinking "nothing has changed" even though the second thread did work that violates that assumption.")  
  
Reverting that single line fixes the issue in both 1.71 and 1.73  
  
The following program demonstrates the problem where data can be returned from the queue out of order. Note that recreating this depends on system load and system architecture, but I have reliably recreated the problem on both server and desktop class systems by repeating this test 10,000 times.  
  
```c++  
// Boost queue stress test....all credits to Wes Darvin  
#include <boost/lockfree/queue.hpp>  
#include <iostream>  
#include <thread>  
#include <vector>  
  
static constexpr int BLOCK_SIZE = 1000;  
static constexpr int NUM_THREADS = 20;  
  
// Enqueue BLOCK_SIZE values in order  
void enqueueThreadMethod(boost::lockfree::queue<int32_t> *queue, int threadID) {  
    int start = BLOCK_SIZE * threadID;  
    for (int i = 0; i < BLOCK_SIZE; i++) {  
        while (!queue->push(start + i))  
            ;  
    }  
}  
  
// Dequeue BLOCK_SIZE values.  Note that the values returned may come from  
// different enqueuers, but for each enqueuer they should be in increasing  
// order.  
void dequeueThreadMethod(  
    boost::lockfree::queue<int32_t> *queue,  
    std::array<int32_t, NUM_THREADS * BLOCK_SIZE> *retValues, int threadID) {  
    for (int i = 0; i < BLOCK_SIZE; i++) {  
        while (!queue->pop((*retValues)[threadID * BLOCK_SIZE + i]))  
            ;  
    }  
}  
  
int main(int argc, char **argv) {  
    std::array<int32_t, NUM_THREADS * BLOCK_SIZE> retValues;  
  
    std::vector<std::thread> threadVector;  
  
    boost::lockfree::queue<int32_t> queue(100);  
  
    // Enqueue and dequeue relatively simulataniously. And yes, we could make  
    // this more robust by adding a barrier, but it works as-is  
    for (int i = 0; i < NUM_THREADS; i++) {  
        threadVector.emplace_back(&enqueueThreadMethod, &queue, i);  
        threadVector.emplace_back(&dequeueThreadMethod, &queue, &retValues, i);  
    }  
  
    for (std::thread &t : threadVector) {  
        t.join();  
    }  
  
    // Now validate the values we dequeued.   All we are checking is  
    // that for each enqueuer, its values only increase...i.e. we  
    // are checking for out-of-order conditions  
    for (int i = 0; i < NUM_THREADS; i++) {  
        // vector of last-seen values by enqueuer. Each  
        // entry should increase  
        std::vector<int> localMax(NUM_THREADS, -1);  
  
        // check the values dequeued by a single dequeuer  
        for (int j = 0; j < BLOCK_SIZE; j++) {  
            int localValue = retValues[i * BLOCK_SIZE + j];  
            int index = localValue / BLOCK_SIZE;  
  
            if (localValue < localMax[index]) {  
                std::cout << "Out of order values: " << localValue << " < "  
                          << localMax[index] << " i: " << i << ", j: " << j  
                          << ", index: " << index << std::endl;  
            }  
            localMax[index] = localValue;  
        }  
    }  
  
    // Now sort all the dequeued values and make sure there are none missing  
    // or duplicated  
    std::sort(retValues.begin(), retValues.end());  
    for (int i = 0; i < (BLOCK_SIZE * NUM_THREADS); i++) {  
        if (retValues[i] != i) {  
            std::cout << "Error checking sorted values at index " << i  
                      << std::endl;  
        }  
    }  
  
    return 0;  
}  
```

---

## Comment 1

> Username: timblechmann  
> Created at: 2020-06-03 15:12:14 UTC  
> Url: https://github.com/boostorg/lockfree/issues/59#issuecomment-638261923  

thanks a lot for this very detailed test case! i need to have a look into this in detail! while i can reproduce it here, i'm wondering, where the actual bug is coming from.  
i have a prototype implementation here, trying to replace the zero-initialisation with a generated number ABA prevention tag, but i want do double-check the michael/scott paper to see where things are going wrong

---

## Comment 2

> Username: daveboutcher  
> Created at: 2020-06-03 16:39:53 UTC  
> Url: https://github.com/boostorg/lockfree/issues/59#issuecomment-638315215  

Well, I think the issue is the interaction of the pool and the queue...we definitely shouldn't zero a tagged node when it goes through the pool....though to be honest, I think the same issue could occur if a node was returned and reallocated from the heap

---

## Comment 3

> Username: itrofimow  
> Created at: 2022-08-04 14:57:56 UTC  
> Url: https://github.com/boostorg/lockfree/issues/59#issuecomment-1205374151  

Hi!  
This is fixed in 1.74 by https://github.com/boostorg/lockfree/commit/df78b9da42d7f5124a865345759d86e95ea79823, am i right?

---

## Comment 4

> Username: daveboutcher  
> Created at: 2022-08-05 08:55:17 UTC  
> Url: https://github.com/boostorg/lockfree/issues/59#issuecomment-1206211427  

doubt it...losing the tag bits (i.e. memsetting everything to zero all the time) is exactly what causes this

---

## Comment 5

> Username: itrofimow  
> Created at: 2022-08-05 11:11:19 UTC  
> Url: https://github.com/boostorg/lockfree/issues/59#issuecomment-1206329505  

> doubt it...losing the tag bits (i.e. memsetting everything to zero all the time) is exactly what causes this  
  
I might be mistaken, but my understanding is that all that https://github.com/boostorg/lockfree/commit/df78b9da42d7f5124a865345759d86e95ea79823 does is reverting erroneous https://github.com/boostorg/lockfree/commit/3d45c000b2cc0f0291e5caf7e67ed759a9917ea9 and initializing some memory to zeroes **at allocation** to prevent valgrind errors, nothing more.  
  
I don't have recent versions of boost at hand, but maybe you could rerun your test to validate it?  
  
@timblechmann could you please shed some light on this?
