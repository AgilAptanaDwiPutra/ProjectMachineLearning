#  Klasifikasi teks berita bahasa indonesia

Repositori ini berisi kode dan eksperimen penelitian yang membandingkan model Machine Learning klasik dan Deep Learning berbasis RNN untuk tugas klasifikasi teks subtitle. Penelitian ini menguji tiga representasi fitur utama: **Bag-of-Words (BoW)**, **TF-IDF**, dan **Word2Vec**, pada berbagai model seperti Naive Bayes, SVM, Random Forest, serta RNN.

---

## 📌 Tujuan Penelitian
Menganalisis performa model klasik vs model deep learning dalam mengklasifikasikan teks subtitle ke dalam 4 kategori:
1. **Politics**
2. **Technology**
3. **Entertainment**
4. **Others**

Penelitian mengevaluasi:
- Pengaruh representasi fitur terhadap performa model  
- Dampak ketidakseimbangan dataset  
- Perbandingan akurasi dan macro-F1  
- Perilaku model pada teks pendek (subtitle)

---

## 📊 Dataset  
Asal dataset ini di scraping dari detik.com secara keseluruhan.
- Total data: **9.479 subtitle**  
- Jumlah kelas: **4**  
- Kelas sangat tidak seimbang → kelas Entertainment hanya 62 data  
- Preprocessing dilakukan sesuai tahapan laporan:
  - lowercasing  
  - penghapusan simbol  
  - tokenisasi  
  - stopword removal  
  - stemming

---

## 🚀 Model yang Diuji

### **🔹 Model Klasik**
- **Naive Bayes (Multinomial & Gaussian)**  
- **Support Vector Machine (LinearSVC)**  
- **Random Forest Classifier**

### **🔹 Model Deep Learning**
- RNN menggunakan arsitektur:
  - Input shape: (1, n_features)
  - GRU/SimpleRNN
  - Dense (ReLU)
  - Softmax output

---

## 🔧 Representasi Fitur
- **BoW (CountVectorizer)**  
- **TF-IDF (TfidfVectorizer)**  
- **Word2Vec (Gensim Skip-gram, 300 dimensi, doc-average)**  

---

## 📈 Hasil Eksperimen  
### 🔹 **Model Klasik** (PDF halaman 16–17)

| Model | Representasi | Accuracy | F1-Macro |
|-------|--------------|----------|----------|
| NB Multinomial | BoW | 0.7127 | 0.5233 |
| NB Multinomial | TF-IDF | 0.6760 | 0.4312 |
| NB Gaussian | Word2Vec | 0.5337 | 0.4262 |
| Linear SVM | BoW | 0.8433 | 0.6891 |
| Linear SVM | TF-IDF | **0.8524** | 0.6695 |
| Random Forest | BoW | **0.8779** | 0.6863 |

### 🔹 **Model Deep Learning (RNN)** (PDF halaman 22–25)

| Model | Representasi | Accuracy | F1-Macro |
|-------|--------------|----------|----------|
| RNN | BoW | **0.82** | **0.71** |
| RNN | TF-IDF | 0.79 | 0.66 |
| RNN | Word2Vec | 0.78 | 0.56 |

---

## 🧠 Insight Penting 
1. **BoW adalah representasi paling stabil** untuk semua model.  
2. **Random Forest** mencapai akurasi tertinggi dari seluruh model klasik dan deep learning.  
3. **TF-IDF sangat efektif untuk SVM**, tetapi menyebabkan overfitting di RNN.  
4. **Word2Vec doc-average menghilangkan konteks urutan**, sehingga model mengalami penurunan performa.  
5. Ketidakseimbangan kelas menjadi faktor utama rendahnya nilai macro-F1.  
6. RNN tidak optimal karena input tidak mengandung urutan kata (1 timestep).  

---

## 📝 Kesimpulan  
- Model klasik (terutama Random Forest dan SVM) **mengungguli** model RNN dalam klasifikasi subtitle.  
- Representasi fitur sangat berpengaruh; **BoW dan TF-IDF** bekerja jauh lebih baik dibanding Word2Vec average.  
- RNN membutuhkan pendekatan sekuens (Embedding + LSTM/BiLSTM) untuk meningkatkan performa.

---

