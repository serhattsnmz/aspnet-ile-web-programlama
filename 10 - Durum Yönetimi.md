## DURUM YÖNETİMİ

### 01 – Session Kullanımı

- Session nedir?
    - Sunucunun REMinde tutulur.
    - Her kullanıcı için bir ID oluşturur ve tutulacak verileri bu ID üzerinden saklar.
    - Bu oluşturulan ID’ler kullanıcının bilgisayarında cookie olarak tutulur.
    - Farklı tarayıcılarda farklı kullanıcı gibi davranır.
    - Tarayıcı kapandığında silinir.
    - Belirli bir süresi vardır, ayarlanabilir. Default : 20dk
    - Sessionlar obje tutar.
- Session oluşturma
    - Session[“key”] = value;
    - Session.Add( string key, object value )
- Session null geldiğinde ToString() çevirme sorunu – null referance
    - Session null kontrolü
- Session silme
    - Session.Remove( string “key” )
    - Session.Clear() -> tüm değerleri silme
    - Session’ı null değere çekme
- Session süresi ayarlama
    - Web.config dosyasında <system.web> altına
        - `<sessionState timeout=”30”></sessionState>`
        - Dk olarak session süresini ayarlama
    - Hosting firmalarının session süresine müdahale etmesi ve manuel düzenlemeye izin vermemesi durumlarına dikkat edilmesi lazım.
    - Session verileri belli aralıklarla çağırılarak session canlı tutulabilir.

### 02 – ApplicationState Kullanımı

- Her kullanıcı aynı anahtar değeriyle aynı value değerine ulaşır.
- Session gibi kullanıcıya spesifik değildir.
- Süresi, server yenilenmediği süre boyunca geçerlidir.
- Oluşturma : 
    - HttpContext.Application[ string key ] = value;
    - HttpContext.Application.Add( string key, object value );
- Basit bir sayaç yapımı
- Silme
    - HttpContext.Application.Remove( string key )
    - HttpContext.Application.Clear()
    - HttpContext.Application.RemoveAll()
    - Null değere çekme
- Null kontrolü
- **NOT:** Bu yöntem, veriyi statik bir değişken üzerinde saklamaya benzer. ApplicationState ile yaptığımızı, statik bir değişken tanımlayarak da yapabiliriz.

### 03 – Cache Kullanımı

- Caching mekanizması, herhangi bir veriyi cevap olarak daha hızlı döndürmek için, bu veriyi RAM üzerinde saklama ve gerektiğinde geri döndürme işlemidir.
- Globaldir. Her kullanıcı aynı bilgileri görür.
- ApplicationState’ten farkı, burada süre belirtebiliyoruz.
- İsteğimiz dışımızda, server alan açmaya zorlandığında yine bu cache’ler silinebilir.
    - Bu silinmeler önceliğe göre yapılır.
- Oluşturma : 
    - HttpContext.Cache[ string key ] = value;
        - Bu şekilde oluşturulursa süresiz şekilde kullanılabilr olur.
    - HttpContext.Cache.Add( aşağıdaki_degerler )
        - string key, 
        - object value, 
        - CacheDependency ihtiyac_halinde_bağlanabilecek_dosyalar, 
        - DateTime silinmesini_istediğimiz_kesin_tarih, 
        - TimeSpan şu_zamandan_sonra_sil
        - CacheItemPriority oluşturulan_cache_nesnesinin_önceliği
        - CacheItemRemoveCallback cache_silindiğinde_çağırılacak_methot
    - AbsoluteExpiration kullanılacaksa,
        - SlidingExpiration -> System.Web.Caching.Cache.NoSlidingExpiration
    - SlidingExpiration kullanılacaksa,
        - AbsoluteExpiration -> System.Web.Caching.Cache.NoAbsoluteExpiration
        - New TimeSpan( saat, dk, sn )
        - Belirtilen süreden önce erişilirse tekrardan süre sıfırlanır.
    - Callback kullanımı : 

```cs
public ActionResult Check()
{
    HttpContext.Cache.Add(
        "Check", "True", null, Cache.NoAbsoluteExpiration,
        new TimeSpan(1,0,0), CacheItemPriority.Normal, CacheCallback
        );
    return Json(data: true);
}

private void CacheCallback(string key, object value, CacheItemRemovedReason reason)
{
    System.IO.File.WriteAllText(
        Server.MapPath("~/log.txt"),
        $"<{key}> key ve <{value}> yapısına sahip cache silindi. Neden: {reason}"
        );
}
```

- Silme : 
    - HttpContext.Cache.Remove [ string key ];
- Kullanımı : 
    - Html kısmında kullanılacaksa : 
        - HttpContext.Current.Cache[ string key ]
    - Controller içinde kullanılacaksa : 
        - HttpContext.Cache[ string key ] 

### 04 – Cookie Kullanımı

- Kullanıcının bilgisayarında tutulur.
- Kullanıcıya tanımlıdır.
- Cookie obje değil string bir değişken tutar. 
- Tarayıcı kapandığında silinmez. Belirli bir ömür belirtilmesi lazım.
- Her request ve response üzerinde taşınır.
    - Bu taşınmanın performansının düşmemesi için cookie boyutunu minimum tutmak önemlidir.
    - Genellikle kullanıcıya özel bilgiler server üzerinde barındırılıp, bunu tanımlayan unique bir değer cookie ile kullanıcıya gönderilir.
- Çoğu browser, cookie boyutunu 4096 byte olarak sınırlandırmıştır.
- Ekleme için kullanıcıya yanıt ( response ) gönderilir. Okuma için kullanıcıya istek ( request ) gönderilir.
- Oluşturma : 
    - HttpCookie cookie = new HttpCookie ( string key, string value )
    - HttpContext.Response.Cookies.Add( cookie_name )
        - Bu komut cookie’yi kullanıcın bilgisayarına gönderir.
        - Bu komut çalıştırılmazsa, cookie kullanıcın bilgisayarında oluşmaz.
- Kullanma : 
    - HttpContext.Request.Cookies[ string key ].Value;
- Null kontrolü
    - HttpContext.Request.Cookies[ string key ] != null; 
- Silme :
    - HttpContext.Request.Cookies.Remove( string key ) -> Genellikle silme başarısız.
    - HttpContext.Response.Cookies[string key].Expires = Datetime.Now;
- Ömrünü belirtmek :
    - Cookie oluşturulduktan sonra Expires özelliğine DateTime ekleme
        - Cookie.Expries = DateTime.Now.AddDays(1);
