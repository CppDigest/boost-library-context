# #391 - my bash program wont work with boost [Open]

> Username: igorgsouza  
> Created at: 2022-06-25 06:19:18 UTC  
> Updated at: 2022-06-25 13:34:47 UTC  
> Url: https://github.com/boostorg/asio/issues/391  

I can compile and run my code using boost perfectly on the powershell, however when I try to run the executable on bash the program runs but I get no outputs, as if the program crashed  
  
It happens when I include boost libraries, if I remove them it goes back to working, I am using CMake and Ninja to compile and run everything

---

## Comment 1

> Username: igorgsouza  
> Created at: 2022-06-25 06:25:56 UTC  
> Updated at: 2022-06-25 06:27:19 UTC  
> Url: https://github.com/boostorg/asio/issues/391#issuecomment-1166288119  

update: It seems that the problem are not the boost libraries itself, but rather the following function:  
  
```  
void print(const boost::system::error_code & /*e*/, boost::asio::steady_timer *t, Reservoir &r)  
{  
	while (r.time() < 10)  
	{  
		std::cout << "Water Level: " << r.water_level() << " Time: " << r.time() << std::endl;  
  
		r.next_second();  
  
		t->expires_at(t->expiry() + boost::asio::chrono::milliseconds(1000));  
		t->async_wait(boost::bind(print, boost::asio::placeholders::error, t, r));  
	}  
}  
```
