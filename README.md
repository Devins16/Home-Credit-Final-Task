# Home Credit Payment Difficulty Prediction

## 1. Problem Research

Home Credit perlu mengidentifikasi calon nasabah yang berisiko mengalami kesulitan pembayaran (`TARGET = 1`).

### Tantangan Utama

- Data bersifat **imbalanced** (±8% default)
- Risiko gagal bayar menyebabkan kerugian finansial
- Informasi kredit tersebar di beberapa tabel
- Dibutuhkan sistem screening berbasis data untuk mendukung proses underwriting

### Objective

- Memprediksi probabilitas gagal bayar
- Membandingkan model baseline (data aplikasi saja) dengan model yang menggunakan external credit history
- Mengidentifikasi faktor risiko utama

---

## 2. Dataset Overview

### Data Sources

- `application_train` (dengan TARGET)
- `application_test` (tanpa TARGET)
- `bureau`
- `previous_application`
- `installments_payments`
- `pos_cash_balance`
- `credit_card_balance`

### Karakteristik Dataset

- 307.511 baris data training
- 233 fitur awal

### Target Variable

- `0` → Tidak gagal bayar
- `1` → Gagal bayar

Data historis bersifat **transaksi-level** sehingga perlu dilakukan agregasi menjadi **customer-level**.

---

## 3. Data Preprocessing & Feature Engineering

### Data Cleaning

- Menghapus fitur dengan missing value tinggi
- Imputasi median untuk fitur numerik

### Feature Engineering

- Membuat fitur `ext_median` dari `EXT_SOURCE_1/2/3`
- Konversi `DAYS_BIRTH` menjadi usia (tahun)
- Log transform untuk fitur yang skewed
- One-Hot Encoding untuk fitur kategorikal
- Scaling (khusus Logistic Regression)

### External Data Aggregation

Agregasi dilakukan menggunakan `groupby()` dan `agg()` untuk menghasilkan:

- Total debt
- Jumlah aplikasi sebelumnya
- Rata-rata kredit
- Jumlah keterlambatan pembayaran
- Credit utilization

Hasil akhir: ±45 fitur customer-level (1 baris per `SK_ID_CURR`).

---

## 4. Data Visualization & Business Insight

### Temuan Utama dari EDA

- Class imbalance signifikan → evaluasi menggunakan **AUPRC**
- Usia lebih muda cenderung memiliki risiko default lebih tinggi
- Kelompok income lebih rendah memiliki proporsi default lebih besar
- Nilai `EXT_SOURCE` rendah berkorelasi kuat dengan risiko gagal bayar
- Riwayat keterlambatan pembayaran menjadi indikator risiko paling kuat

### Insight Utama

External credit history dan behavioral data lebih informatif dibandingkan data aplikasi saja.

---

## 5. Machine Learning Implementation

Empat model dibandingkan:

1. Logistic Regression (Application Only)
2. Logistic Regression (Application + External Data)
3. XGBoost (Application Only)
4. XGBoost (Application + External Data)

Dilakukan hyperparameter tuning pada XGBoost untuk meningkatkan performa model.

---

## 6. Model Evaluation

Karena data bersifat imbalanced, digunakan:

- **AUPRC** (metric utama)
- **ROC-AUC** (metric tambahan)

### ROC-AUC Results

| Model | AUC |
|-------|------|
| Logistic Regression (App Only) | 0.637 |
| Logistic Regression (Merge Data) | 0.650 |
| XGBoost (App Only) | 0.759 |
| XGBoost (Merge Data) | 0.767 |

Model terbaik: **XGBoost + External Data**

---

## 7. Business Recommendation

- Implementasikan XGBoost + External Data sebagai decision support system
- Gunakan threshold berbasis risk appetite perusahaan
- Lakukan monitoring dan retraining model secara berkala
- Integrasikan model ke proses underwriting untuk meningkatkan kualitas risk screening

---

## Conclusion

- Integrasi external credit history meningkatkan performa model secara konsisten
- XGBoost menunjukkan hasil terbaik
- Model layak digunakan untuk mendukung strategi manajemen risiko kredit

---

## Link

- Linkedin    : https://www.linkedin.com/in/devin-syafa-92b7bb22b/
- Presentation: https://youtu.be/ea6SiW_jCMY
