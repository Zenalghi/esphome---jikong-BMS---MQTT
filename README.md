# ESPHome JK BMS BLE to MQTT Bridge

Proyek ini berisi konfigurasi [ESPHome](https://esphome.io/) untuk menggunakan ESP32 sebagai jembatan (bridge) komunikasi antara [JK BMS](https://github.com/syssi/esphome-jk-bms) (via Bluetooth Low Energy / BLE) dan MQTT. 

File konfigurasi utama adalah `JK-BMS.yaml`.

## ⚠️ Konfigurasi Penting Sebelum Flash

Sesuai dengan catatan sebelumnya, sebelum melakukan kompilasi atau *flashing* ke ESP32, **pastikan Anda mengubah `mac_address`** pada file `JK-BMS.yaml` sesuai dengan alamat MAC Bluetooth dari perangkat JK BMS Anda!

```yaml
substitutions:
  name: jk-bms
  # UBAH MAC ADDRESS DI BAWAH INI SESUAI BMS ANDA!
  mac_address: 28:D4:1E:26:24:71 
  protocol_version: JK02_32S
  mqtt_prefix: "bms_panel/2602165"
```

## Fitur Utama

1. **Koneksi BLE ke BMS**: Membaca data secara *real-time* dari JK BMS menggunakan komponen `jk_bms_ble` dari repositori Syssi.
2. **Pengiriman Data via MQTT**: Mengemas berbagai data dari BMS ke dalam format JSON dan memublikasikannya secara berkala (setiap 1 detik) ke broker MQTT (default: `broker.emqx.io`).
3. **WiFi & OTA**: Menggunakan dukungan WiFi Manager (Captive Portal) pada AP `Homispe` untuk kemudahan pengaturan koneksi jaringan, serta dukungan pembaruan Over-The-Air (OTA).
4. **Kontrol BMS (Switch & Setting)**: Mengekspos sakelar kontrol (Charge, Discharge, Balance) dan pengaturan parameter (Cell count, Capacity, Balance trigger voltage) agar dapat diatur dan dipantau dari luar secara langsung.

## Topik MQTT

Data dikirimkan ke beberapa topik MQTT dengan awalan (prefix) yang didefinisikan pada bagian `substitutions` (default: `bms_panel/2602165`).

### 1. Data Utama (`bms_panel/2602165/data/main`)
Berisi data telemetri umum dan rincian per sel (sel 1 hingga 8):
- `voltage` (Tegangan Total)
- `current` (Arus)
- `power` (Daya)
- `mos_temp` (Suhu MOS)
- `bat_temp1` & `bat_temp2` (Suhu Baterai)
- `cells_v` (Array tegangan sel 1-8)
- `wire_res` (Array hambatan kabel per sel 1-8)

### 2. Data SOC (`bms_panel/2602165/data/soc_bawaan`)
- `soc_jk` (State of Charge / Persentase Baterai)
- `capacity_remain` (Kapasitas tersisa)

### 3. Status Switch (`bms_panel/2602165/state/switches`)
Menunjukkan status sakelar relay pengontrol BMS:
- `charge_switch` (ON / OFF)
- `discharge_switch` (ON / OFF)
- `balance_switch` (ON / OFF)

### 4. Status Pengaturan (`bms_panel/2602165/state/settings`)
Menunjukkan nilai dari pengaturan BMS:
- `cell_count_setting`
- `capacity_setting`
- `balance_trig_v`

## Cara Penggunaan

1. Buka file [`JK-BMS.yaml`](JK-BMS.yaml).
2. Ganti nilai `mac_address` dengan MAC perangkat JK BMS Anda.
3. Sesuaikan `mqtt_prefix` atau `broker` MQTT jika Anda menggunakan broker pribadi.
4. Flash konfigurasi ke board ESP32 Anda menggunakan ESPHome (via Home Assistant Add-on, Command Line, atau ESPHome Web).
