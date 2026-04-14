

# SSIS-DWH-Pipeline Proje Dokümantasyonu



Bu proje, **AdventureWorks2022** veritabanını kaynak alarak geliştirilmiş, uçtan uca bir ETL (Extract, Transform, Load) mimarisidir. Proje, veri tutarlılığını korumak ve kaynak sistem performansını optimize etmek amacıyla **Staging (Dump) -> ODS -> DWH** hiyerarşisini kullanır.





## 🏗️ Veri Akış Mimarisi



Veri transfer süreci üç temel aşamadan oluşmaktadır:



1.  **STG (Staging/Dump) Katmanı:** Kaynak sistemden çekilen veriler, herhangi bir dönüşüme uğramadan doğrudan `STAGING` tablolarına aktarılır. 

2.  **ODS (Operational Data Store) Katmanı:** Dump tablolarındaki veriler, `MERGE INTO` komutları kullanılarak ODS katmanına senkronize edilir. Bu aşamada temel veri temizliği ve tip dönüşümleri gerçekleştirilir.

3.  **DWH (Data Warehouse) Katmanı:** ODS'den alınan veriler; iş kuralları, SCD (Slowly Changing Dimension) mantığı ve Surrogate Key atamaları ile nihai Boyut (Dimension) ve Olgu (Fact) tablolarına dönüştürülür.



## Veri Kaynağı
*OLTP `AdventureWorks2022.bak`
*DWH  `AdventureWorks2022_DWH2.bak`


## 📂 Proje İçeriği ve Paketler


### 📦 SSIS Paketleri



#### 1. ODS & Staging Süreçleri

Aşağıdaki paketler veriyi önce `Dump` tablolarına (Staging) aktarır, ardından ODS tablolarına `MERGE` eder:

* `ODS_CUSTOMER.dtsx`

* `ODS_PRODUCT.dtsx`

* `ODS_SALES_HEADER.dtsx`

* `ODS_SALES_DETAIL.dtsx`

* `ODS_SALES_PERSON.dtsx`

* `ODS_TERRITORY.dtsx`



#### 2. DWH Katmanı (Dimension & Fact)

Veriyi ambar yapısına uygun şekilde işleyen paketler:

* `DWH_DIM_CUSTOMER.dtsx`: Geçmiş verileri korumak adına **SCD Type 2** mantığı uygulanmıştır.

* `DWH_DIM_PRODUCT.dtsx`: Geçmiş verileri korumak adına **SCD Type 2** mantığı uygulanmıştır.

* `DWH_DIM_SALES_PERSON.dtsx` : Geçmiş verileri korumak adına **SCD Type 2** mantığı uygulanmıştır.

* `DWH_DIM_TERRITORY.dtsx`: Geçmiş verileri korumak adına **SCD Type 2** mantığı uygulanmıştır.

* `DWH_FACT_SALES.dtsx`: Satış verilerini içeren merkezi olgu tablosu.



#### 3. Ana Paket

* **`MAIN_PACKAGE.dtsx`**: Tüm ETL sürecini (Dump -> ODS -> DWH) belirli bir hiyerarşi ve bağımlılık sırasına göre tetikleyen ana pakettir.



## 🛠️ Teknik Detaylar ve Performans

* **Incremental Load:** Dump tabloları üzerinden yapılan MERGE işlemleri sayesinde sadece değişen veya yeni eklenen veriler işlenerek performans artışı sağlanmıştır.

* **Parametre Yönetimi:** `Project.params` dosyası ile bağlantı dizeleri ve çalışma parametreleri merkezi olarak yönetilir.



---

*Bu dokümantasyon, SSIS-DWH-Pipeline projesinin teknik mimarisini ve işleyişini özetlemek amacıyla oluşturulmuştur.*











































































# SSIS-DWH-Pipeline Proje Dokümantasyonu



Bu proje, **AdventureWorks2022** veritabanını kaynak alarak uçtan uca bir ETL (Extract, Transform, Load) süreci yönetmek amacıyla geliştirilmiştir. Veriler önce ODS (Operational Data Store) katmanına alınmakta, ardından DWH (Data Warehouse) katmanındaki Boyut (Dimension) ve Olgu (Fact) tablolarına aktarılmaktadır.



## 📁 Proje Yapısı ve Dosyalar



Görsellerdeki hiyerarşiye göre proje bileşenleri şunlardır:



### ⚙️ Yapılandırma

- **Project.params:** Proje genelinde kullanılan parametreler (Bağlantı cümleleri vb.).

- **Connection Managers:** Veritabanı bağlantılarının merkezi yönetimi.



### 📦 SSIS Paketleri (ETL Akışı)



#### 1. ODS Katmanı (Staging)

Ham verilerin kaynak sistemden (AdventureWorks) çekilerek ODS şemasına aktarıldığı paketler:

- `ODS_CUSTOMER.dtsx`

- `ODS_PRODUCT.dtsx`

- `ODS_SALES_HEADER.dtsx`

- `ODS_SALES_DETAIL.dtsx`

- `ODS_SALES_PERSON.dtsx`

- `ODS_TERRITORY.dtsx`



#### 2. DWH Katmanı (Data Warehouse)

İş kurallarının uygulandığı ve verilerin analize hazır hale getirildiği paketler:

- `DWH_DIM_CUSTOMER.dtsx`: Müşteri boyut tablosu.

- `DWH_DIM_PRODUCT.dtsx`: Ürün boyut tablosu.

- `DWH_DIM_SALES_PERSON.dtsx`: Satış personeli boyut tablosu.

- `DWH_DIM_TERRITORY.dtsx`: Bölge boyut tablosu (SCD Type 2 uygulanmıştır).

- `DWH_FACT_SALES.dtsx`: Satışların tutulduğu merkezi olgu tablosu.



#### 3. Ana Paket

- `MAIN_PACKAGE.dtsx`: Tüm akışı sırasıyla yöneten ana paket.





## 🚀 Deployment (Dağıtım) Adımları



Projenin yayına alınması için aşağıdaki dosya yapısı kullanılır:

1. **Build İşlemi:** Visual Studio üzerinden `Build` yapıldığında `bin` klasöründe `.ispac` dosyası oluşur.

2. **Deploy:** Oluşan `.ispac` dosyası SQL Server Management Studio (SSMS) üzerinden `SSISDB` kataloğuna yüklenir.



---



## 🛠️ Geliştirme Notları

- **SCD (Slowly Changing Dimension):** Territory boyutunda geçmiş verilerin saklanması için Type 2 mantığı kurgulanmıştır.

- **Performans:** Büyük veri setleri için dump/staging tabloları üzerinden MERGE işlemleri optimize edilmiştir.





