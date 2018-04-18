## ROUTING

### 01 – Giriş

- RouteConfig ve Global.asax dosyaları nedir?
- IgnoreRoute ve MapRoute ile izin verilmeyen ve verilen route’lar
- Default Route ayarları
- Parametre tanımlama ve linkleri parametre yardımıyla çağırma
    - Neden parametreler?
        - SEO optimizasyonu
        - Düzgün URL
- RouteConfig ayarlarında girilen ayarlamaların sıralaması önemlidir. Öncelikle hangisiyle uyarsa ( opsiyonel olanlar dışında ) ona göre url düzenlemesi yapılır.
- Ayarlamalarda verilen parametlerelerin opsiyonel olması isteniyorsa;
    - `UrlParameter.Optional olarak ayarlanmalıdır.`
- Kategori sayfaları yapma :
    - RouteConfig içinde for kullanarak birden fazla ayar tanımlama
    - Controller içinde “this.RouteData.Values” içinde gelenlere breakpoint ile bakma
    - “this.RouteData.Values[ string value ] ile gelen değerleri alma ve yazdırma
- Aynı düzenlemeyi UrlParameter.Optional ile yapma

### 02 – Route Constrainsts ( Rota Kısıtlamaları )

- Rota oluştururken girilen değerlerin bizim istediğimiz tarza olmasını, haricinde 404 hatası çıkarmasını istediğimiz durumlarda kullandığımız ifadelerdir.
- DİKKAT : Rota kısıtlaması kullanıldığında parametreler opsiyonel olarak tanımlanamaz.
- Regular Expressions (Düzenli İfadeler) - Regex
    - (.) -> Bir  karakterin gelecebileceği ve karakteri tanımlamadığımız yerlerde kullanılır.
    - (+) -> Kendinden önce gelen karakterin en az bir kere tekrarlanması gerektiğini belirtir.
    - (*) -> Kendinden önce gelen karakterin sıfır veya daha fazla tekrarlanması gerektiğini belirtir.
    - (?) -> Kendinden önce gelen karakterin en fazla bir kere gelmesi gerektiğini belirtir.
    - ([ ]) -> İçindeki karakterlerden birinin geleceğini belirtir.
        - [ab] [0-9] [a-z]
    - ({ }) -> Kendinden önce gelen karakterin içinde yazılan sayı kadar tekrar edeceğini belirtir.
    - (^) -> Metnin başını ifade eder. Kendinden sonra gelen ifadeyi metnin başında arar.
    - ($) -> Metnin sonunu ifade eder. Kendinden önce gelen ifadeyi metnin sonunda arar.
    - (/s) -> Belirtilen yerde boşluk olması gerektiğini belirtir.
    - (/S) -> Belirtilen yerde boşluk olmaması gerektiğini belirtir.
    - (/d) -> Sayısal ifade geleceğini belirtir.
    - Örnekler : 
        - Cep telefonu deseni : (05)071112233
            - ^(05)[0-9]{9}$   -  (05)/d{9}
        - Tarih deseni : 2014-12-01
            - ^\d{4}-\d{2}-\d{2}$

```cs
routes.MapRoute(
    name: "arsiv",
    url: "arsiv/{kayit}",
    defaults: new { controller = "Kategori", action = "Arsiv" },
    constraints : new {kayit = @"^\d{4}-\d{2}-\d{2}$" }
);
```

#### HttpMetodConstraint ile Rota Kısıtlama

- Hangi metodlarla urlye ulaşılabileceği kısıtlaması bu yöntemle yapılır. 

```cs
routes.MapRoute(
    name: "KategoriEkle",
    url: "kategori/kaydet",
    defaults: new { controller = "Kategori", action = "kaydet" },
    constraints: new { metod = new HttpMethodConstraint("GET", "POST") }
);
```

#### Custom Constraint oluşturma

