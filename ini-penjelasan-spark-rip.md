# ini ko baca yang __judul__ dan  __penjelasa__ aja jangan code nya


---

### 1. Instalasi dan Konfigurasi Apache Spark di Google Colab

```python
# Install Java, Spark, dan findspark
!apt-get install openjdk-11-jdk-headless -qq > /dev/null
!wget -q https://archive.apache.org/dist/spark/spark-3.1.2/spark-3.1.2-bin-hadoop2.7.tgz
!tar xf spark-3.1.2-bin-hadoop2.7.tgz
!pip install -q findspark
```

💬 *Penjelasan:*
Langkah ini bertujuan untuk menginstal Java dan Spark versi 3.1.2 pada Google Colab. Spark membutuhkan Java sebagai dependensinya. Perintah `findspark` dipasang untuk mempermudah integrasi Spark di Python.

```python
# Set environment
import os
os.environ["JAVA_HOME"] = "/usr/lib/jvm/java-11-openjdk-amd64"
os.environ["SPARK_HOME"] = "/content/spark-3.1.2-bin-hadoop2.7"

import findspark
findspark.init()
```

💬 *Penjelasan:*
Di sini kita mengatur variabel lingkungan agar Spark dapat dikenali oleh sistem. `findspark.init()` berfungsi untuk menghubungkan Spark ke sesi Python yang sedang berjalan.

---

### 2. Membuat Spark Session

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("AnalisisCuacaJakarta") \
    .getOrCreate()
```

💬 *Penjelasan:*
Kita membuat **Spark Session** yang merupakan pintu masuk untuk menggunakan semua fitur Apache Spark. Session ini diberi nama "AnalisisCuacaJakarta" agar mudah dikenali.

---

### 3. Membaca Dataset

```python
df = spark.read.csv("/content/jakarta.csv", header=True, inferSchema=True)
# df = spark.read.csv("./jakarta.csv", header=True, inferSchema=True) kalau di local
df.printSchema()
df.show(5)
```

💬 *Penjelasan:*
Dataset `jakarta.csv` dibaca dengan Spark DataFrame.

* `header=True` menunjukkan bahwa file memiliki baris header.
* `inferSchema=True` memerintahkan Spark untuk mendeteksi tipe data secara otomatis.
  `printSchema()` menampilkan struktur kolom dan tipe datanya, sedangkan `show(5)` menampilkan 5 data teratas sebagai sampel.

✔️ *Hasil yang Didapat:*
Struktur tabel dengan kolom seperti `datetime`, `tempmax`, `tempmin`, `temp`, `humidity`, `windspeed`, `sealevelpressure`, dll.
Data sampel berupa cuaca harian Jakarta seperti suhu maksimum, suhu minimum, tekanan udara, dan lainnya.

---

### 4. Menampilkan Statistik Deskriptif

```python
df.describe().show()
```

💬 *Penjelasan:*
Kode ini menghitung **statistik deskriptif** seperti *count, mean, stddev, min, dan max* untuk setiap kolom numerik.
Ini membantu memahami gambaran umum distribusi data yang dimiliki.

✔️ *Hasil yang Didapat:*
Diperoleh nilai rata-rata, standar deviasi, nilai minimum, dan maksimum dari semua kolom numerik seperti suhu, kelembapan, dan tekanan.

---

### 5. Visualisasi Data dengan Matplotlib

```python
import matplotlib.pyplot as plt

# Ambil data suhu dan tekanan udara
data = df.select("datetime", "temp", "sealevelpressure").toPandas()

# Konversi kolom datetime ke tipe datetime pandas
data['datetime'] = pd.to_datetime(data['datetime'])

# Sortir data berdasarkan waktu
data = data.sort_values(by='datetime')

# Plot suhu dan tekanan udara
fig, ax1 = plt.subplots()

color = 'tab:blue'
ax1.set_xlabel('Waktu')
ax1.set_ylabel('Suhu (°C)', color=color)
ax1.plot(data['datetime'], data['temp'], color=color, marker='o')
ax1.tick_params(axis='y', labelcolor=color)

ax2 = ax1.twinx()

color = 'tab:red'
ax2.set_ylabel('Tekanan (mb)', color=color)
ax2.plot(data['datetime'], data['sealevelpressure'], color=color, marker='s')
ax2.tick_params(axis='y', labelcolor=color)

plt.title('Grafik Suhu dan Tekanan Udara (Seperti Gambar Contoh)')
plt.show()
```

💬 *Penjelasan:*
Langkah ini mengubah DataFrame Spark menjadi DataFrame Pandas agar dapat divisualisasikan dengan Matplotlib.

* Data diurutkan berdasarkan waktu agar grafik terbaca dengan baik.
* Sumbu kiri menunjukkan **suhu** (warna biru) dan sumbu kanan menunjukkan **tekanan udara** (warna merah).
* Grafik menggambarkan hubungan antara suhu dan tekanan udara selama periode waktu tertentu.

✔️ *Hasil yang Didapat:*
Grafik dua sumbu:

* Garis biru menunjukkan tren **kenaikan suhu**.
* Garis merah menunjukkan **fluktuasi tekanan udara**.

Grafik ini memudahkan untuk melihat pola cuaca yang terjadi berdasarkan data yang tersedia.

