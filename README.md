# 📰 Klasifikasi Teks Berita Bahasa Indonesia  
**Perbandingan Model Klasik, Deep Learning (RNN), dan Transfer Learning IndoBERT**

Repositori ini berisi kode dan eksperimen penelitian yang membandingkan:

- **Model Machine Learning klasik**
- **Model Deep Learning berbasis RNN**
- **Model Transfer Learning berbasis IndoBERT**

untuk tugas klasifikasi teks subtitle berita berbahasa Indonesia.

Eksperimen mencakup berbagai kombinasi **model** × **representasi fitur**:  
**BoW**, **TF-IDF**, **Word2Vec**, serta **kombinasi fitur dengan IndoBERT**.

---

## 🎯 Tujuan Penelitian

Penelitian ini bertujuan untuk:

- Menganalisis performa model klasik vs deep learning vs transfer learning.
- Mengukur pengaruh representasi fitur terhadap performa model.
- Mengamati dampak ketidakseimbangan kelas terhadap skor macro-F1.
- Mengkaji perilaku model pada teks pendek (subtitle berita).

Klasifikasi dilakukan ke dalam 4 kategori:

1. **Politics**
2. **Technology**
3. **Entertainment**
4. **Others**

---

## 📊 Dataset

Dataset diperoleh melalui scraping dari **detik.com**.

- Total data: **9.479 subtitle**
- Jumlah kelas: **4**
- Distribusi kelas tidak seimbang (kelas *Entertainment* hanya ±62 data)
- Tahapan preprocessing:
  - lowercasing  
  - penghapusan simbol & angka  
  - tokenisasi  
  - stopword removal  
  - stemming (Sastrawi)

---

## 🚀 Model yang Diuji

### 🔹 Model Klasik
- **Naive Bayes**
  - Multinomial
  - Gaussian
- **Support Vector Machine**
  - Linear SVM (LinearSVC)
- **Random Forest Classifier**

### 🔹 Model Deep Learning
- **RNN / GRU** dengan arsitektur sederhana:
  - Input: vektor fitur (hasil BoW / TF-IDF / Word2Vec average)
  - Layer RNN (SimpleRNN/GRU)
  - Dense (ReLU)
  - Output Softmax

### 🔹 Model Transfer Learning (IndoBERT)

Model transfer learning menggunakan:

- **IndoBERT**: `indobenchmark/indobert-base-p1`  
- Digunakan sebagai **feature extractor**:
  - Setiap teks di-*tokenize* dengan tokenizer IndoBERT.
  - Output logits / hidden representation diambil sebagai fitur numerik.
- Fitur IndoBERT kemudian **digabungkan** dengan:
  - **BoW**
  - **TF-IDF**
  - **Word2Vec (average)**  
- Klasifikasi dilakukan menggunakan **Logistic Regression** di atas fitur gabungan tersebut.

---

## 🔧 Representasi Fitur

- **BoW (Bag-of-Words, CountVectorizer)**  
  Representasi sparse berbasis frekuensi kata.

- **TF-IDF (TfidfVectorizer)**  
  Memberi bobot lebih pada kata-kata yang jarang tetapi informatif.

- **Word2Vec (Gensim Skip-gram, 300 dimensi, doc-average)**  
  Kalimat direpresentasikan sebagai rata-rata vektor word embedding.

- **IndoBERT logits (Transfer Learning)**  
  Representasi kontekstual dari Transformer, kemudian:
  - Digabung dengan BoW  
  - Digabung dengan TF-IDF  
  - Digabung dengan Word2Vec average  

---

## 📈 Hasil Eksperimen

### 🔹 Model Klasik

| Model           | Representasi | Accuracy | F1-Macro |
|-----------------|--------------|----------|----------|
| NB Multinomial  | BoW          | 0.7127   | 0.5233   |
| NB Multinomial  | TF-IDF       | 0.6760   | 0.4312   |
| NB Gaussian     | Word2Vec     | 0.5337   | 0.4262   |
| Linear SVM      | BoW          | 0.8433   | 0.6891   |
| Linear SVM      | TF-IDF       | **0.8524** | 0.6695 |
| Random Forest   | BoW          | **0.8779** | 0.6863 |
| Random Forest   | TF-IDF       | 0.8731   | 0.6827   |
| Random Forest   | Word2Vec     | 0.6819   | 0.4638   |

