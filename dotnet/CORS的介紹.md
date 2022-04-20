# CORS的介紹

最新看了一本書，其中一個章節介紹了CORS，本來想跳過去，但看他寫了6頁，所以就看一下為什麼可以寫那麼多頁，看完之後我就糊塗了，不就是一個跨域請求嗎，怎寫到又要加DI又要寫Midddeare的。

所以我改用GOOGLE找了CORS的內容，看到一則CORS其實沒想到的那麼簡單，看完我又醉了，整張看完問題不在HEADER有沒有Access-Control-Allow-Origin，而是在PROXY下的header改寫問題，其內容表達的是不要以為寫了Access-Control-Allow-Origin就可以用了，如果被改了，那你還是沒有用滴。

所以記錄一下怕我以後會忘記。


基本上我不會有這個問題，因為基於本人的習慣，我會先把跨域的請求，交到伺服端再由伺服端對其他環境進行JSON查詢，但工程師的存在就是為了**特例**的出現

原則上瀏覽器只能對自己的域名進行交易(即AJAX)我們把這叫同源政策，但不知何時開始，有人為了方便就打開了這個原則，就是當伺服端回給你的資料是可以跨不同域名的，瀏覽器就可以使用，這就是把"偷你的資料"這件事打開了一個大門，意思是有間公司跟小偷說，你可以把別人的東西放到我這，就不用怕出事了，因為我是合法的另一方面我也可以寫一個套件去攻擊有開放CORS的網站。就像大家約好了AI機器人不能殺人，但..有人為了賣士兵機器人就打開了這個準則，並公告全世界在這個前提下機器人可以殺人，所以每個機器人都可以殺人了。

以上都是癈話，以下是CORS

伺服端的HEADER回個("Access-Control-Allow-Origin", "*")這樣別人的AJAX就可以使用你的資料了，所以就想法子讓你的RESPONSE.HEADER中有這個值，中間沒有經過PROXY或CDN什麼的，原則上就沒問題了。

#### DOTNET

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
```

#### PHP 
```
header("Access-Control-Allow-Origin: *");

至於這個Origin要填什麼就看需求了
```