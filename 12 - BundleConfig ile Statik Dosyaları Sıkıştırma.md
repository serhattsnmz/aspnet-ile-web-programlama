## BUNDLECONFIG İLE STATIC DOSYALARI SIKIŞTIRMA

- Kullanmak için indirilmesi gereken paketler
    - Microsoft.AspNet.Web.Optimization
    - WebGrease -> Update edilmeli
- App_Start altında BundleConfig adında bir class oluşturulur.
    - Adı değiştirilebilir, fakat klasik olarak bu isim kullanılır.
- Class içinde bundle kodlarını tutan static bir fonksiyon yazılır.

```cs
public class BundleConfig
{
    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new StyleBundle("~/css/all").Include(
            "~/Content/bootstrap.css",
            "~/Content/bootstrap-theme.css",
            "~/Content/Site.css"));

        bundles.Add(new ScriptBundle("~/js/all").Include(
            "~/Scripts/jquery-3.1.1.js",
            "~/Scripts/bootstrap.js",
            "~/Scripts/modernizr-2.6.2.js"
            ));
    }
}
```

- Oluşturulan fonksiyon Global.asax içine eklenir.
    - `BundleConfig.RegisterBundles(BundleTable.Bundles);`
- Bundle’lar html içine çağırılır.

```js
@using System.Web.Optimization
@Styles.Render("~/css/all")
@Scripts.Render("~/js/all")
```

- BundleConfig optimizasyonu ve sıkıştırma
    - Optimizasyon açıldığında, tüm css ve js dosyaları birleştirilip tek dosya haline getirilir ve dosyalardaki gereksiz kullanımlar silinerek sıkıştırma sağlanır. Bu sayade yüklenme daha hızlı hale gelir.
    - Bundle fonksiyonumuzun en altına : 
        - `BundleTable.EnableOptimizations = true;`
    - Release modda ( Siteyi canlıya aldığımızda release modda alırız ), bu tanımlama yapılmazsa dahi default olarak true alınır. Fakat tanımlama yapılmadığında debug modunda, dosyalar tek tek yüklenmeye devam eder.
- BundleConfig içinde otomatik versiyon alma
    - `{version}` parametresi eklenir.
    - Birden fazla versiyon varsa hepsi eklenir. 
- (*) WildCard kullanımı
    - https://docs.microsoft.com/en-us/aspnet/mvc/overview/performance/bundling-and-minification
- CDN üzerinden bağlantı ekleme
    - CDN üzerinden çağırdığımızda otomatik versiyon alma yapamayız.
    - CDN çağırma sadece release modda geçerli olur. 

```cs
bundles.UseCdn = true;
string jqCdn = "https://code.jquery.com/jquery-2.2.4.js";

bundles.Add(new ScriptBundle("~/js/all", jqCdn).Include(
"~/Scripts/jquery-{version}.js",
```

- Bundle oluşurken Cache’leme
    - Tarayıcının incele kısmından oluşan isteğin Expires Date özelliğine bakma