---

### 🔹 Model Deep Learning (RNN)

| Model | Representasi | Accuracy | F1-Macro |
|-------|--------------|----------|----------|
| RNN   | BoW          | **0.82** | **0.71** |
| RNN   | TF-IDF       | 0.79     | 0.66     |
| RNN   | Word2Vec     | 0.78     | 0.56     |

---

### 🔹 Model Transfer Learning IndoBERT

IndoBERT digunakan sebagai feature extractor, kemudian digabung dengan tiga jenis fitur tradisional. Hasil ringkas:

| Model               | Representasi Gabungan     | Accuracy  | F1-Macro | Catatan Utama                                      |
|---------------------|---------------------------|-----------|----------|----------------------------------------------------|
| IndoBERT + BoW      | IndoBERT logits + BoW     | **0.8217** | 0.61    | Kuat di kelas mayoritas, kelas minoritas masih lemah |
| IndoBERT + TF-IDF   | IndoBERT logits + TF-IDF  | 0.8106    | 0.59    | Mirip BoW, sedikit lebih rendah di kelas Technology |
| IndoBERT + Word2Vec | IndoBERT logits + W2V avg | **0.6376** | 0.39    | Tidak efektif, sulit memprediksi kelas minoritas    |

---

## 🧠 Insight Penting

1. **BoW adalah representasi paling stabil**  
   - Memberikan performa konsisten di Naive Bayes, SVM, Random Forest, dan RNN.
   - Mudah digunakan dan sangat sesuai untuk teks pendek.

2. **Random Forest & Linear SVM unggul di model klasik**  
   - Random Forest dengan BoW mencapai akurasi tertinggi.
   - Linear SVM dengan TF-IDF memberikan kombinasi akurasi + stabilitas yang sangat baik.

3. **TF-IDF cocok untuk SVM, tapi tidak selalu untuk RNN**  
   - TF-IDF meningkatkan performa SVM.
   - Pada RNN, TF-IDF cenderung menyebabkan overfitting dan menurunkan F1-macro.

4. **Word2Vec average kurang efektif untuk model klasik maupun RNN**  
   - Rata-rata embedding menghilangkan konteks urutan dan detail semantik level kalimat.
   - Model kesulitan membedakan kelas minoritas.

5. **RNN tidak optimal karena input bukan sekuens kata sebenarnya**  
   - RNN di sini bekerja di atas vektor fitur (bukan embedding urutan kata), sehingga keunggulan arsitektur sekuensial tidak termanfaatkan.

6. **Transfer Learning IndoBERT menjanjikan, tapi setup ini belum maksimal**  
   - IndoBERT + BoW / TF-IDF kompetitif, namun:
     - Belum mengalahkan Random Forest / SVM terbaik.
     - Masih lemah di kelas dengan sampel sangat sedikit (misalnya *Entertainment*).
   - Hal ini wajar karena:
     - IndoBERT hanya digunakan sebagai **feature extractor**, bukan di-*fine-tune* end-to-end.
     - Fitur gabungan cenderung berdimensi tinggi (high-dimensional), berpotensi menyebabkan overfitting.

---

## 📝 Kesimpulan

- **Model klasik (Random Forest dan Linear SVM)** masih menjadi baseline yang sangat kuat untuk klasifikasi subtitle berita pendek.
- **RNN** dapat mencapai F1-macro yang baik, namun kalah akurasi dan tidak memanfaatkan informasi urutan secara penuh dalam setup ini.
- **Transfer learning dengan IndoBERT** menunjukkan potensi, terutama bila nanti dikembangkan dengan:
  - Fine-tuning penuh (bukan sekadar feature extractor)
  - Penanganan ketidakseimbangan kelas (class weighting, oversampling)
  - Arsitektur khusus untuk teks pendek dan multi-class imbalance.

---
