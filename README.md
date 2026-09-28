# ATC — Aksa Torque Control

**ATC (Aksa Torque Control)** adalah firmware dan software untuk membuat **DIY Direct Drive Force Feedback wheel** berbasis STM32.

Fokus ATC sederhana: membuat DD wheel yang performanya serius, tetapi tetap menggunakan hardware yang realistis untuk dicari, dimodifikasi, diperbaiki, dan dipahami oleh pengguna DIY.

Platform ATC saat ini berpusat pada:

- **STM32F411CEU6** sebagai USB HID / Force Feedback controller utama
- **STM32F103 / GD32F103 hoverboard controller** sebagai driver motor controller
- **Hoverboard hub motor** sebagai direct-drive motor
- **Local AB encoder** untuk posisi steering
- **ATC Control Center** untuk konfigurasi, telemetry, license, dan firmware update

> **Status: BETA**
>
> ATC masih dalam pengembangan aktif. Behavior firmware, konfigurasi, hardware support, dan prosedur update masih dapat berubah selama fase Beta.
>
> Project ini ditujukan untuk pengguna yang sudah cukup familiar dengan basic electronics, wiring, flashing STM32, dan troubleshooting hardware.

---

## Kenapa ATC?

ATC tidak dibuat untuk menjadi motor-control framework yang mendukung semua jenis hardware.

Project ini sengaja fokus pada beberapa hardware path saja supaya masing-masing bisa dituning dengan serius, bukan sekadar dibuat “bisa jalan”.

### Hoverboard Serial Torque

STM32F411 menangani USB HID, Force Feedback processing, wheel input, konfigurasi, dan high-rate torque scheduler.

STM32F103/GD32F103 pada hoverboard controller menangani motor FOC.

Torque dikirim secara digital melalui UART, bukan melalui analog/PWM torque signal.

### PWM Motor Driver

Firmware profile terpisah tersedia untuk pengguna yang memakai external PWM motor driver.

Mode yang tersedia antara lain:

- RC PPM
- 0–50–100% Centered PWM
- PWM + Direction
- Dual PWM

---

## Fitur Utama

### USB HID Force Feedback

ATC bekerja sebagai native USB HID Force Feedback device untuk game dan simulator yang menggunakan DirectInput.

FFB effect yang didukung termasuk:

- Constant Force
- Ramp
- Sine
- Square
- Triangle
- Sawtooth
- Spring
- Damper
- Friction
- Inertia

### Direct Drive Wheel Control

- Local quadrature encoder
- Configurable CPR
- Steering range
- Endstop
- Maximum torque
- Torque rate limit
- FFB filtering
- Idle spring
- Damper
- Friction
- Inertia
- Multi-band FFB processing

### Hoverboard Motor Control

- FOC torque mode
- High-rate Serial Torque
- Motor current / Iq telemetry
- Motor Kt based torque estimation
- Motor torque limit
- Torque filtering
- Torque scheduler / TX monitoring
- F103 firmware update melalui ATC Control Center

### Wheel Input

ATC mendukung konfigurasi untuk:

- Steering-wheel button
- Paddle shift
- H-shifter
- Throttle
- Brake
- Clutch
- Handbrake
- Analog input
- HX711 load-cell brake

---

## ATC Control Center

**ATC Control Center** adalah software Windows untuk konfigurasi hardware ATC.

Fitur utamanya:

- Device dashboard
- Force Feedback tuning
- Encoder configuration
- Button configuration
- Pedal configuration
- Hoverboard motor configuration
- PWM output configuration
- Live telemetry
- License status
- Firmware update
- Device save / reboot / recovery tools

Versi Windows didistribusikan sebagai **portable application**.

User tidak perlu install Python atau development toolchain.

---

## Hardware yang Didukung

### Main Controller

**STM32F411CEU6**

Support ATC F411 saat ini meliputi:

- USB Full Speed
- USB HID Force Feedback
- Local AB encoder
- Digital button
- Analog input
- HX711
- UART motor interface
- PWM motor interface

### Hoverboard Controller

Target utama:

- STM32F103 hoverboard board
- GD32F103 compatible board apabila didukung firmware motor yang digunakan

Hoverboard controller menjalankan motor-control firmware dan berkomunikasi dengan F411 melalui UART.

### Motor

Motor utama yang ditargetkan adalah BLDC hoverboard hub motor.

Setiap motor bisa memiliki torque constant, cogging, current limit, dan thermal behavior yang berbeda. Karena itu ATC Control Center menyediakan konfigurasi Motor Kt untuk estimasi torque.

---

## Firmware Profile

### `USER_HOVERBOARD`

Untuk F411 + hoverboard F103/GD32 motor controller.

Mencakup:

- Serial Torque
- Hoverboard telemetry
- Motor configuration
- F103 firmware updater
- User FFB configuration
- Wheel input dan pedal

### `USER_PWM`

Untuk F411 + external PWM motor driver.

Mencakup:

