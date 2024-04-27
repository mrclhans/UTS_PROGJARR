# UTS PROGJAR
# server.py
```python
import  socket

import  random

import  threading

import  time

import  sys

  

# Membuat pemetaan antara warna dalam bahasa Inggris dan bahasa Indonesia

pemetaan_warna  = {

'red': 'merah',

'yellow': 'kuning',

'blue': 'biru',

'orange': 'orange',

'purple': 'ungu',

'green': 'green',

'brown': 'coklat',

'black': 'hitam',

'white': 'putih'

}

  

# Fungsi untuk mengatur timer respons klien

def  klien_timer_respons(alamat_klien):

time.sleep(5) # Klien memiliki 5 detik untuk merespons

if  not  respons_diterima.is_set():

print("[SERVER] Waktu respons telah habis. Tidak ada respons yang diterima.")

umpan_balik  =  0

kunci_umpan_balik.acquire()

try:

umpan_baliks[alamat_klien] =  umpan_balik

finally:

kunci_umpan_balik.release()

SocketServer.sendto(b'[SERVER] Waktu habis!', alamat_klien) # Kirim pesan "Waktu habis" ke klien

  

# Fungsi untuk menghentikan utas pengirim warna dan menutup soket server

def  hentikan_server():

global  berjalan

berjalan  =  False  # Set flag berjalan menjadi False untuk menghentikan perulangan while di kode server utama

utas_pengirim_warna.join() # Tunggu utas pengirim warna untuk selesai

SocketServer.close() # Tutup soket server

sys.exit(0) # Keluar dari program dengan kode keluar 0

  
  
  

# Fungsi untuk mengirim warna ke semua klien

def  kirim_warna_ke_klien(warna):

for  alamat_klien  in  klien:

SoketKlien.sendto(str.encode(warna), alamat_klien)

  

# Server

SocketServer  =  socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

host  =  '127.0.0.1'

port  =  5555

  

warna  =  list(pemetaan_warna.keys())

  

try:

SocketServer.bind((host, port))

except  socket.error  as  e:

print(str(e))

  

print('[SERVER] Menunggu Koneksi..')

  

umpan_baliks  = {} # Kamus untuk menyimpan umpan balik dari klien

kunci_umpan_balik  =  threading.Lock() # Kunci untuk mengakses kamus umpan balik

respons_diterima  =  threading.Event() # Event untuk menandai apakah respons dari klien diterima

  

klien  =  set() # Set untuk menyimpan alamat klien yang terhubung

  

# Tentukan SoketKlien

SoketKlien  =  socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

  

# Mulai utas untuk terus-menerus mengirim warna ke semua klien

def  pengirim_warna(berjalan): # Terima flag berjalan sebagai argumen

while  berjalan:

warna_acak  =  random.choice(warna)

kirim_warna_ke_klien(warna_acak)

time.sleep(10) # Kirim warna setiap 10 detik

  

utas_pengirim_warna  =  threading.Thread(target=pengirim_warna, args=(True,)) # Lewati True untuk menunjukkan berjalan

utas_pengirim_warna.start()

  

berjalan  =  True  # Tentukan flag berjalan di sini

while  berjalan:

data, alamat  =  SocketServer.recvfrom(1024)

print('[CONNECTED] Terhubung dengan: '  +  alamat[0] +  ':'  +  str(alamat[1]))

klien.add(alamat) # Tambahkan alamat klien ke set klien yang terhubung

# Mulai timer untuk waktu respons klien

utas_timer  =  threading.Thread(target=klien_timer_respons, args=(alamat,))

utas_timer.start()

# Kirim warna acak ke klien

warna_acak  =  random.choice(warna)

SocketServer.sendto(str.encode(warna_acak), alamat)

# Terima respons dari klien

respons_diterima.clear() # Tandai bahwa respons belum diterima

Respons, _  =  SocketServer.recvfrom(1024)

respons_diterima.set() # Tandai bahwa respons diterima

# Periksa jawaban klien

jawaban_klien  =  Respons.decode('utf-8').lower().strip() # Ubah ke huruf kecil dan hapus spasi

jawaban_benar  =  pemetaan_warna[warna_acak] # Dapatkan terjemahan Indonesia dari warna yang benar

umpan_balik  =  100  if  jawaban_klien  ==  jawaban_benar  else  0

# Berikan umpan balik kepada klien

kunci_umpan_balik.acquire()

try:

umpan_baliks[alamat] =  umpan_balik

finally:

kunci_umpan_balik.release()

# Perbaiki jawaban dan berikan umpan balik

if  umpan_balik  ==  100:

print("[SERVER] Klien di {} menjawab dengan benar '{}'.".format(alamat, jawaban_klien))

SocketServer.sendto(b'[SERVER] Jawaban Anda benar! Nilai Anda 100', alamat)

else:

print("[SERVER] Klien di {} menjawab dengan salah '{}' (jawaban benar: '{}').".format(alamat, jawaban_klien, jawaban_benar))

SocketServer.sendto(b'[SERVER] Jawaban Anda salah! Nilai Anda 0', alamat)

  

hentikan_server()
```

