# mtk-su

Jadi bukan rahasia besar yang belum lama ini, saya menemukan cara untuk mencapai root sementara pada chipset MediaTek. Tidak diperlukan solusi root atau unlock perangkat yang sudah diinstal sebelumnya. Alat yang saya buat, MTK-SU, mengambil keuntungan dari kelemahan dalam desain perangkat lunak Mediatek. Awalnya ditujukan untuk membantu pemilik Amazon Fire HD dengan mudah melakukan root dan membuka kunci tablet mereka. (Tanpa itu, sebagian besar model memerlukan mod perangkat keras untuk mencapai root & unlock. Alat ini membuat rooting dapat diakses berkali-kali lipat dari jumlah pemilik. Ini juga memungkinkan untuk me-root Fire TV gen 2.) Tapi cerita lucu: metode ini sebenarnya bekerja pada hampir semua chip 64-bit MediaTek. Banyak perangkat dari berbagai vendor telah dikonfirmasi.

Jadi kalau tidak jelas, apa yang dilakukan mtk-su adalah memberi Anda shell root untuk dilakukan sesuka Anda. Ini seperti menjalankan 'su', tetapi tanpa perlu menginstal su. Itu mungkin cawan suci untuk perangkat yang terkunci. Pada beberapa perangkat, dimungkinkan untuk menginstal pengelola root untuk root permanen menggunakan mtk-su sebagai loncatan.

