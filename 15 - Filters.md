## FILTERS

- DataAnnotation’lara benzer.
- Daha çok actionlar ve controllerlar için kullanılır.
- Proje adımlarında sürekli yapılacak işlemleri daha seri bir şekilde yapmaya olanak tanır.
- 4 türü vardır.
    - IActionFilter
    - IResultFilter
    - IAuthorizationFilter
    - IExceptionFilter
- Bunların dışında kendi filterlarımızı da yazabiliriz.
- Bunların hepsi birer interface’tir. Kodlarımıza implante ederek kullanabiliriz.
- Filtreleri genel olarak Filters diye bir klasör oluşturduktan sonra içine class eklenerek kullanılır. 
- Filtreleri attribute olarak kullanırız.
    - Bunun için filter classımızı FilterAttribute adlı clastan türetmemiz gerek.
- Attribute’leri her action için ayrı ayrı kullanabileceğimiz gibi, controller’ın başına yazarak, o controller’ın kapsadığı tüm actionlar için tek seferde kullanabiliriz.

### 01 – Action Filter

- Action çalıştırılmadan önceki ve sonraki anları yakalamaya yarar.
- Bu method kullanılmazsa ortaya çıkacak yapı

```cs
public ActionResult Index() {
    // Giriş işlemlerini yap
    // kodlar...
    // Çıkış işlemlerini yap
    return View();
}

public ActionResult Index2() {
    // Giriş işlemlerini yap
    // kodlar...
    // Çıkış işlemlerini yap
    return View();
}

public ActionResult Index3() {
    // Giriş işlemlerini yap
    // kodlar...
    // Çıkış işlemlerini yap
    return View();
}
```

- Filter kullanıldıktan sonra ortaya çıkacak yapı

```cs
[ActFilter]
public ActionResult Index() {
    // kodlar...
    return View();
}

[ActFilter]
public ActionResult Index2() {
    // kodlar...
    return View();
}

[ActFilter]
public ActionResult Index3() {
    // kodlar...
    return View();
}
```

- Oluşturduğumuz class’ı Action Filter olarak kullanabilmek için,
    - FilterAttribute classından türetmemiz
    - IActionFilter interface’inden implante etmemiz gerekiyor.

```cs
public class ActFilter : FilterAttribute, IActionFilter
{
    // Action çalıştıktan sonra çalışır
    public void OnActionExecuted(ActionExecutedContext filterContext)
    {
        // Çalıştırılacak Kodlar...
    }

    // Action çalışmadan önce çalışır
    public void OnActionExecuting(ActionExecutingContext filterContext)
    {
        // Çalıştırılacak Kodlar...
    }
}
```

- ActionName ve ControllerName yakalama
    - `filterContext.ActionDescriptor.ActionName`
    - `filterContext.ActionDescriptor.ControllerDescriptor.ControllerName`

### 02 – Result Filter

- View çalıştırılmadan önceki ve sonraki anları yakalar.
- Oluşturduğumuz class’ı Result Filter olarak kullanabilmek için,
    - FilterAttribute classından türetmemiz
    - IResultFilter interface’inden implante etmemiz gerekiyor.

```cs
public class ResFilter : FilterAttribute, IResultFilter
{
    // View gönderildikten sonra
    public void OnResultExecuted(ResultExecutedContext filterContext)
    {
        // Çalıştırılacak Kodlar...
    }

    // View gönderilmeden önce
    public void OnResultExecuting(ResultExecutingContext filterContext)
    {
        // Çalıştırılacak Kodlar...
    }
}
```

- ActionName ve ControllerName için,
    - `filterContext.RouteData.Values[“action”].ToString()`
    - `filterContext.RouteData.Values[“controller”].ToString()`

### 03 – Authorization Filter

- Login işlemleri için kullanılır.
    - Login işlemleri için gerekli olan Session ve Cache kontrolleri burada yapılır. 
- Action çalışmadan önce çalışır.
- `IAuthorizationFilter`’dan implante edilir.
- Session yakalama,
    - `filterContext.HttpContext.Session[ string session_key ]`
- Action çalışmadan direk url yönlendirmesi istiyorsak;
    - `filterContext.Result = new RedirectResult( string gidilecek_url )`

```cs
public class AuthFilter : FilterAttribute, IAuthorizationFilter
{
    public void OnAuthorization(AuthorizationContext filterContext)
    {
        // Çalıştırılacak Kodlar...
    }
}
```

### 04 – Exception Filter
- Action çalışırken herhangi bir hata oluştuğunda çalışır.
- Gereken kodlar : 
    - `filterContext.ExceptionHandled = true;`
        - Hataları bizim yöneteceğimizi söylüyoruz.
    - `filterContext.Exception`
        - Hatayı yakalama ve istenildiğinde bir sayfa gönderme
    - `filterContext.Result = new RedirectResult()`
        - Hata gösterimi için kullandığımız sayfaya yönlendirme yapıyoruz.

```cs
public class ExpFilter : FilterAttribute, IExceptionFilter
{
    public void OnException(ExceptionContext filterContext)
    {
        filterContext.ExceptionHandled = true;
        filterContext.Controller.TempData["errorMessage"] = filterContext.Exception;
        filterContext.Result = new RedirectResult("/ErrorPage");
    }
}
```

- Burada hatayı bu filter içerisinde yakalayıp, gerekli işlemleri yaptıktan sonra, istersek klasik hata sayfasına değil de, bizim oluşturduğumuz sayfaya gitmesine olanak tanıyoruz.
- Hatalar taşınırken Exception modeli olarak taşınır.
- Oluşan hataların db üzerinde log kayıtları tutulacaksa bu şekilde bir filtre kullanılarak hataları ve zamanlarını tutabiliriz.