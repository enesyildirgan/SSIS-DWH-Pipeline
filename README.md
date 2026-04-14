# 🚀 SSIS-DWH-Pipeline Proje Dokümantasyonu

Bu proje, **AdventureWorks2022** veritabanını kaynak alarak geliştirilmiş, uçtan uca bir ETL (Extract, Transform, Load) mimarisidir. Proje, veri tutarlılığını korumak ve kaynak sistem performansını optimize etmek amacıyla **Staging (Dump) -> ODS -> DWH** hiyerarşisini kullanır.

---

## 🏗️ Veri Akış Mimarisi

Veri transfer süreci üç temel aşamadan oluşmaktadır:

1.  **STG (Staging/Dump) Katmanı:** Kaynak sistemden çekilen ham veriler, herhangi bir dönüşüme uğramadan doğrudan `_Dump` (Staging) tablolarına aktarılır. Bu katman, kaynak veritabanı üzerindeki okuma yükünü minimize etmek için kullanılır.
2.  **ODS (Operational Data Store) Katmanı:** Dump tablolarındaki veriler, `MERGE INTO` komutları kullanılarak ODS katmanına aktarılır. 
3.  **DWH (Data Warehouse) Katmanı:** ODS'den alınan veriler **SCD (Slowly Changing Dimension) Type 2** mantığı ve Surrogate Key atamaları ile nihai Boyut (Dimension) ve Olgu (Fact) tablolarına dönüştürülür.

---

## 🛠️ Veritabanı Kurulumu

Projeyi yerel ortamınızda çalıştırmak için aşağıdaki adımları izleyin:

1.  **SQL Server Management Studio (SSMS)** uygulamasını açın.
2.  `database/database_setup.sql` dosyasını bir query sayfasında açın.
3.  Hedef veritabanınızda (Örn: `AdventureWorks2022_DWH2`) scripti **Execute** ederek tabloları ve stored procedure'leri oluşturun.
4.  SSIS projesi içindeki `Project.params` dosyasından kendi sunucu isminizi (`SERVER_NAME`) ve DB isimlerini güncelleyin.

---

## 📂 Proje İçeriği ve Paketler

### 📦 SSIS Paketleri

#### 1. ODS & Staging Süreçleri
Veriyi önce Dump tablolarına aktarıp ardından ODS katmanına taşıyan paketler:
* `ODS_CUSTOMER.dtsx`
* `ODS_PRODUCT.dtsx`
* `ODS_SALES_HEADER.dtsx`
* `ODS_SALES_DETAIL.dtsx`
* `ODS_SALES_PERSON.dtsx`
* `ODS_TERRITORY.dtsx`

#### 2. DWH Katmanı (Dimension & Fact)
Veriyi ambarı yapısına uygun şekilde, tarihsel izlenebilirlik (SCD) ekleyerek işleyen paketler:
* **Dimension Paketleri (SCD Type 2):** `DWH_DIM_CUSTOMER.dtsx`, `DWH_DIM_PRODUCT.dtsx`, `DWH_DIM_SALES_PERSON.dtsx`, `DWH_DIM_TERRITORY.dtsx`
* **Fact Paketi:** `DWH_FACT_SALES.dtsx` (Merkezi satış olgu tablosu).

#### 3. Ana Kontrol Paketi
* **`MAIN_PACKAGE.dtsx`**: Tüm ETL sürecini (STG -> ODS -> DWH) bağımlılık sırasına göre otomatik olarak tetikleyen ana pakettir.

---

## ⚙️ Teknik Detaylar ve Performans

* **Incremental Load:** Dump tabloları üzerinden yapılan `MERGE` işlemleri sayesinde sadece değişen veya yeni eklenen veriler işlenerek performans maksimize edilmiştir.
* **SCD Type 2:** Boyut tablolarında tarihsel veri takibi sağlanarak analizlerin doğruluğu ve geçmişe dönük raporlama kabiliyeti korunmuştur.
* **Merkezi Yönetim:** `Project.params` dosyası ile tüm paketlerin bağlantı dizeleri ve sunucu bilgileri tek bir noktadan yönetilir.

---
*Bu dokümantasyon, SSIS-DWH-Pipeline projesinin teknik mimarisini ve işleyişini özetlemek amacıyla oluşturulmuştur.*
