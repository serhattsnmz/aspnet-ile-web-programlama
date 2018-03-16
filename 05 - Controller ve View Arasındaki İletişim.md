## CONTROLER VE VİEW ARASI İLETİŞİM

### 1) ViewData, ViewBag, TempData Kullanımı
- Aralarındaki farklar ve taşıma ömürleri

### 2) View’dan Controller’a Veri Gönderme
- GET ve POST Yöntemleri
    - Bu metotların verileri taşıma yolları
    - Raw Request içindeki yerleri
- GET ve POST ile gönderilen veriler, action üzerinden nasıl alınır.

```html
<!-- HTML PART -->
<form method="post" action="/Home/GetInfo">
    <input type="text" name="name" /> <br />
    <input type="text" name="surname" /> <br />
    <input type="text" name="age" /> <br />
    <input type="submit" value="Send" />
</form>
``` 

```cs
// Metot 01
[HttpPost]
public ActionResult GetInfo()
{
    string name = Request.Form["name"];
    string surname = Request.Form["surname"];
    int age = int.Parse(Request.Form["age"]);
    return RedirectToAction("Index");
}
```

```cs
// Metot 02
[HttpPost]
public ActionResult GetInfo(string name, string surname, int age)
{
    return RedirectToAction("Index");
}
```

- If( IsPost ) Kullanımı
- Using ( Html.BeginForm ) Kullanımı
- Başka bir controller'a veri gönderme yöntemi