1.  **Import Libraries**: Mengimpor modul-modul yang diperlukan seperti `socket`, `random`, `threading`, `time`, dan `sys`.
    
2.  **Pemetaan Warna**: Membuat pemetaan antara warna dalam bahasa Inggris dan Bahasa Indonesia.
    
3.  **Fungsi Timer Respons Klien**: Membuat fungsi `klien_timer_respons` untuk mengatur timer respons klien.
    
4.  **Fungsi untuk Menghentikan Server**: Membuat fungsi `hentikan_server` untuk menghentikan server secara graceful.
    
5.  **Fungsi untuk Mengirim Warna ke Klien**: Membuat fungsi `kirim_warna_ke_klien` untuk mengirim warna ke semua klien yang terhubung.
    
6.  **Inisialisasi Server**: Menyiapkan socket server, mengikatnya ke host dan port tertentu, dan menangani pengecualian jika ada.
    
7.  **Loop Utama**: Melakukan pengulangan utama untuk mendengarkan data masuk dari klien, menambahkan klien baru ke set klien yang terhubung, mengirimkan warna acak ke klien, menerima respons dari klien, memeriksa jawaban klien, memberikan umpan balik kepada klien, dan menghentikan server jika diperlukan.
    
8.  **Thread untuk Mengirim Warna**: Membuat thread `pengirim_warna` untuk secara terus-menerus mengirimkan warna ke klien.
    

Kode ini mengimplementasikan server untuk permainan menebak warna dengan menyediakan fungsi-fungsi yang diperlukan seperti mengatur timer respons klien, mengirim warna ke klien, dan menghentikan server secara graceful saat diperlukan.


# client.py
```python
import  socket

import  sys

  

# Membuat pemetaan antara warna dalam bahasa Indonesia dan bahasa Inggris

# Klien

KlienSocket  =  socket.socket(socket.AF_INET, socket.SOCK_DGRAM) # Membuat soket UDP

host  =  '127.0.0.1'  # Host lokal

port  =  5555  # Port server

  

# Mengirim pesan kosong ke server untuk memulai komunikasi

KlienSocket.sendto(b'', (host, port)) # Mengirim pesan kosong ke server

  

berjalan  =  True  # Flag untuk menandai apakah program berjalan atau tidak

while  berjalan:

# Menerima warna dari server

Respon, alamat  =  KlienSocket.recvfrom(1024) # Menerima respon dari server

warna  =  Respon.decode('utf-8') # Mendecode pesan menjadi string

print("[SERVER] Menerima warna dari server:", warna) # Menampilkan pesan

# Meminta jawaban dari pengguna dalam bahasa Indonesia

jawaban  =  input("[SYSTEM] Apa warna ini? (Jawab dalam bahasa Indonesia): ") # Meminta input dari pengguna

# Mengirim jawaban pengguna ke server

KlienSocket.sendto(str.encode(jawaban), (host, port)) # Mengirim jawaban ke server

# Menerima umpan balik dari server

umpan_balik, _  =  KlienSocket.recvfrom(1024) # Menerima umpan balik dari server

print(umpan_balik.decode('utf-8')) # Menampilkan umpan balik dari server

  

# Menutup koneksi soket saat loop selesai

KlienSocket.close() # Menutup soket klien

sys.exit(0) # Mengakhiri program dengan kode keluar 0
```
1.  **Inisialisasi Klien**: Membuat soket UDP untuk klien dan menentukan host dan port server.
    
2.  **Memulai Komunikasi**: Mengirim pesan kosong ke server untuk memulai komunikasi.
    
3.  **Loop Utama**: Melakukan pengulangan utama untuk menerima warna dari server, meminta jawaban dari pengguna dalam bahasa Indonesia, mengirim jawaban pengguna ke server, dan menerima umpan balik dari server.
    
4.  **Menutup Koneksi**: Setelah loop selesai, menutup koneksi soket klien dan mengakhiri program.
    

Kode ini mengimplementasikan klien untuk permainan menebak warna dengan melakukan komunikasi dengan server, meminta input pengguna, dan menampilkan umpan balik dari server.

# output
[![N|Solid]([[https://cldup.com/dTxpPi9lDf.thumb.png](https://github.com/mrclhans/UTS_PROGJARR/blob/main/1.PNG)](https://raw.githubusercontent.com/mrclhans/UTS_PROGJARR/main/2.PNG))]
[![N|Solid]([[https://cldup.com/dTxpPi9lDf.thumb.png](https://github.com/mrclhans/UTS_PROGJARR/blob/main/2.PNG)](https://raw.githubusercontent.com/mrclhans/UTS_PROGJARR/main/1.PNG))]
