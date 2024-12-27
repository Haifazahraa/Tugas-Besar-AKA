import time
import matplotlib.pyplot as plt
from prettytable import PrettyTable

# Kelas Dokter
class Dokter:
    def __init__(self, nama, spesialisasi, waktu_tersedia):
        self.nama = nama
        self.spesialisasi = spesialisasi
        self.waktu_tersedia = waktu_tersedia  # Dalam jam

# Kelas Pasien
class Pasien:
    def __init__(self, nama, kebutuhan_spesialisasi, durasi_perawatan):
        self.nama = nama
        self.kebutuhan_spesialisasi = kebutuhan_spesialisasi
        self.durasi_perawatan = durasi_perawatan  # Dalam jam

# Algoritma Greedy Rekursif
def jadwal_greedy_rekursif(dokter_list, pasien_list, index=0):
    if index == len(pasien_list):
        return []  # Basis rekursi: Tidak ada pasien yang tersisa untuk dijadwalkan

    pasien = pasien_list[index]
    for dokter in dokter_list:
        if dokter.spesialisasi == pasien.kebutuhan_spesialisasi and dokter.waktu_tersedia >= pasien.durasi_perawatan:
            dokter.waktu_tersedia -= pasien.durasi_perawatan  # Kurangi waktu dokter
            return [(dokter.nama, pasien.nama, pasien.durasi_perawatan)] + jadwal_greedy_rekursif(dokter_list, pasien_list, index + 1)

    return jadwal_greedy_rekursif(dokter_list, pasien_list, index + 1)

# Algoritma Greedy Iteratif
def jadwal_greedy_iteratif(dokter_list, pasien_list):
    jadwal = []
    for pasien in pasien_list:
        for dokter in dokter_list:
            if dokter.spesialisasi == pasien.kebutuhan_spesialisasi and dokter.waktu_tersedia >= pasien.durasi_perawatan:
                jadwal.append((dokter.nama, pasien.nama, pasien.durasi_perawatan))
                dokter.waktu_tersedia -= pasien.durasi_perawatan  # Kurangi waktu dokter
                break
    return jadwal

# Data untuk Grafik
jumlah_pasien_list = []
waktu_rekursif = []
waktu_iteratif = []

# Fungsi untuk memperbarui grafik
def perbarui_grafik():
    plt.clf()
    plt.plot(jumlah_pasien_list, waktu_rekursif, label='Greedy Rekursif', marker='o', linestyle='-')
    plt.plot(jumlah_pasien_list, waktu_iteratif, label='Greedy Iteratif', marker='o', linestyle='-')
    plt.title('Perbandingan Waktu Eksekusi: Greedy Rekursif vs Iteratif')
    plt.xlabel('Jumlah Pasien')
    plt.ylabel('Waktu Eksekusi (detik)')
    plt.legend()
    plt.grid(True)
    plt.pause(0.1)

# Fungsi untuk mencetak tabel hasil
def cetak_tabel_hasil():
    tabel = PrettyTable()
    tabel.field_names = ["Jumlah Pasien", "Waktu Rekursif (detik)", "Waktu Iteratif (detik)"]
    for i in range(len(jumlah_pasien_list)):
        tabel.add_row([jumlah_pasien_list[i], waktu_rekursif[i], waktu_iteratif[i]])
    print(tabel)

# Program Utama
plt.ion()
while True:
    try:
        jumlah_pasien = int(input("Masukkan jumlah pasien (atau ketik -1 untuk keluar): "))
        if jumlah_pasien == -1:
            print("Program selesai. Terima kasih!")
            break
        if jumlah_pasien <= 0:
            print("Masukkan jumlah pasien yang valid!")
            continue

        # Data dokter dan pasien
        dokter_list = [
            Dokter("Dr. Sarah", "Pediatri", 10),
            Dokter("Dr. John", "Kardiologi", 8),
            Dokter("Dr. Emily", "Neurologi", 12),
            Dokter("Dr. Mark", "Ortopedi", 6)
        ]

        pasien_list = [Pasien(f"Pasien {i + 1}", "Pediatri", 2) for i in range(jumlah_pasien // 2)]
        pasien_list += [Pasien(f"Pasien {i + 1 + jumlah_pasien // 2}", "Kardiologi", 3) for i in range(jumlah_pasien // 2)]

        jumlah_pasien_list.append(jumlah_pasien)

        # Hitung waktu untuk Greedy Rekursif
        mulai = time.time()
        jadwal_greedy_rekursif(dokter_list.copy(), pasien_list)
        waktu_rekursif.append(time.time() - mulai)

        # Hitung waktu untuk Greedy Iteratif
        mulai = time.time()
        jadwal_greedy_iteratif(dokter_list.copy(), pasien_list)
        waktu_iteratif.append(time.time() - mulai)

        # Cetak tabel hasil
        cetak_tabel_hasil()

        # Perbarui grafik
        perbarui_grafik()

    except ValueError:
        print("Masukkan jumlah pasien yang valid!")

plt.ioff()
plt.show()
