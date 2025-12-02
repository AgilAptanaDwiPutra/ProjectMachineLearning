# 📰 Klasifikasi Teks Berita Bahasa Indonesia  
**Perbandingan Model Klasik, Deep Learning (RNN), dan Transfer Learning IndoBERT + LoRA**

Repositori ini berisi kode dan eksperimen penelitian untuk membandingkan performa tiga pendekatan klasifikasi teks berbahasa Indonesia:

- **Model Klasik (Naive Bayes, SVM, Random Forest)**
- **Model Deep Learning (RNN/GRU)**
- **Model Transfer Learning IndoBERT (Full Fine-Tuning & LoRA)**

Dataset berupa subtitle berita dari detik.com yang diklasifikasikan ke empat kategori topik.

---

## 🎯 Tujuan Penelitian

- Membandingkan performa model klasik, RNN, dan IndoBERT.
- Mengukur pengaruh representasi fitur (BoW, TF-IDF, Word2Vec) pada model klasik & RNN.
- Menganalisis ketidakseimbangan kelas terhadap F1-Macro.
- Menguji efektivitas **IndoBERT full fine-tuning** dan **IndoBERT dengan LoRA** pada teks berita pendek.

Klasifikasi mencakup 4 kategori:
1. Politics  
2. Technology  
3. Entertainment  
4. Others  

---

## 📊 Dataset

Dataset diperoleh dari scraping subtitle berita **detik.com**.

- Total data: **9.479 subtitle**
- Kategori: **4 kelas**
- Distribusi data tidak seimbang
- Preprocessing:
  - lowercase  
  - hapus simbol & angka  
  - tokenisasi  
  - stopword removal (NLTK + Sastrawi)  
  - stemming (Sastrawi)

---

## 🚀 Model yang Digunakan

### 🔹 1. Model Klasik  
Menggunakan fitur:
- BoW  
- TF-IDF  
- Word2Vec-average  

Model:
- Naive Bayes  
- Linear SVM  
- Random Forest  

---

### 🔹 2. Model Deep Learning (RNN/GRU)

Input:  
- BoW / TF-IDF / Word2Vec  

Arsitektur:  
- Layer RNN / GRU  
- Dense  
- Softmax  

---

### 🔹 3. Model Transfer Learning IndoBERT (Full Fine-Tuning)

- Model: `indobenchmark/indobert-base-p1`
- Digunakan sebagai **full classifier**
- Fine-tuning dilakukan end-to-end menggunakan HuggingFace `Trainer`
- Tidak menggunakan lagi:
  - BoW
  - TF-IDF  
  - Word2Vec  
  - Logistic Regression  
- Representasi kontekstual sepenuhnya dihasilkan internal oleh IndoBERT

---

### 🔹 4. **IndoBERT + LoRA (Low-Rank Adaptation)**  
*Parameter-Efficient Fine-Tuning (PEFT)*

Selain full fine-tuning, penelitian ini juga menerapkan **LoRA** untuk membuat fine-tuning lebih efisien:

- Parameter IndoBERT asli dibekukan (frozen)  
- LoRA menambahkan matriks ber-rank rendah pada layer Attention (Query & Value)  
- Hanya ±0.1% parameter yang dilatih  
- Lebih cepat, hemat GPU, dan lebih stabil pada dataset kecil  

Konfigurasi LoRA yang digunakan:
- `r = 8`  
- `alpha = 16`  
- `lora_dropout = 0.1`  
- `target_modules = ["query", "value"]`  
- Task: klasifikasi teks (SEQ_CLS)

Keunggulan LoRA:
- Latihan lebih cepat daripada full fine-tuning  
- Risiko overfitting lebih rendah  
- Performanya tetap kompetitif  
- Dapat dijalankan pada GPU kecil  

---

## 🔧 Representasi Fitur

- **BoW, TF-IDF, Word2Vec** → hanya untuk model klasik & RNN  
- **IndoBERT / IndoBERT-LoRA full fine-tuning** → tidak menggunakan fitur manual  

---

## 📈 Hasil Eksperimen

### 🔹 Model Klasik

| Model           | Fitur      | Accuracy | F1-Macro |
|-----------------|------------|----------|----------|
| NB Multinomial  | BoW        | 0.7127   | 0.5233   |
| NB Multinomial  | TF-IDF     | 0.6760   | 0.4312   |
| NB Gaussian     | Word2Vec   | 0.5337   | 0.4262   |
| Linear SVM      | BoW        | 0.8433   | 0.6891   |
| Linear SVM      | TF-IDF     | **0.8524** | 0.6695 |
| Random Forest   | BoW        | **0.8779** | 0.6863 |
| Random Forest   | TF-IDF     | 0.8731   | 0.6827 |
| Random Forest   | Word2Vec   | 0.6819   | 0.4638 |

---

### 🔹 Model Deep Learning (RNN)

| Model | Fitur  | Accuracy | F1-Macro |
|-------|--------|----------|----------|
| RNN   | BoW    | **0.82** | **0.71** |
| RNN   | TF-IDF | 0.79     | 0.66     |
| RNN   | W2V    | 0.78     | 0.56     |

---

### 🔹 Model Transfer Learning IndoBERT (Full Fine-Tuning)

| Model      | Pendekatan           | Accuracy | F1-Macro | Catatan Utama |
|------------|----------------------|----------|----------|----------------|
| IndoBERT   | Fine-tuning penuh    | **0.86** | **0.75** | Performa terbaik & paling stabil |

---

### 🔹 Model Transfer Learning IndoBERT + LoRA

| Model         | Pendekatan | Accuracy | F1-Macro | Catatan Utama |
|---------------|------------|----------|----------|----------------|
| IndoBERT-LoRA | PEFT       | Hampir setara IndoBERT full FT | Stabil, training jauh lebih ringan |

> LoRA menghasilkan performa yang mendekati IndoBERT full fine-tuning, namun dengan waktu pelatihan lebih cepat dan konsumsi memori lebih rendah.

---

## 🧠 Insight Utama

- **BoW** adalah fitur paling stabil untuk model klasik & RNN.  
- **Random Forest & SVM** unggul pada pendekatan klasik.  
- **Word2Vec-average** kurang efektif karena menghilangkan konteks.  
- **IndoBERT full fine-tuning** memberikan performa terbaik secara keseluruhan.  
- **IndoBERT-LoRA** sangat efisien dan cocok untuk dataset kecil tanpa kehilangan banyak akurasi.

---

## 🛠️ XAI (Explainable AI)

Metode interpretasi yang digunakan:

- **SHAP** → untuk model klasik  
  (menjelaskan kontribusi kata pada BoW/TF-IDF/W2V)  
- **LIME** → untuk model RNN  
- **LIME** → untuk IndoBERT dan IndoBERT-LoRA  

XAI membantu mengetahui kata atau frasa apa yang paling memengaruhi prediksi model.
