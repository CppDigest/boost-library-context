# #213 - static_vector size issue Win 10 x64 compilation [Closed]

> Username: illion20  
> Created at: 2022-03-02 23:35:54 UTC  
> Updated at: 2022-07-10 16:40:04 UTC  
> Closed at: 2022-07-10 16:40:04 UTC  
> Url: https://github.com/boostorg/container/issues/213  

I am trying to compile the project from this repo:  
https://github.com/critter-mj/akochan  
  
I have created an issue describing the problem here:  
https://github.com/critter-mj/akochan/issues/18  
  
The only idea I have is that the size of the static_vector is causing this issue. Is there a limitation on size for Win 10 64?  
  
This works  
`const int MAX_EDGE_NUM_PER_THREAD = 100000 * 60;`  
This doesnt work:  
`const int MAX_EDGE_NUM_PER_THREAD = 100000 * 61;`  
For the code  
```  
std::array<boost::container::static_vector<Tehai_Action, MAX_EDGE_NUM_PER_THREAD>, CAL_NUM_THREAD> cand_graph_sub_tsumo_work;  
std::array<boost::container::static_vector<Tehai_Action, MAX_EDGE_NUM_PER_THREAD>, CAL_NUM_THREAD> cand_graph_sub_fuuro_work;  
```  
Error:  
![image](https://user-images.githubusercontent.com/29117895/156467622-18b672bb-407a-49ac-8634-23ab9b98f2c4.png)

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-07-10 16:40:04 UTC  
> Url: https://github.com/boostorg/container/issues/213#issuecomment-1179760548  

There is no limitation for Win x64, it looks like the compiler is generating an incorrect executable, I can't see any bug related to static_vector.
