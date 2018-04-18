## WEB HELPERS KÜTÜPHANESİ KULLANIMI

### 01 – Kurulum ve Hata Giderme

- İndirilecek paket : 
    - Microsoft.AspNet.WebHelpers
    - Microsoft.AspNet.WebPages.Data -> Update
- View içerisinde web helper kullanılacaksa using olarak eklenmesi gerekmektedir.
    - `@using System.Web.Helpers`

### 02 – AntiForgery ile Güvenlik

- Bir formu taklit eden başka bir form sayfası yapılıp buradan istek gönderilebilir. Bunu engellemek için AntiForgeryToken kullanılır.
- Gönderilecek form içerisine : 
    - `@Html.AntiForgeryToken()`
- Formu alan action üstüne attribute olarak : 
    - `[ValidateAntiForgeryToken]`

### 03 – Chart (Grafik) Oluşturma

- Chart yöntemi ile grafiklerimizi resim formatında oluşturabiliriz.
    - Chart controller içinde oluşturulur ve model olarak view’a gönderilir.
    - View içinde sadece model olarak alınır ve yazdırma işlemi yapılır.
    - Chart kullanılacak sayfada resim olarak eklenir ve adres kısmına link olarak verilir.
- Aşamalar :
    - Yeni Chart oluşturma -> Genişlik, Uzunluk, Tema
    - Chart içine yeni bir alan ekleme
        - Chart Types :
            - Column – Line – Pie – Doughnut
    - Chart içine db üzerinden alan ekleme
    - Başlık ekleme
    - Legend ekleme
    - Chart yazdırma
    - Chart Kaydetme -> Resim – XML
    - Cache üzerinde kayıt yapma ve cache kontrolü
        - SlidingExpiration – Kaydırmalı zaman kullanma. True işaretlenirse, verilen süre içinde cache bilgisine her ulaşıldığında süre sıfırlanır.
        - Break Point kullanarak yeniden oluşturup oluşturulmadığına bakma
- Notlar : 
    - Eklerken en son eklenen alan en üstte gösterir.
    - Line ve Doughnut grafikleri yapılacaksa sadece bir alan bulunması gereklidir.
- Linkler : 
    - https://msdn.microsoft.com/en-us/library/system.web.helpers.chart(v=vs.111).aspx
    - https://docs.microsoft.com/en-us/aspnet/web-pages/overview/data/7-displaying-data-in-a-chart

```cs
public ActionResult CreateChart()
{
    // Cache üzerinden çekim kontrolü - yoksa null döner
    Chart chart = Chart.GetFromCache("chart1"); 

    if (chart == null)
    {
        chart = new Chart(700, 500, ChartTheme.Yellow);

        chart.AddSeries(    // Yeni bir alan ekleme
            name: "Acer",
            chartType: "Column",
            xValue: new[] { "Ocak", "Şubat", "Mart" },
            yValues: new[] { 10, 20, 30 }
            );

        chart.AddSeries(    // DB verilerini ekleme
            name: "Asus",
            chartType: "Column",
            xValue: VeriListesi.liste, xField: "Ay",
            yValues: VeriListesi.liste, yFields: "Fiyat"
            );

        chart.AddTitle("Fiyat Listesi"); // Grafik Başlığı
        chart.AddLegend("Markalar"); // Bilgilendirme başlığı
        chart.SaveToCache("chart1", 10, true); // Verilen isim ile cache'e kayıt etme

        // Grafik Kaydetme
        string dir = Server.MapPath("~/charts/");

        string imagePath = dir + "chart1.jpeg";
        string xmlPath = dir + "chart1.xml";

        chart.Save(imagePath, "jpeg");
        chart.SaveXml(xmlPath);
    }

    return View(chart);
}
private class Veri
{
    public string Ay { get; set; }
    public double Fiyat { get; set; }
}

private class VeriListesi
{
    public static List<Veri> liste = new List<Veri>()
    {
        new Veri() { Ay = "Ocak", Fiyat = 30 },
        new Veri() { Ay = "Şubat", Fiyat = 40 },
        new Veri() { Ay = "Mart", Fiyat = 40 }
    };
}
```

### 04 – Crypto İle Şifreleme

- Database üzerinde tutulacak parolalar, şifrelenerek saklanmalıdır. Aksi takdirde database kötü niyetli birinin eline geçtiği zaman tüm şifreler ele geçmiş olur.
- Hash – SHA1 – SHA256 her seferinde aynı şifreyi oluşturur.
- HashPassword her seferinde farklı şifreyi oluşturur. Bu yüzden kendi metodu ile doğrulanması gerekmektedir.
- https://msdn.microsoft.com/en-us/library/system.web.helpers.crypto_methods(v=vs.111).aspx

