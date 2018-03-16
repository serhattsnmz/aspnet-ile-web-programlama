## VİEWS

### 1) Views ve Layout Kullanımı
- Layout page oluşturma
- RenderBody kısmı
- Basit bir layout sayfasıyla, 3 sayfalık bir web sitesi oluşturma

### 2) ViewStart Ne İşe Yarar
- Views klasörü altına _ViewStart.cshtml tanımlama
- ViewStart için layout tanımlama
- Html sayfalarında layout tanımını kaldırma

### 3) Otomatik Layout Sayfası Oluşturma
- Yeni bir view oluştururken “Use Layout Page” tanımını;
    - Belirtme
    - Boş bırakma
    - Tick kaldırma

### 4) Nested (İç İçe) Layout Oluşturma
- View Page with Layout oluşturma

### 5) Section ( Bölüm ) Oluşturma
- RenderSection – True/False

### 6) Partial View Kullanımı
- Yeni bir Partial View oluşturma
- @Html.Partial(“yol”)
- @{ Html.RenderPartial(“yol”) }
- Yol Belirtme
    - Shared içinde veya kendi klasörünün altında direk isim yazarak çağırılabilir.
    - Diğer klasörlerin altında ise tam yol belirtilmesi zorunludur.
- Partial çekilirken, partial’a bir model gönderilerek de çekilebilir.