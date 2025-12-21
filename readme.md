# 🫀 Kalp Hastalığı Risk Tahmini (Heart Failure Prediction)

Bu proje, hastaların klinik ve fiziksel verilerini kullanarak kalp yetmezliği veya kalp hastalığı riskini tahmin eden bir uçtan uca makine öğrenmesi (End-to-End Machine Learning) çalışmasıdır.

## 📌 Proje Özeti
Kalp damar hastalıkları, dünya genelinde ölümlerin bir numaralı sebebidir. Bu projede, **918 hastaya ait** 11 farklı klinik özelliği içeren kapsamlı bir veri seti kullanılarak, erken teşhis için yüksek doğruluklu bir tahmin modeli geliştirilmiştir.

Çalışma sırasında **Veri Temizleme (Data Cleaning)**, **Keşifçi Veri Analizi (EDA)**, **Özellik Mühendisliği (Feature Engineering)** ve **Model Karşılaştırma** teknikleri uygulanmıştır.

## 📂 Veri Seti Hakkında
Kullanılan veri seti, literatürdeki en kapsamlı kalp hastalığı veri setlerinden biridir (UCI & Kaggle). 5 farklı kalp veri setinin birleştirilmesiyle oluşturulmuştur.

**Boyut:** 918 Satır, 12 Sütun (11 Özellik + 1 Hedef)

| Özellik Adı (TR) | Orijinal Ad | Açıklama |
| :--- | :--- | :--- |
| **Yaş** | `Age` | Hastanın yaşı |
| **Cinsiyet** | `Sex` | E: Erkek, K: Kadın |
| **Göğüs Ağrısı Tipi** | `ChestPainType` | TA: Tipik Anjin, ATA: Atipik, NAP: Anjin Olmayan, ASY: Asemptomatik |
| **Dinlenme Tansiyonu** | `RestingBP` | mm Hg cinsinden kan basıncı |
| **Kolesterol** | `Cholesterol` | mm/dl cinsinden serum kolesterolü |
| **Açlık Kan Şekeri** | `FastingBS` | 1: > 120 mg/dl (Diyabet Riski), 0: Normal |
| **EKG Sonucu** | `RestingECG` | Normal, ST (Anomali), LVH (Hipertrofi) |
| **Max Kalp Hızı** | `MaxHR` | Ulaşılan maksimum nabız |
| **Egzersiz Anjini** | `ExerciseAngina` | Egzersizle gelen ağrı (Var/Yok) |
| **ST Depresyonu** | `Oldpeak` | Egzersiz sonrası EKG çökme değeri |
| **ST Eğimi** | `ST_Slope` | Up (Yukarı), Flat (Düz), Down (Aşağı) |
| **Kalp Hastalığı** | `HeartDisease` | **HEDEF:** 1 (Hasta), 0 (Sağlıklı) |

## 🛠️ Uygulanan Metodoloji (Pipeline)

Proje aşağıdaki 6 ana aşamadan oluşmaktadır:

### 1. Veri Temizliği (Data Cleaning) 🧹
* **Hatalı Veri Tespiti:** Kolesterol sütununda tıbben imkansız olan **"0"** değerleri tespit edildi.
* **Imputation (Doldurma):** Bu 0 değerleri, hastanın `Kalp_Hastaligi` sınıfına (Hasta/Sağlıklı) göre o grubun **medyan** değeri ile doldurularak veri kaybı önlendi ve gürültü temizlendi.

### 2. Keşifçi Veri Analizi (EDA) 📊
* Histogramlar ve Kutu Grafikleri (Boxplot) ile dağılımlar incelendi.
* Korelasyon matrisi (Heatmap) ile özellikler arası ilişkiler haritalandı.
* *Bulgu:* `ST_Egimi_Flat` ve `Egzersiz_Anjini` özelliklerinin hastalıkla çok güçlü pozitif ilişkisi olduğu, `ST_Egimi_Up` özelliğinin ise güçlü bir koruyucu faktör olduğu görüldü.

### 3. Özellik Mühendisliği (Feature Engineering) 🧬
Model performansını artırmak için tıbbi literatüre dayalı 3 yeni özellik türetildi:
* **Nabız Oranı:** `Max_Kalp_Hizi / (220 - Yas)` (Kalbin zorlanma oranı)
* **Kalp İş Yükü (Rate Pressure Product):** `Max_Kalp_Hizi * Tansiyon`
* **Yaş & Kolesterol Etkisi:** `Yas * Kolesterol` (Yaşlandıkça kolesterolün riski artırması)

### 4. Aykırı Değer Analizi (Outlier Handling) 📉
* Kolesterol ve Tansiyon gibi verilerde aşırı uç değerler (Outliers) tespit edildi.
* **Winsorization (Baskılama):** Verinin %5 altı ve %95 üstü değerleri sınırlara çekilerek gürültü azaltıldı ve modelin stabilitesi artırıldı.

### 5. Ön İşleme (Preprocessing) ⚙️
* **One-Hot Encoding:** Kategorik veriler (Cinsiyet, Ağrı Tipi vb.) sayısal matrislere dönüştürüldü. `drop_first=True` kullanılarak "Dummy Variable Trap" önlendi.
* **Scaling (StandardScaler):** Tüm sayısal veriler (özellikle türetilen büyük sayılar) aynı ölçeğe getirilerek modelin büyük sayılardan (örn: 15.000) yanılması engellendi.

### 6. Modelleme (Modeling) 🤖
Veri seti **%80 Eğitim**, **%20 Test** olarak ayrıldı (Stratified Split). Aşağıdaki algoritmalar karşılaştırıldı:
* Random Forest Classifier
* XGBoost (Extreme Gradient Boosting)
* Support Vector Machines (SVM)

## 🏆 Sonuçlar

Yapılan testler sonucunda en iyi performansı **Random Forest** modeli göstermiştir.

| Model | Doğruluk (Accuracy) |
| :--- | :--- |
| **Random Forest** | **%88.58** 🥇 |
| XGBoost | %87.20 |
| SVM | %86.40 |

### Modelin En Çok Güvendiği Özellikler (Feature Importance):
Random Forest modeline göre hastalığı belirleyen en kritik faktörler:
1.  **ST Eğimi (Yukarı):** En güçlü koruyucu faktör.
2.  **ST Eğimi (Düz):** En güçlü risk faktörü.
3.  **ST Depresyonu:** Kalp hasarı göstergesi.
4.  **Maksimum Kalp Hızı:** Fiziksel kondisyon göstergesi.
5.  **Kolesterol:** (Yapılan temizlik ve Feature Engineering sonrası etkisi belirginleşti).



## 👨‍💻 Yazar
Bu proje, veri bilimi ve makine öğrenmesi teknikleri kullanılarak sağlık alanında karar destek mekanizması oluşturmak amacıyla geliştirilmiştir.