```cs
string sifresiz = "Serhat Sönmez";
string sifreli = "ALBVOdRNckFnK3psmpvqlWtWZVP2S0g7Cl8qfQ4lwdZUSHkbQDJcOXGptCcaobcHfg==";

string salt = Crypto.GenerateSalt();

string hash = Crypto.Hash(sifresiz);
string sha1 = Crypto.SHA1(sifresiz);
string sha256 = Crypto.SHA256(sifresiz);

string hPass = Crypto.HashPassword(sifresiz);
bool ctrl = Crypto.VerifyHashedPassword(sifreli, sifresiz);
```

### 05 – WebImage ile Resim İşlemleri
- İşlem sonucunda çıktı resim dosyasıdır. Bu nedenle img tagları arasında link olarak belirtilmesi gerekmektedir.

```cs
public void Process(string p)
{
    string imagePath = Server.MapPath("~/pic/pic.jpg");
    WebImage image = new WebImage(imagePath);

    // Rotasyon işlemleri
    image.RotateLeft();
    image.RotateRight();
    image.FlipHorizontal();
    image.FlipVertical();

    // Resize
    image.Resize(
        width : 500,
        height : 500,
        preserveAspectRatio : true, // En Boy oranını koru
        preventEnlarge : false // Büyültmeyi engelle
        );

    // Resim üzerine yazı ekleme
    image.AddTextWatermark(
        text: "Serhat Sönmez",
        fontColor: "#fff",
        fontSize: 12,
        fontStyle: "Regular",
        fontFamily: "Microsoft Sans Serif",
        horizontalAlign: "Center",
        verticalAlign: "Bottom",
        opacity: 100,
        padding: 0
        );

    // Resim üzerine resim ekleme
    WebImage watermark = new WebImage(imagePath);
    watermark.Resize(50, 50);
    image.AddImageWatermark(
        watermarkImage: watermark, // WebImage veya path
        width: 0,   // 0 belirtilirse otomatik alır
        height: 0, // 0 belirtilirse otomatik alır
        horizontalAlign: "Right",
        verticalAlign: "Top",
        opacity: 50,
        padding: 0
        );

    // Resim kesme
    image.Crop(50, 50, 50, 50);

    // Resim kaydetme
    string savePath = Server.MapPath("~/pic/last.jpg");
    image.Save(savePath, "jpg");

    // Resim yazdırma
    image.Write();
}
```

### 06 – WebCache ile Performans İşlemleri

- Normal yaptığımız cache işlemlerini daha kolay hale getirir.
- Hatırlatma : Cache object türünden nesne tutar.
- https://msdn.microsoft.com/en-us/library/system.web.helpers.webcache(v=vs.111).aspx

```cs
// Ekleme
WebCache.Set(
    key : "zaman",
    value: DateTime.Now.ToString(),
    minutesToCache: 1,
    slidingExpiration: false
    );

// Çekme
object data = WebCache.Get("zaman");

// Silme
WebCache.Remove("zaman");
```

### 07 – WebMail ile E-Posta Hizmetleri
- Notlar:
    - Eklenecek dosyalar null olamaz.
    - Eklenecek dosyanın server üzerinde bulunması gereklidir. Upload edilen dosya önce kaydedilmeli sonra eklenmelidir. 
    - From kısmı null olursa noreply@localhos olarak gider.

```cs
private bool MailGonder(string file = null)
{
    bool sonuc = false;

    WebMail.SmtpServer = "mail.serhatsonmez.net";
    WebMail.SmtpPort = 587;
    WebMail.UserName = "deneme@serhatsonmez.net";
    WebMail.Password = "serhat123";
    WebMail.From = "deneme@serhatsonmez.net";
    WebMail.EnableSsl = false;

    try
    {
        WebMail.Send(
            to: "iletisim@serhatsonmez.net",
            subject: "Deneme Mail",
            body: "<b>Bu bir deneme postasıdır.</b>",
            from: null,
            cc: null,
            filesToAttach: (String.IsNullOrEmpty(file)) ? null : new[] { file },
            isBodyHtml: true,
            additionalHeaders: null,
            bcc: null,
            contentEncoding: null,
            headerEncoding: null,
            priority: "High",
            replyTo: null
            );
        sonuc = true;
    }
    catch (Exception)
    {
        sonuc = false;
    }
    return sonuc;
}
```

### 08 – WebGrid İle Hazır Grid Sistemleri
- http://www.ugurkizmaz.com/YazilimMakale-1849-Asp-Net-MVC-WebGrid-Kullanimi-ve-Ornek-Uygulama.aspx