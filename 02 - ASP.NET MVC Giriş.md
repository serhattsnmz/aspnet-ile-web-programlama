## ASP.NET MVC GİRİŞ

### 1) MVC Nedir? ASP.NET MVC Nedir?
MVC;  
- Design Pattern
- Kurallar düzenidir / Kalıptır.
- Belirli kurallar ve düzenleri sabitleyerek projenin işleyişini ve - takım çalışmasını kolaylaştırır.
- İlk olarak 1979 yılında Trygve Reenskaug ortaya atılmıştır. - (Microsoft’un kurulumu 1975)

ASP.NET MVC;
- 2009 yılında Microsoft Asp ile MVC mimarisini birleştirerek piyasayaa sürmüştür.

## 2) MVC Nasıl Çalışır?
- Model – View – Controller
- Resim-02
- ASP.NET Web Form ile ASP.NET MVC arasındaki fark
    - Web Form
        - Hızlı proje geliştirme
        - Eventler, daha az kod
        - Masaüstü kod geliştirme yapısına benzer yapı
        - Belirli bir düzen yok.
        - Projenin derinine müdahale edilemiyor.
    - ASP.NET MVC
        - Projenin tamamına hakim olabilme
        - Belirli bir düzen var
        - Temiz kod yapısı
        - Projede birden fazla kişinin çalışmasına uygun
        - Süreç nisbeten daha uzun, daha fazla kod

### 3) Yeni Bir Proje Oluşturma ve Controller View Yapısı
- Yeni bir proje nasıl oluşturulur?
- Oluşturulan yeni projedeki klasör yapısı
- Controller ve View arasındaki ilişki, klasör yapısı arasındaki ilişki
- url / { controller } / { action(methot) } düzeni.
- Hatalar : 
    - Action yoksa -> 404 hatası / Browser üretir
    - View yoksa -> View bulunamadı hatası / Yazılım üretir
- Birden fazla sayfaya giden basit bir proje
- GET ve POST anlamları