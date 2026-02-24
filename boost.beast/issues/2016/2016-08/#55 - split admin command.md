# #55 - split admin command [Closed]

> Username: vinniefalco  
> Created at: 2016-08-19 16:55:43 UTC  
> Updated at: 2016-08-19 17:03:19 UTC  
> Closed at: 2016-08-19 17:03:19 UTC  
> Url: https://github.com/boostorg/beast/issues/55  

Need a command to split a database into two. This could work conceptually by iterating the data file and writing a new data/key file until reaching the halfway point in terms of file size, then continue the iteration and write the second data/key file. In practice to make this work in reasonable time frames (days instead of weeks) it would render large portions of the key file in memory the way that rekey works.