The original thread is here: [Rapid Temporary Root for HD8 & HD10.](https://forum.xda-developers.com/hd8-hd10/orig-development/experimental-software-root-hd-8-hd-10-t3904595) Ini adalah sumber informasi yang bagus. Tapi tolong hindari posting di sana tentang perangkat non-Amazon. Utas baru ini adalah topik pembicaraan umum untuk perangkat dan vendor lain.

## DISCLAIMER
Apa pun yang Anda lakukan yang dijelaskan di utas ini adalah risiko Anda sendiri. Tidak ada orang lain yang bertanggung jawab atas kehilangan data, atau kerusakan perangkat Anda, termasuk yang disebabkan oleh bug dalam perangkat lunak ini. Ada kemungkinan bukan nonzero dari peristiwa ini terjadi sebagai akibat dari menggunakan alat atau metode di sini.

## REQUIREMENTS
Mastery of the Thanks button under XDA posts
A phone, tablet or TV box based on Mediatek MT67xx, MT816x or MT817x chipsets
Either:

    A PC with ADB installed to interact with your device, or
    A terminal emulator app
Keakraban dengan ADB (jika menggunakan PC) dan perintah shell Linux dasar
Anda setuju untuk memposting nama model perangkat yang belum dikonfirmasi yang menjalankan mtk-su dengan sukses.

## INSTRUCTIONS FOR ADB
Pastikan Anda memenuhi semua persyaratan yang tercantum di atas, terutama 2 yang terakhir.
Unduh file zip `mtk-su` saat ini ke PC Anda dan unzip. Di dalamnya akan ada 2 direktori: `arm` & `arm64` dengan biner `mtk-su` di masing-masing. Pilih satu untuk perangkat Anda. Perbedaan antara:
`arm64: 64-bit` kernel dan userspace
`arm`: userspace 32-bit pada kernel 64-bit atau 32-bit (juga akan bekerja di userspace 64-bit)
Hubungkan perangkat Anda ke ADB dan pindahkan mtk-su ke folder `/data/lokal/tmp` Anda.
```bash
adb push path/to/mtk-su /data/local/tmp/
```
### Open an adb shell
```bash
adb shell
```
### open dir tmp
```bash
cd /data/local/tmp
```
### permesions mtk-su
```
chmod 755 mtk-su
```
### run mtk-su
```
./mtk-su
```
Seharusnya hanya butuh satu atau dua detik. Jika program macet selama lebih dari beberapa detik , tekan Ctrl + C untuk menutupnya.
Opsi -v mengaktifkan output verbose, yang penting bagi saya untuk men-debug masalah apa pun.
Output dari ./mtk-su -v mirip dengan ini:
```
$ ./mtk-su -v
param1: 0x3000, param2: 0x18040, type: 2
Building symbol table
kallsyms_addresses pa 0x40bdd500
kallsyms_num_syms 70337, addr_count 70337
kallsyms_names pa 0x40c66d00, size 862960
kallsyms_markers pa 0x40d39800
kallsyms_token_table pa 0x40d3a100
kallsyms_token_index pa 0x40d3a500
Patching credentials
Parsing current_is_single_threaded
ffffffc000354868+50: ADRP x0, 0xffffffc000fa2000
ffffffc000354868+54: ADD xd, x0, 2592
init_task VA: 0xffffffc000fa2a20
Potential list_head tasks at offset 0x340
comm swapper/0 at offset 0x5c0
Found own task_struct at node 1
cred VA: 0xffffffc0358ac0c0
Parsing avc_denied
ffffffc0002f13bc+24: ADRP x0, 0xffffffc001113000
ffffffc0002f13bc+28: LDR [x0, 404]
selinux_enforcing VA: 0xffffffc001113194
Setting selinux_enforcing
Switched selinux to permissive
starting /system/bin/sh
UID: 0  cap: 3fffffffff  selinux: permissive
#
```
## Some other options:
    mtk-su -c <command>: Runs <command> as root. Default command is /system/bin/sh.

    mtk-su -s: Prints the kernel symbol table

    mtk-su -Z <context>: Runs shell in a new selinux context. Example: ./mtk-su -Z u:r:logd:s0
Jika Anda melihat kesalahan atau tidak mendapatkan shell root, laporkan di sini. Saat melaporkan masalah dengan perangkat, silakan kirim tautan ke firmware dan / atau sumber kernel.
Silakan kirim model perangkat apa pun yang bekerja dengan mtk-su yang belum dikonfirmasi.

### Penting: 
dalam kasus yang jarang terjadi, mungkin perlu menjalankan alat beberapa kali sebelum Anda menekan UID 0 dan mendapatkan selinux permisif. Jika Anda tidak mencapai root pada proses tertentu, baris "UID: N cap: xxxxx ...." akan mencerminkan hal itu. Jika tidak tertulis "UID: 0 cap: 3fffffffff selinux: permissive", ketikkan exit untuk menutup subshell dan coba mtk-su lagi.

# WARNING
Jika Anda memiliki perangkat dengan Android 6 atau lebih tinggi, kemungkinan dm-verity diaktifkan. Pada perangkat seperti itu orang tidak hanya mengount ulang partisi sistem sebagai read/write. Perintah remount mungkin akan gagal. Tetapi jika Anda berhasil memaksakannya entah bagaimana itu akan memicu [dm-verity](https://source.android.com/security/verifiedboot/dm-verity), yang akan menghasilkan hari yang sangat buruk. Perangkat Anda akan menjadi tidak dapat dioperasikan sampai Anda mengembalikan partisi sistem persediaan.

Release 20 - Dec 28, 2019

    Add support for MT6580
    Add support for some MT8183 versions
    Fix handling of some 32-bit 4.x kernels with stack protection
    Move to NDK build


Release 19 - October 20, 2019

    Add -Z option for setting custom selinux context
    Fix seccomp on armv7
    Fix seccomp handling on late-revision 3.18 kernels
    Improve error printing for critical failures
    Strip supplementary groups in root shell
    Do not spawn root shell on critical failures


Release 18 - July 29, 2019

    Add support for kernel address space layout randomization (KASLR)
    Change status output format


Release 17 - July 13, 2019

    Fix missing capabilities under adb shell in Android 9.x
    Disable seccomp in app mode of Android 9.x
    Add support for MT6771 on Android 8.x
    Reliability improvements


Release 16 - June 9, 2019

    Add support for 32 & 64-bit kernels compiled with CONFIG_KALLSYMS_BASE_RELATIVE
    Add support for MT676x on Android 7.x
    Speedups


Release 15 - May 29, 2019

    Run shell/command in global mount namespace -- mounting from apps is now visible to the whole system


Release 14 - May 22, 2019

    Remove restriction for adb shell initial run on Android 8.0+
    Add support for 32-bit kernels compiled under Android 8.0+
    Add initial support for MT6771 on Android 9+
    Minor bug fixes


Release 13 - May 16, 2019

    Improve stack protection detection -- add support for some armv7-kernel 3.x phones


Release 12 - April 26, 2019

    Unify the arm and armv7-kernel binaries into one
    Support Linux 4.9.x
    Improve speed and possibly reliability
    Fix arm64 support for phones on kernel 3.10.65
    Fix stack protection workaround for armv7 kernels
    Update readme file


Release 11 - April 10, 2019

    Fix up and enable rooting for 32-bit kernels -- first such device confirmed (thanks @anthonykb)
    Improve criteria for detecting strong stack protection


Release 10 - April 7, 2019

    Fix support for the latest Oreo devices
    Add compatibility for kernels with stack protection (Nokia phones)
    Improve reliability
    Initial support for 32-bit (armv7) kernels -- needs testing


Release 9 - April 1, 2019

    Confirmed support for at least some Oreo devices
    Fix bugs with R8


Release 8 - March 30, 2019 (REMOVED)

    Lay the groundwork for Oreo devices
    Improve performance
    Improve reliability


Release 7 - March 17, 2019

    Add/fix support for many Linux ver. ≤ 3.18.22 devices
    Fix arm binary on Fire HD 10


Release 6 - March 13, 2019

    Add support for some devices with kernel 4.4.x (MT8167 confirmed by @cybersaga)
    Minor bug fixes


Release 5 - March 7, 2019

    Support kernels with CONFIG_KALLSYMS_ALL disabled
    Improve reliability


Release 4 - March 4, 2019

    Improve compatibility with phones
    Support Fire TV 2 new FW
    Minor bug fixes
    Improve reliability


Release 3 - March 1, 2019

    Add support for HD 10 7th gen
    Add support for 3.10 kernel layout
    Add possible support for MT67xx phones
    Improve reliability


Release 2 - Feb. 27, 2019

    Add support for HD 8 8th gen and 32-bit only user stacks

## FAQ
***Saya mendapat pesan "Gagal kritis init ... Platform ini tidak dapat (atau tidak) didukung pesan. Apa yang saya lakukan?***
Ini berarti firmware perangkat Anda tidak rentan terhadap mekanisme yang digunakan oleh mtk-su. Ini mungkin perangkat baru atau mungkin sudah dimulai dari pembaruan firmware. Tidak mungkin untuk menambahkan dukungan root untuk versi firmware saat ini atau di masa depan. Periksa versi firmware yang didukung terakhir di pos 4. Jika FW yang berfungsi terakhir tidak terdaftar dan perangkat Anda digunakan untuk bekerja dengan mtk-su, tolong laporkan versi kerja terakhir dan / atau versi Anda saat ini.

**Apakah ini akan berfungsi di ponsel saya?**
Ya, itu akan berfungsi pada ponsel Anda, kecuali jika tidak. Tetapi untuk serius, tidak ada gunanya mengajukan pertanyaan ini. Jika Anda memiliki perangkat di tangan, jauh lebih cepat untuk hanya mencoba prosedur di atas daripada menunggu jawaban. Anda biasanya orang terbaik untuk menjawab pertanyaan itu. Jika perangkat Anda terdaftar di antara model yang dikonfirmasi atau, pada tingkat lebih rendah, chipset Anda didukung, itu indikasi yang baik bahwa mtk-su akan berhasil, tetapi itu tidak dijamin. Anda harus melaporkan keberhasilan atau kegagalan Anda di utas ini, bersama dengan materi yang diminta jika gagal.

# Confirmed Devices
```
Acer Iconia One 10 B3-A30
Acer Iconia One 10 B3-A40
Alba tablet series
Alcatel 1 5033 series
Alcatel 1C
Alcatel 3L (2018) 5034 series
Alcatel 3T 8
Alcatel A5 LED 5085 series
Alcatel A30 5049 series
Alcatel Idol 5
Alcatel/TCL LX A502DL
Alcatel Tetra 5041C
Amazon Fire 7 2019 -- up to Fire OS 6.3.1.2 build 0002517050244 only
Amazon Fire HD 8 2016 -- up to Fire OS 5.3.6.4 build 626533320 only
Amazon Fire HD 8 2017 -- up to Fire OS 5.6.4.0 build 636558520 only
Amazon Fire HD 8 2018 -- up to Fire OS 6.3.0.1 only
Amazon Fire HD 10 2017 -- up to Fire OS 5.6.4.0 build 636558520 only
Amazon Fire HD 10 2019 -- up to Fire OS 7.3.1.0 only
Amazon Fire TV 2 -- up to Fire OS 5.2.6.9 only
ASUS ZenFone Max Plus X018D
ASUS ZenPad 3s 10 Z500M
ASUS ZenPad Z3xxM(F) MT8163-based series
Barnes & Noble NOOK Tablet 7" BNTV450 & BNTV460
Barnes & Noble NOOK Tablet 10.1" BNTV650
Blackview A8 Max
Blackview BV9600 Pro (Helio P60)
BLU Life Max
BLU Life One X
BLU R1 series
BLU R2 LTE
BLU S1
BLU Tank Xtreme Pro
BLU Vivo 8L
BLU Vivo XI
BLU Vivo XL4
Bluboo S8
BQ Aquaris M8
CAT S41
Coolpad Cool Play 8 Lite
Dragon Touch K10
Echo Feeling
Gionee M7
Huawei GR3 TAG-L21
Huawei Y5II
Huawei Y6II MT6735 series
Lava Iris 88S
Lenovo Tab E8
Lenovo A7000
Lenovo Tab2 A10-70F
LG K8+ (2018) X210ULMA (MTK)
LG K10 (2017)
LG Tribute Dynasty
LG X power 2/M320 series (MTK)
LG Xpression Plus 2/K40 LMX420 series
Lumigon T3
Meizu M5c
Meizu M6
Meizu Pro 7 Plus
Nokia 1
Nokia 1 Plus
Nokia 3
Nokia 3.1
Nokia 3.1 Plus
Nokia 5.1
Nokia 5.1 Plus/X5
Onn 7" Android tablet
Onn 8" & 10" tablet series (MT8163)
Oppo A5s
Oppo F5 series/A73 -- Android 8.x only
Oppo F7 series -- Android 8.x only
Oppo F9 series -- Android 8.x only
Oukitel K12
Protruly D7
Realme 1
Sony Xperia C4
Sony Xperia C5 series
Sony Xperia L1
Sony Xperia L3
Sony Xperia XA series
Sony Xperia XA1 series
Southern Telecom Smartab ST1009X (MT8167)
TECNO Spark 3 series
Umidigi F1 series
Umidigi Power
Wiko Ride
Wiko Sunny
Wiko View3
Xiaomi Redmi 6/6A series
ZTE Blade A530
ZTE Blade D6/V6
ZTE Quest 5 Z3351S
```
## Support Problematic*
```
Most/all Vivo phones
Most/all Huawei/Honor models with Android 8+
Most Oppo phones in app mode
Oppo F11 -- up to CPH1911EX_11_A.22 only
Most/all Samsung MTK-based phones
```
## Supported Chipsets
Including, but not limited to: MT6735, MT6737, MT6738, MT6739, MT6750, MT6753, MT6755, MT6757, MT6758, MT6761, MT6762, MT6763, MT6765, MT6771, MT6779, MT6795, MT6797, MT6799, MT8163, MT8167, MT8173, MT8176, MT8183, MT6580, MT6595

* Perangkat ini biasanya menggunakan modifikasi kernel untuk mencegah akses root melalui eksploitasi. Tetapi metode temp root ini masih dapat mencapai root pada sebagian besar model ini secara teori. Namun, saya tidak akan menambahkan dukungan untuk kernel non-standar dalam versi rilis utama. Versi khusus mtk-su dapat dibuat untuk menangani kernel yang dilindungi dalam firmware tertentu. Ini bukan sesuatu yang biasanya saya termotivasi untuk melakukannya.

# SUMBER: [XDA](https://forum.xda-developers.com/android/development/amazing-temp-root-mediatek-armv8-t3922213)