- Yeni bir class oluşturup `IRouteConstraint` üzerinden kalıtım alınır.
- Interface implement edilir.
- Kullanılacak değerler : 
    - httpContext : Kullanıcının yaptığı sayfa talebinin bilgilerini tutar.
    - Route : İlgili route tanımının bilgisini tutar.
    - paramaterName : Sınıfın tanımlandığı parametrenin ismini tutar.
    - Values : Route ile gelen parametrelerin değerlerini tutar.
    - routeDirection : Talebin gelen istek mi yoksa url yönlendirmesi mi olduğu bilgisini tutar.
- İlgili kontroller yapıldıktan sonra return olarak true veya false döndürülmelidir. 

```cs
public class KategoriConstraints : IRouteConstraint
{
    public bool Match(HttpContextBase httpContext, Route route, string parameterName, RouteValueDictionary values, RouteDirection routeDirection)
    {
        string kategori1 = values["kategori0"].ToString();
        return (Data.kategoriler.Contains(kategori1)) ? true : false;
    }
}
```

### 03 – Web Api Route Yapılandırması (Attribute Routing)

- Attribute Route ayarlaması kullanılan actionlara, {controller}/{action} üzerinden artık bağlanılınamaz.
- Route Tanımlama
    - Attribute Route ayarlamalarının kullanılması için RouteConfig içine aşağıdaki satırın girilmesi gereklidir:
        - `routes.MapMvcAttributeRoutes();`
    - Actionlar üzerinde attribute olarak gideceği route tanımlanır:
        - `[Route("books/{bookName}/detail")]`
- RoutePrefix
    - Controller üzerinde belirtilir ve her action için kök yol olarak tanımlanır.
    - `[RoutePrefix("books")]`
    - RoutePrefix istenilirse “ ~/ ” işareti ile ezilebilir.

```cs
[RoutePrefix("books")]
public class BookController : Controller
{
    [Route]
    public ActionResult Index()
    {
        ViewBag.gelenSayfa = "1.Sayfa";
        return View();
    }

    [Route("{bookName}")]
    public ActionResult Index(string bookName)
    {
        ViewBag.gelenSayfa = "2.Sayfa";
        ViewBag.bookName = bookName;
        return View();
    }

    [Route("{bookName}/detail")]
    public ActionResult Index(string bookName, string bos)
    {
        ViewBag.gelenSayfa = "Detay Sayfası";
        ViewBag.bookName = bookName;
        return View();
    }
}
```

- Default Route
    - Prefix ile belirtilen yola ulaşıldında default girilecek action tanımlanmasıdır. 

```cs
[RoutePrefix("reviews")]
[Route("{action=Index}")]
public class HomeController : Controller
{
    // örnek: /reviews      
    public ActionResult Index()
    {
        return View();
    }
    // örnek: /reviews/detail
    public ActionResult Detail()
    {
        string title = "Detay Sayfası";
        ViewBag.Title = title;
        return View();
    }
}
```

- Optional URL
    - Parametre yanına ? konularak null değer alması sağlanabilir. 

```cs
[Route("books/{title?}")]
public ActionResult Index(string title)
{
    ViewBag.Title = title;
    return View();
}
```

- Default Değer Atama

```cs
[Route("books/{title=Default Başlık}")]
public ActionResult Index(string title)
{
    ViewBag.Title = title;
    return View();
}
```

- Route Constraint
    - Genel yazım şekli {parameter:constraint}
    - https://docs.microsoft.com/en-us/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints

```cs
[Route("~/books/{id:int:min(1)?}")]
public ActionResult Index(int id)
{
    ViewBag.Title = id;
    return View();
}
```

- Areas
    - Grubu bir area gurubuna atamaya yarar. 

```cs
[RouteArea("library")]
[RoutePrefix("reviews")]
public class HomeController : Controller
{
    // örnek /library/reviews/books/5
    [Route("books/{id:min(2)}")]
    public ActionResult Index(int id)
    {
        ViewBag.Title = id;
        return View();
    }
}
```

```html
Buraya giden link :
<a href="@Url.Action("Detail", "Home" , new { area="library" })">Detay</a>
```