# #2224 - Will beast add support for swagger like golang? [Closed]

> Username: heafox  
> Created at: 2021-05-06 05:49:13 UTC  
> Updated at: 2021-05-06 19:22:30 UTC  
> Closed at: 2021-05-06 19:22:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2224  



---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-06 08:50:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2224#issuecomment-833352952  

Are you able to describe what support you are hoping to see?

---

## Comment 2

> Username: heafox  
> Created at: 2021-05-06 08:53:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2224#issuecomment-833355414  

like this https://github.com/swaggo/swag

---

## Comment 3

> Username: heafox  
> Created at: 2021-05-06 08:55:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2224#issuecomment-833356377  

```go  
func main() {  
	r := gin.Default()  
  
	c := controller.NewController()  
  
	v1 := r.Group("/api/v1")  
	{  
		accounts := v1.Group("/accounts")  
		{  
			accounts.GET(":id", c.ShowAccount)  
			accounts.GET("", c.ListAccounts)  
			accounts.POST("", c.AddAccount)  
			accounts.DELETE(":id", c.DeleteAccount)  
			accounts.PATCH(":id", c.UpdateAccount)  
			accounts.POST(":id/images", c.UploadAccountImage)  
		}  
    //...  
	}  
	r.GET("/swagger/*any", ginSwagger.WrapHandler(swaggerFiles.Handler))  
	r.Run(":8080")  
}  
```

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-05-06 11:24:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2224#issuecomment-833447142  

This looks like a higher level concern that beast.  
There is nothing stopping someone writing a library like this that uses beast for the HTTP encoding and decoding.
