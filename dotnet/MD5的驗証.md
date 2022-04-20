# MD5的密碼驗証

很多文章說MD5不安全，我很不理解，怎就不安全了，於是我用心拜讀很多文章，我發現所謂的不安全是指，我把密碼MD5儲存了，存多了大家都知你存的是什麼，又或是不同的字串得到相同的MD5。

但MD5不是用來組合內容後比對是否變動的簡單查閱嗎?從我用MD5的那天開始，我從來沒有針對單獨字串MD5比對的。

所以要替MD5這種驗証類功能，擊個鼓看能不能找到青天大老爺

接下來就是可能會用的密碼儲存方式，跟驗証方式

- [MD5的密碼驗証](#md5的密碼驗証)
  - [密碼儲存](#密碼儲存)
  - [密碼驗証](#密碼驗証)

## 密碼儲存

我個使用一個值做為組合的條件，如鍵值，創造出的MD5字串在組合一個值如建立日期來防止重覆，可能不能百分百確認，但我可以肯定那樣的機率我可能遇不到

``` 字串合並

    回傳一個沒有-的大寫字串
    public static string md5_string(params object[] args)
    {
        //轉換成字串 a,b,c
        string code = string.Join(",", args.Select(o => o.ToString()));
        //轉成byte
        byte[] msg = Encoding.UTF8.GetBytes(code);

        using (var cryptoMD5 = MD5.Create())
        {
            var hash = cryptoMD5.ComputeHash(msg);
            return BitConverter.ToString(hash)
                .Replace("-", "")
                .ToUpper();
        }
    }
```

``` 密碼組合

    MD5(MD5(鍵值+密碼),加權值如建立日期)
    這樣的值我會存在資料庫中
    public static string md5_pwd(object reckey,string pwd,string token)
    {
        return md5_string(md5_string(reckey, pwd), token);
    }
```

## 密碼驗証

我會先取出帳號後在判斷，因為我的老闆要我告訴他，都有什麼的狀態發生在登入這事

``` 驗証

    var model_user = new Model_loginuser();
    //SELECT * FROM USER WHERE uid = @uid的內容
    var db_user = model_user.get_user(uid);
    //找不到帳號
    if (db_user == null) return Common.response_error(403, "帳號密碼錯誤!",$"使用{uid}登入但沒查到帳號");
    //密碼使用二次壓縮用來減少相同MD5的問題 md5(md5(reckey,pwd),可能是日期)
    //取得要判斷的密碼
    pwd = Common.md5_pwd(db_user.sRecKey, pwd, 日期吧);
    if (db_user.lStatus != 1)
    {
        //判斷有沒有啟用，沒啟用還有人來那就是來亂的
        return Common.response_error(403, "帳號尚未啟用!!",$"使用{uid} => {db_user.sView}登入但帳號未啟用");
    }
    else if (db_user.lError > 5)
    {
        //被查詢太多次了，因為我每查一次user的資料就會被lError加1，一直到登入後歸0
        return Common.response_error(403,"帳號已停止使用!!", $"使用{uid} => {db_user.sView}登入但帳號已鎖住");
    }
    else if (model_user["PASSWORD"] != pwd)
    {
        //最後才是比對密碼
        return Common.response_error(403,"帳號密碼錯誤!", $"使用{uid} => {db_user.sView}登入但密碼認証錯誤");
    }

    //以下就通過了密碼驗証了

```