# CORS的介紹

最新看了一本書，其中一個章節介紹了CORS，本來想跳過去，但看他寫了6頁，所以就看一下為什麼可以寫那麼多頁

後來換用GOOGLE找了CORS的內容，看到一則CORS其實沒想到的那麼簡單，看完我又醉了，所以記錄一下怕我以後會忘記。


基本上我不會有這個問題，因為基於本人的習慣，我會先把跨域的請求，交到伺服端再由伺服端對其他環境進行JSON查詢，但工程師的存在就是為了**特例**的出現

原則上瀏覽器只能對自己的域名進行交易(即AJAX)，但不知何時開始，為了方便就打開了這個原則，就是當伺服端回給你的資料是可以跨不同域名的，瀏覽器就可以使用。

所以伺服端的HEADER就回個("Access-Control-Allow-Origin", "*")這樣別人的AJAX就可以使用你的資料了

DOTNET

```
HttpContext.Response.AddHeader("Access-Control-Allow-Origin", "*")

但這樣可能太麻煩

MVC下我們就加個屬性函數來過一下

    public class AllowCrossSiteJsonAttribute : ActionFilterAttribute
    {
        public override void OnActionExecuted(ActionExecutedContext filterContext)
        {
            
            filterContext.RequestContext.HttpContext.Response.AddHeader("Access-Control-Allow-Origin", "*");
            base.OnActionExecuted(filterContext);
        }
    }

    這樣在其他的Action或Control下就可以用

    [AllowCrossSiteJsonAttribute]
    Public ActionResult GOGOGO()
    {
        ....
    }

PHP 

header("Access-Control-Allow-Origin: *");

至於這個Origin要填什麼就看需求了
```