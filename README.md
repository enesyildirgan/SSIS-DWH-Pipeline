

# SSIS-DWH-Pipeline Proje Dokümantasyonu



Bu proje, **AdventureWorks2022** veritabanını kaynak alarak geliştirilmiş, uçtan uca bir ETL (Extract, Transform, Load) mimarisidir. Proje, veri tutarlılığını korumak ve kaynak sistem performansını optimize etmek amacıyla **Staging (Dump) -> ODS -> DWH** hiyerarşisini kullanır.





## 🏗️ Veri Akış Mimarisi



Veri transfer süreci üç temel aşamadan oluşmaktadır:



1.  **STG (Staging/Dump) Katmanı:** Kaynak sistemden çekilen veriler, herhangi bir dönüşüme uğramadan doğrudan `STAGING` tablolarına aktarılır. 

2.  **ODS (Operational Data Store) Katmanı:** Dump tablolarındaki veriler, `MERGE INTO` komutları kullanılarak ODS katmanına senkronize edilir. Bu aşamada temel veri temizliği ve tip dönüşümleri gerçekleştirilir.

3.  **DWH (Data Warehouse) Katmanı:** ODS'den alınan veriler; iş kuralları, SCD (Slowly Changing Dimension) mantığı ve Surrogate Key atamaları ile nihai Boyut (Dimension) ve Olgu (Fact) tablolarına dönüştürülür.




## 🛠️ Veritabanı Kurulumu
Projeyi çalıştırmadan önce veritabanı yapısını oluşturmak için:
1. SQL Server Management Studio'yu açın.
2. `database/database_setup.sql` dosyasını açın.
3. Hedef veritabanınızda (örn: `AdventureWorks2022_DWH2`) scripti **Execute** ederek tabloları ve procedure'leri oluşturun.

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














































































