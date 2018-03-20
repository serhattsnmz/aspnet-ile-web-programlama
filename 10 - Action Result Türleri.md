## ACTION RESULT TÜRLERİ

- Tüm Result türleri `ActionResult`’tan türemiştir.
- Bu nedenle `ActionResult` altında tüm result türlerini return edebiliriz.
- Bir action içinde birden fazla farklı dönüş tipleri olabileceği için, genel olarak `IActionResult` kullanmak daha mantıklıdır.

### ViewResult - View()
- Tanımlı bir View içeriğini render edip döndürmeyi sağlar.
- Metot boş kullanılırsa, Controller ile aynı ismi taşıyan klasör içindeki Action ile aynı ismi taşıyan cshtml belgesini döndürür. 
- Farklı bir view döndürülmek isteniyorsa, metot içine ismi yazılabilir.

### PartialViewResult - PartialView()
- Tanımlanmış bir partial görünümünün render edilip döndürülmesini sağlar.
- Partial, Controller ile aynı isimli klasör içinde veya `Shared` klasörü içindeyse, sadece ismi girilerek partial döndürülebilir. Aksi durumda tam yol girilmesi gereklidir.
- Partial tek başına döndürülebileceği gibi, bir model ile birlikte render edilerek döndürülebilir.

### ContentResult - Content()
- Kullanıcı tarafına view kullanmadan içerik döndürür.
- String parametre alır.

### RedirectResult - Redirect()
- Url yönlendirmesi yapar
- Dış siteye bağlantı verilecekse `http://` kullanılması unutulmamalıdır.
- `Redirect` => 302 döndürür.
- `RedirectPermanent` => 301 döndürür.

### RedirectToRouteResult - RedirectToAction()
- Farklı bir action metoduna yönlendirme yapar.
- Sadece action ismi verilirse, aynı controller üzerinde bu action'ı arar.
- Farklı bir controller üzerindeki action'a yönlendirme yapılabilir.
- Yönlendirme yapılırken bilgi taşınacaksa, `routeValues` parametresi kullanılabilir.

### JsonResult - Json()
- Verilen bir objeyi serialize edip Json formatına dönüştürür ve döndürür.
- Özellikle Ajax işlemlerinde kullanılır.
- İkinci parametre olarak Json sayfasına GET metoduyla erişim yönetimi girilebilir.
    - Default olarak bu parametre `DenyGet`tir.

### JavaScriptResult - JavaScript()
- Kullanıcı tarafına çalıştırılabilir javascript kodlarını döndürür.
- JavaScript döndüren action'ın html içinde kullanımı şu şekildedir:
    - `<script src="~/Home/JS"></script>`

### FileResult - File()
- Dosya döndürmek için kullanılır.
- Dosya şu türlerde verilebilir:
    - Virtual Path (`VirtualFileResult`)
    - Byte dizisi (`FileContentResult`)
    - Stream nesnesi (`FileStreamResult`)
- Burada dosya türü olarak `MimeType` verilmelidir.
    - Bu bilgi raw response'un headers kısmında iletilir.
    - Bu bilgi sayesinde tarayıcı gelen dosyanın türünü anlar ve ona göre işlem yapar.

### HttpStatusCodeResult
- `new HttpStatusCodeResult()`
    -`int` türünde verilen durum kodunu döndürür.
- `HttpNotFound()` - (`HttpNotFoundResult`)
    - Aranılan içeriğin bulunmadığını belirten sayfayı döndürür.
    - Durum kodu : 404
- `new HttpUnauthorizedResult()` - (`HttpUnauthorizedResult`)
    - Yetkisizi işlem yaptığınıa dair bir durum kodu ve mesajı döndürür.
    - Durum kodu : 401