- PWM motor output
- PWM mode configuration
- User FFB configuration
- Wheel input dan pedal

Kedua profile sengaja dipisah. ATC tidak mencoba menyembunyikan dua arsitektur motor yang benar-benar berbeda di balik satu konfigurasi generik.

---

## License ATC

ATC menggunakan device-based licensing untuk komponen proprietary ATC.

### LOCKED

Device belum memiliki Trial atau Full license aktif.

Motor/FFB operation yang membutuhkan license dapat dinonaktifkan.

### TRIAL

Trial digunakan supaya user dapat mencoba experience ATC yang sebenarnya sebelum membeli Full license.

- **7 hari**
- User feature tersedia selama Trial
- Membutuhkan koneksi ke ATC license server
- Trial terikat pada device identity
- Reinstall GUI atau reflash firmware tidak dimaksudkan untuk mereset masa Trial

### FULL

Full License terikat pada MCU/device.

Setelah permanent activation berhasil:

- ATC dapat digunakan tanpa harus terus membuka ATC Control Center
- Normal use dapat berjalan offline
- Tidak memiliki expiry kecuali dinyatakan berbeda untuk tipe license tertentu

Full license untuk satu MCU tidak dimaksudkan untuk dipindahkan ke MCU lain.

---

## Getting Started

1. Download release terbaru dari **GitHub Releases**.
2. Pilih firmware F411:
   - `USER_HOVERBOARD`
   - `USER_PWM`
3. Flash initial firmware sesuai installation/recovery guide.
4. Extract **ATC Control Center Portable**.
5. Jalankan `ATC Control Center.exe`.
6. Connect ATC controller melalui USB.
7. Configure encoder, motor, button, pedal, dan FFB.
8. Save configuration.
9. Test pertama dengan torque limit rendah.

Panduan wiring dan instalasi lengkap akan tersedia di repository ini.

---

## Firmware Update dan Recovery

ATC menggunakan resident bootloader untuk mempermudah firmware update berikutnya.

Initial installation mungkin masih membutuhkan external programmer.

Setelah resident bootloader terpasang, firmware update yang didukung dapat dilakukan melalui ATC tools tanpa mengulang full development flashing.

Recovery guide dipisah karena prosedurnya berbeda untuk:

- STM32F411 main controller
- STM32F103/GD32F103 motor controller

---

## Safety

Direct Drive wheel dapat menghasilkan torque yang cukup besar untuk menyebabkan cedera atau kerusakan mekanik.

Sebelum menaikkan torque:

- Pastikan motor dan wheel terpasang kuat
- Gunakan wiring yang sesuai dengan arus
- Gunakan fuse / electrical protection yang sesuai
- Sediakan emergency stop jika memungkinkan
- Pastikan steering direction dan endstop benar
- Mulai dari torque limit rendah
- Jauhkan tangan, kabel, dan benda lepas dari bagian yang bergerak
- Jangan gunakan hardware dengan wiring atau isolasi yang belum diyakini aman

User bertanggung jawab terhadap hardware yang dirakit dan limit yang dikonfigurasi.

---

### Komponen ATC

ATC Control Center, ATC licensing components, server-side components, asset original ATC, dan komponen proprietary lain mengikuti **ATC End-User License Agreement (EULA)** kecuali dinyatakan berbeda.

Commercial use untuk komponen proprietary ATC membutuhkan izin atau commercial license terpisah.


---

## Personal dan Commercial Use

### Personal / DIY

Personal, non-commercial use untuk komponen proprietary ATC diperbolehkan sesuai ATC EULA.

### Commercial

Commercial use termasuk misalnya:

- Menjual wheelbase ATC rakitan
- Menjual kit ATC komersial
- OEM integration
- Bundling proprietary ATC software dengan produk berbayar
- Instalasi atau jasa simulator komersial yang menggunakan komponen proprietary ATC

Commercial use membutuhkan izin atau license terpisah.

---

## Community

ATC menggunakan beberapa channel sederhana:

- **GitHub Releases** — firmware dan build ATC Control Center
- **GitHub Issues** — bug yang dapat direproduksi dan technical problem
- **GitHub Discussions** — diskusi teknis dan feature idea
- **Discord** — diskusi cepat, setup help, dan komunitas lokal

Official link akan ditambahkan ketika public Beta dibuka.

---

## Disclaimer

ATC disediakan **AS IS**, tanpa warranty, sejauh diizinkan oleh hukum yang berlaku.

Tidak ada jaminan bahwa:

- semua board revision kompatibel
- semua hoverboard motor memiliki behavior yang sama
- semua game menghasilkan FFB yang identik
- setiap Beta release kompatibel dengan konfigurasi Beta sebelumnya
- software atau firmware bebas dari bug

Developer tidak bertanggung jawab terhadap kerusakan akibat wiring yang salah, hardware yang tidak sesuai, konfigurasi yang tidak aman, atau penggunaan yang tidak semestinya sejauh diizinkan oleh hukum yang berlaku.

---

### Build it. Tune it. Drive it.
