[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/Eu-CByJh)
|    NRP     |      Name      |
| :--------: | :------------: |
| 5025241179 | Erlangga Rizqi Dwi Raswanto |
| 5025241193  | Farrel Jatmiko Aji |
| 5025241220 | Davin Adiputra Suryolaksana |

# Praktikum Modul 3 _(Module 3 Lab Work)_

### Laporan Resmi Praktikum Modul 3 _(Module 3 Lab Work Report)_

Di suatu pagi hari yang cerah, Budiman salah satu mahasiswa Informatika ditugaskan oleh dosennya untuk membuat suatu sistem operasi sederhana. Akan tetapi karena Budiman memiliki keterbatasan, Ia meminta tolong kepadamu untuk membantunya dalam mengerjakan tugasnya. Bantulah Budiman untuk membuat sistem operasi sederhana!

_One sunny morning, Budiman, an Informatics student, was assigned by his lecturer to create a simple operating system. However, due to Budiman's limitations, he asks for your help to assist him in completing his assignment. Help Budiman create a simple operating system!_

### Soal 1

> Sebelum membuat sistem operasi, Budiman diberitahu dosennya bahwa Ia harus melakukan beberapa tahap terlebih dahulu. Tahap-tahapan yang dimaksud adalah untuk **mempersiapkan seluruh prasyarat** dan **melakukan instalasi-instalasi** sebelum membuat sistem operasi. Lakukan seluruh tahapan prasyarat hingga [perintah ini](https://github.com/arsitektur-jaringan-komputer/Modul-Sisop/blob/master/Modul3/README-ID.md#:~:text=sudo%20apt%20install%20%2Dy%20busybox%2Dstatic) pada modul!

> _Before creating the OS, Budiman was informed by his lecturer that he must complete several steps first. The steps include **preparing all prerequisites** and **installing** before creating the OS. Complete all the prerequisite steps up to [this command](https://github.com/arsitektur-jaringan-komputer/Modul-Sisop/blob/master/Modul3/README-ID.md#:~:text=sudo%20apt%20install%20%2Dy%20busybox%2Dstatic) in the module!_

**Answer:**

- **Code:**
  
  `1. Update & Install software:`
    ```bash
    sudo apt -y update
    sudo apt -y install qemu-system build-essential bison flex libelf-dev libssl-dev bc grub-common grub-pc libncurses-dev libssl-dev mtools grub-pc-bin xorriso tmux
    ```
  `2. Buat direktori osboot`
    ```bash
    mkdir -p osboot
    cd osboot
    ```
  `3. Download dan ekstrak kernel linux`
    ```bash
    wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.1.1.tar.xz
    tar -xvf linux-6.1.1.tar.xz
    cd linux-6.1.1
    ```
  `4. Kommpilasi Kernel`
    ```bash
    make -j$(nproc)
    ```
  `5. Copy file bzImage ke /osboot`
    ```bash
     cp arch/x86/boot/bzImage ..
    ```
  `6. Install BusyBox`
    ```bash
    sudo apt install -y busybox-static
    ```
- **Explanation:**

  - Persiapkan kernel Linux  
    1. Update dan install software yang dibutuhkan seperti qemu, build-essential, bison, flex, dan lainnya menggunakan kode `1`.
    2. Membuat direktori untuk pembuatan dan konfigurasi kernel menggunakan kode `2`. Disini nama direktorinya adalah `osboot`. Hasilnya ada di screenshot `i`.
    3. Download dan ekstrak kernel linux menggunakan kode `3` (`wget` untuk donwload lalu ekstrak dengan `tar -xvf (-x untuk extract)` kemudian pindah dir ke folder hasil ekstrak dengan `cd`).
    4. Lalu configurasi kernel dengan `make tinyconfig` dan `make menuconfig`. Hasil config akan tersimpah di file `.config`. Screenshot `iii` dan `iv`.
    5. Kompilasi kernel (kode `4`) agar menghasilkan file `bzImage` (file kernel yang digunakan untuk booting). Hasilnya dapat dilihat di screenshot `v`
    6. File `bzImage` akan terbuat di folder  `arch/x86/boot/`, kita copy file tersebut ke folder `osboot` dengan kode `5`. screenshot `vi`
    7. Terakhir, install `BusyBox` dengan kode `6`. screenshot `vii`
  

- **Screenshot:**

  1. Hasil `mkdir osboot`
     
     ![image](https://github.com/user-attachments/assets/8d9fb6ee-7ba2-4697-91de-e8cd14206078)
  2. Hasil `Download dan ekstrak kernel linux`
     
     ![image](https://github.com/user-attachments/assets/6f138adb-88e4-4ef0-8cea-e73a7252f86d)
  3. Hasil `make tinyconfig`
     
     ![image](https://github.com/user-attachments/assets/b36a6ba0-7368-4f6a-9859-f2a070ed3dd2)
  4. Tampilan dari `make menuconfig`
     
     ![image](https://github.com/user-attachments/assets/52115f49-2a20-4f09-bfe4-fc180198f39b)
  5. file `bzImage`
     
     ![image](https://github.com/user-attachments/assets/ed2b7b22-8d68-4a21-8b30-2e85e2465e25)
  6. Copy `bzImage`
     
     ![image](https://github.com/user-attachments/assets/a23d4edf-09fd-48e3-b8c3-89fde0398d75)
  7. Install `BusyBox`
      
     ![image](https://github.com/user-attachments/assets/780021d8-50af-4bce-add6-7b189bee52da)



   

     


### Soal 2

> Setelah seluruh prasyarat siap, Budiman siap untuk membuat sistem operasinya. Dosen meminta untuk sistem operasi Budiman harus memiliki directory **bin, dev, proc, sys, tmp,** dan **sisop**. Lagi-lagi Budiman meminta bantuanmu. Bantulah Ia dalam membuat directory tersebut!

> _Once all prerequisites are ready, Budiman is ready to create his OS. The lecturer asks that the OS should contain the directories **bin, dev, proc, sys, tmp,** and **sisop**. Help Budiman create these directories!_

**Answer:**

- **Code:**

  1. Superuser
     ```bash
     sudo bash
     ```
  2. Buat directory `bin`, `dev`, `proc`, `sys`, `tmp` dan `sisop`.
     ```bash
     mkdir -p myramdisk/{bin,dev,proc,sys,tmp,sisop}
     ```
  3. Copy file device ke dir `dev`
     ```bash
     cp -a /dev/null myramdisk/dev
     cp -a /dev/tty* myramdisk/dev
     cp -a /dev/zero myramdisk/dev
     cp -a /dev/console myramdisk/dev
     ```
  4. Copy BusyBox ke dir `bin`
     ```bash
     cp /usr/bin/busybox myramdisk/bin
     cd myramdisk/bin
     ./busybox --install .
     ```

- **Explanation:**
  
  Sebelum membuat direktori, Budiman harus menjadikan dirinya sebagai superuser dengan kode `i`. Setelah itu, Budiman membuat directory yang diminta dosen menggunakan kode `ii` dengan hasil pada screenshot `a`. Kemudian Budiman copy file dari host yang diperlukan ke `/myramdisk/dev/` (kode `iii`, hasil `b`). Lalu Budiman copy file lain dari host yang diperlukan ke `/myramdisk/bin/` (kode `iv`, hasil `c`).

- **Screenshot:**

  a. Isi dari dir `/myramdisk`

     ![image](https://github.com/user-attachments/assets/c2203627-56fe-4029-af03-b23b97d093f9)

  b. Isi dari dir `myramdisk/dev`

     ![image](https://github.com/user-attachments/assets/e9daf89f-339e-4ea1-a083-f0df71dc372e)
  c. Isi dari dir `myramdisk/bin`

     ![image](https://github.com/user-attachments/assets/e044aefb-cc8c-4f3c-af5e-93cf17a3a4ce)


 

### Soal 3

> Budiman lupa, Ia harus membuat sistem operasi ini dengan sistem **Multi User** sesuai permintaan Dosennya. Ia meminta kembali kepadamu untuk membantunya membuat beberapa user beserta directory tiap usernya dibawah directory `home`. Buat pula password tiap user-usernya dan aplikasikan dalam sistem operasi tersebut!

> _Budiman forgot that he needs to create a **Multi User** system as requested by the lecturer. He asks your help again to create several users and their corresponding home directories under the `home` directory. Also set each user's password and apply them in the OS!_

**Format:** `user:pass`

```
root:Iniroot
Budiman:PassBudi
guest:guest
praktikan1:praktikan1
praktikan2:praktikan2
```

**Answer:**

- **Code:**
  1. buat dir keperluan
     ```bash
     mkdir -p myramdisk/{etc,root,home/Budiman,home/guest,home/praktikan1,home/praktikan2}
     ```
  2. Generate hash password dengan `openssl`
     ```bash
     openssl passwd -1 Iniroot
     openssl passwd -1 PassBudi
     openssl passwd -1 guest
     openssl passwd -1 praktikan1
     openssl passwd -1 praktikan2
     ```
  3. Buat file `passwd` di `etc` lalu isi:
     ```bash
     cd myramdisk/etc
     echo "root:\$1\$VrKjJwl2\$GoRr1PHLZJrxD91PgFqWv0:0:0:root:/root:/bin/sh" > passwd
     echo "Budiman:\$1\$3/4J9lkC\$Mwr3jl1T0kso90lLVw4ol1:1001:100:Budiman:/home/Budiman:/bin/sh" >> passwd
     echo "guest:\$1\$EO101Cv8\$VFfsPM1dgIYQyRGNWiPVs0:1002:100:guest:/home/guest:/bin/sh" >> passwd
     echo "praktikan1:\$1\$iXR66due\$/DJkE5rPMhD.fRe5tHJG70:1003:100:praktikan1:/home/praktikan1:/bin/sh" >> passwd
     echo "praktikan2:\$1\$Kw5Co11C\$Ok1l5W8hOgDfZlOPFwt0x.:1004:100:praktikan2:/home/praktikan2:/bin/sh" >> passwd
     ```
     

- **Explanation:**

  Buat directory-directory yang diperlukan (`etc`,`root`,`home/Budiman`,`home/guest`,`home/praktikan1`,`home/praktikan2`) dengan kode `i` (hasil: screenshot `a`) lalu generate hash password yang diberi di soal dengan openssl (kode `ii` hasil `b`) hasilnya yaitu:
  ```
  $1$VrKjJwl2$GoRr1PHLZJrxD91PgFqWv0
  $1$3/4J9lkC$Mwr3jl1T0kso90lLVw4ol1
  $1$EO101Cv8$VFfsPM1dgIYQyRGNWiPVs0
  $1$iXR66due$/DJkE5rPMhD.fRe5tHJG70
  $1$Kw5Co11C$Ok1l5W8hOgDfZlOPFwt0x.
  ```
  Setelah itu, masukkan hash yang sudah di generate tadi ke file `passwd`. Pertama kita pindah direktori dulu ke dalam folder `etc` dengan `cd myramdisk/etc` lalu echo setiap baris hash (gunakan '\' untuk setiap '$' agar escape). Hasil : screenshot `c`

- **Screenshot:**

  a. isi dari dir `myramdisk` dan `home`

  ![image](https://github.com/user-attachments/assets/f71a65ba-e9c3-4d27-aa70-7bc2b48c5077)
  
  b. Hasil generate password

  ![image](https://github.com/user-attachments/assets/ca068444-9dde-45d9-87a9-d53926c5ee0b)

  
  c. Kode dan Hasil buat file `passwd`

  ![image](https://github.com/user-attachments/assets/ea080cc2-6df5-4e66-918a-e58ebc7caf53)





### Soal 4

> Dosen meminta Budiman membuat sistem operasi ini memilki **superuser** layaknya sistem operasi pada umumnya. User root yang sudah kamu buat sebelumnya akan digunakan sebagai superuser dalam sistem operasi milik Budiman. Superuser yang dimaksud adalah user dengan otoritas penuh yang dapat mengakses seluruhnya. Akan tetapi user lain tidak boleh memiliki otoritas yang sama. Dengan begitu user-user selain root tidak boleh mengakses `./root`. Buatlah sehingga tiap user selain superuser tidak dapat mengakses `./root`!

> _The lecturer requests that the OS must have a **superuser** just like other operating systems. The root user created earlier will serve as the superuser in Budiman's OS. The superuser should have full authority to access everything. However, other users should not have the same authority. Therefore, users other than root should not be able to access `./root`. Implement this so that non-superuser accounts cannot access `./root`!_

**Answer:**

- **Code:**
  Kode 1:
  ```bash
  chown 0:0 myramdisk/root
  chmod 700 myramdisk/root
  ```
  Kode 2:
  ```bash
  cd myramdisk/etc
  echo "root:x:0:root" > group
  echo "users:x:100:Budiman,guest,praktikan1,praktikan2" >> group
  ```

- **Explanation:**
  
  Kode 1 : `chown` (change ownership) `myramdisk/root` dengan `0:0` yang yaitu UID 0 dan GID 0 (root). Lalu `chmod` (change mode), root dengan 700. Penjelasan 700:
  
  |owner|group|others|
  |:-:|:-:|:-:|
  |7|0|0|
  |Bisa read, write, execute|tidak bisa apa-apa|tidak bisa apa-apa|
  
  Kode 2 : Selanjutnya buat file `group` di direktori `etc` untuk mengatur otoritas tiap usernya di dalam group. Pertama, pindah ke dir `etc` di `myramdisk` dengan `cd myramdisk/etc` lalu isi 
  file group dengan:
  ```bash
  root:x:0:root
  users:x:100:Budiman,guest,praktikan1,praktikan2
  ```
- **Screenshot:**
  ![image](https://github.com/user-attachments/assets/dce322f6-048b-4147-b989-eb4c336c4b48)


### Soal 5

> Setiap user rencananya akan digunakan oleh satu orang tertentu. **Privasi dan otoritas tiap user** merupakan hal penting. Oleh karena itu, Budiman ingin membuat setiap user hanya bisa mengakses dirinya sendiri dan tidak bisa mengakses user lain. Buatlah sehingga sistem operasi Budiman dalam melakukan hal tersebut!

> _Each user is intended for an individual. **Privacy and authority** for each user are important. Therefore, Budiman wants to ensure that each user can only access their own files and not those of others. Implement this in Budiman's OS!_

**Answer:**

- **Code:**

```sh
chown 1001:100 myramdisk/home/Budiman
chmod 700 myramdisk/home/Budiman

chown 1002:100 myramdisk/home/guest
chmod 700 myramdisk/home/guest

chown 1003:100 myramdisk/home/praktikan1
chmod 700 myramdisk/home/praktikan1

chown 1004:100 myramdisk/home/praktikan2
chmod 700 myramdisk/home/praktikan2
```

- **Explanation:**

`chown UID:GID` -> mengatur ownership direktori

`chmod 700` -> mengatur izin akses ( 7 (full akses untuk owner (rwx)), 0 (no access untuk grup), 0 (no access untuk other) )

| Direktori          | Pemilik (UID\:GID) | Permission |
| ------------------ | ------------------ | ---------- |
| `/home/Budiman`    | 1001:100           | `700`      |
| `/home/guest`      | 1002:100           | `700`      |
| `/home/praktikan1` | 1003:100           | `700`      |
| `/home/praktikan2` | 1004:100           | `700`      |

Dengan ini, hanya pemilik direktori yang bisa masuk ke direktori, melihat isi, membuat/menyimpan file. Sedangkan user lain akan mendapat permission denied.

- **Screenshot:**

Run command :

![soal5_1!](https://i.imgur.com/cLqaay6.png)


Melihat permission dan ownership menggunakan `ls -l`

![soal5_2!](https://i.imgur.com/eD7zhH2.png)


Point of view jika dari user `guest` (mencoba akses direktori yang bukan miliknya)

![soal5_3!](https://i.imgur.com/TRJsutK.png)



### Soal 6

> Dosen Budiman menginginkan sistem operasi yang **stylish**. Budiman memiliki ide untuk membuat sistem operasinya menjadi stylish. Ia meminta kamu untuk menambahkan tampilan sebuah banner yang ditampilkan setelah suatu user login ke dalam sistem operasi Budiman. Banner yang diinginkan Budiman adalah tulisan `"Welcome to OS'25"` dalam bentuk **ASCII Art**. Buatkanlah banner tersebut supaya Budiman senang! (Hint: gunakan text to ASCII Art Generator)

> _Budiman wants a **stylish** operating system. Budiman has an idea to make his OS stylish. He asks you to add a banner that appears after a user logs in. The banner should say `"Welcome to OS'25"` in **ASCII Art**. Use a text to ASCII Art generator to make Budiman happy!_ (Hint: use a text to ASCII Art generator)

**Answer:**

- **Code:**

```sh
#!/bin/sh
clear

RED='\033[1;31m'
ORANGE='\033[38;5;208m'
YELLOW='\033[1;33m'
GREEN='\033[1;32m'
BLUE='\033[1;34m'
INDIGO='\033[38;5;54m'
VIOLET='\033[1;35m'
NC='\033[0m'

echo -e "${RED} █████   ███   █████          ████                                            "
echo -e "${ORANGE}░░███   ░███  ░░███          ░░███                                            "
echo -e "${YELLOW} ░███   ░███   ░███   ██████  ░███   ██████   ██████  █████████████    ██████ "
echo -e "${GREEN} ░███   ░███   ░███  ███░░███ ░███  ███░░███ ███░░███░░███░░███░░███  ███░░███"
echo -e "${BLUE} ░░███  █████  ███  ░███████  ░███ ░███ ░░░ ░███ ░███ ░███ ░███ ░███ ░███████ "
echo -e "${INDIGO}  ░░░█████░█████░   ░███░░░   ░███ ░███  ███░███ ░███ ░███ ░███ ░███ ░███░░░  "
echo -e "${VIOLET}    ░░███ ░░███     ░░██████  █████░░██████ ░░██████  █████░███ █████░░██████"
echo -e "${RED}     ░░░   ░░░       ░░░░░░  ░░░░░  ░░░░░░   ░░░░░░  ░░░░░ ░░░ ░░░░░  ░░░░░░  "
echo -e "${ORANGE}                                                                              "
echo -e "${YELLOW}                                                                              "
echo -e "${GREEN}                                                                              "
echo -e "${BLUE}  █████                   ███████     █████████   ██  ████████  ██████████    "
echo -e "${INDIGO} ░░███                  ███░░░░░███  ███░░░░░███ ███ ███░░░░███░███░░░░░░█    "
echo -e "${VIOLET} ███████    ██████     ███     ░░███░███    ░░░ ░░░ ░░░    ░███░███     ░     "
echo -e "${RED}░░░███░    ███░░███   ░███      ░███░░█████████        ███████ ░█████████     "
echo -e "${ORANGE}  ░███    ░███ ░███   ░███      ░███ ░░░░░░░░███      ███░░░░  ░░░░░░░░███    "
echo -e "${YELLOW}  ░███ ███░███ ░███   ░░███     ███  ███    ░███     ███      █ ███   ░███    "
echo -e "${GREEN}  ░░█████ ░░██████     ░░░███████░  ░░█████████     ░██████████░░████████     "
echo -e "${BLUE}   ░░░░░   ░░░░░░        ░░░░░░░     ░░░░░░░░░      ░░░░░░░░░░  ░░░░░░░░ ${NC}"
```

- **Explanation:**

1. Generate ASCII Art di [internet](https://www.asciiart.eu/text-to-ascii-art) (dengan font DOS Rebel)
2. Buat File Banner Login
   
   - Untuk menampilkan banner setiap user login dapat memanfaatkan file .
   `etc/profile`
   - File ini dijalankan setiap user login ke shell.

3. Copas ASCII Art ke file `profile`
   
4. Menambahkan warna untuk estetika
   
   Menggunakan ANSI escape code agar tampil berwarna. Warna dibuat seperti pelangi.


`clear`

Untuk membersihkan layar terminal sebelum banner.

`\033[...m`

adalah variabel warna terminal yang menggunakan ANSI escape codes.

`echo -e`

Mencetak teks ke layar, opsi `-e` digunakan agar escape character "ditafsirkan" bukan hanya dicetak sebagai teks biasa.


- **Screenshot:**

Membuat / Mengedit file `profile` menggunakan `gedit`

![soal6_1!](https://i.imgur.com/QsmIVyD.png)

Copy ASCII Art yang sudah dibuat di internet ke file ini, lalu menambahkan warna untuk estetika

![soal6_2!](https://i.imgur.com/PAxEQvq.png)

Contoh tampilan saat user sudah login

![soal6_3!](https://i.imgur.com/ZCmFEfV.png)

### Soal 7

> Melihat perkembangan sistem operasi milik Budiman, Dosen kagum dengan adanya banner yang telah kamu buat sebelumnya. Kemudian Dosen juga menginginkan sistem operasi Budiman untuk dapat menampilkan **kata sambutan** dengan menyebut nama user yang login. Sambutan yang dimaksud berupa kalimat `"Helloo %USER"` dengan `%USER` merupakan nama user yang sedang menggunakan sistem operasi. Kalimat sambutan ini ditampilkan setelah user login dan setelah banner. Budiman kembali lagi meminta bantuanmu dalam menambahkan fitur ini.

> _Seeing the progress of Budiman's OS, the lecturer is impressed with the banner you created. The lecturer also wants the OS to display a **greeting message** that includes the name of the user who logs in. The greeting should say `"Helloo %USER"` where `%USER` is the name of the user currently using the OS. This greeting should be displayed after user login and after the banner. Budiman asks for your help again to add this feature._

**Answer:**

- **Code:**

menambahkan di file `etc/profile`

```bin
...
echo -e "\nHelloo $USER\n"
```

- **Explanation:**
  
Untuk menyambut nama user setelah user login dan setelah banner, dapat menggunakan file `profile` yang sama pada no 6 dikarenakan file ini otomatis akan dijalan kan saat setiap user login ke shell.

`echo -e` : Mencetak teks ke terminal, opsi `-e` untuk mengaktifkan interpretasi karakter khusus seperti `\n`.

`$USER` : adalah environment variable yang berisi nama user yang sedang login.

- **Screenshot:**

  Penambahan kode di `etc/profile`
  
  ![soal7_1!](https://i.imgur.com/rxrw9UC.png)

  Tampilan jika user `Budiman` sedang login

  ![soal7_2!](https://i.imgur.com/jCkD8ZV.png)

  Tampilan jika user `guest` sedang login

  ![soal7_3!](https://i.imgur.com/fsuoxwO.png)

### Soal 8

> Dosen Budiman sudah tua sekali, sehingga beliau memiliki kesulitan untuk melihat tampilan terminal default. Budiman menginisiatif untuk membuat tampilan sistem operasi menjadi seperti terminal milikmu. Modifikasilah sistem operasi Budiman menjadi menggunakan tampilan terminal kalian.

> _Budiman's lecturer is quite old and has difficulty seeing the default terminal display. Budiman takes the initiative to make the OS look like your terminal. Modify Budiman's OS to use your terminal appearance!_

**Answer:**

- **Code:**

edit di `init`
```bin
...
echo "budimanOS" > /proc/sys/kernel/hostname

while true
do
    /bin/getty -L ttyS0 115200 vt100
    sleep 1
done
...
```

edit di `etc/profile`

```bin
...
PS1="\[\e]0;\u@\h: \w\a\]${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ "
```
---

### **Explanation**

###  **1. Mengubah tty dari `tty1` ke `ttyS0`**

Dengan mengubah `getty` dari:

```sh
/bin/getty -L tty1 115200 vt100
```

menjadi:

```sh
/bin/getty -L ttyS0 115200 vt100
```

Maka tampilan terminal sistem operasi akan ditampilkan melalui serial port (`ttyS0`), bukan layar langsung (`tty1`). Hal ini membuat tampilan sistem dapat diakses dari terminal eksternal

### **2. Edit hostname di file `init`**

`echo "budimanOS" > /proc/sys/kernel/hostname` -> akan mengatur hostname ke `budimanOS` saat booting

Hostname berfungsi sebagai nama identitas OS dan agar terlihat rapi saat diterminal


### **3. Mengubah Tampilan Prompt dengan PS1**

`PS1` adalah Prompt String 1,  yaitu variabel environment shell yang menentukan tampilan prompt setiap kali user mengetik perintah.

Nilai `PS1` diambil dari terminal milik praktikan (Linux host) lalu copy paste di file `etc/profile` 

Penjelasan PS1 yang digunakan :

```sh
PS1="\[\e]0;\u@\h: \w\a\]${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ "
```

#### **1. `\[\e]0;\u@\h: \w\a\]`**

* Ini bagian pengaturan judul terminal (window title).
* Hanya terlihat jika terminal mendukung GUI.
* Komponen:
  * `\e` → escape character (ASCII code 27)
  * `\u` → username
  * `\h` → hostname
  * `\w` → working directory (path direktori saat ini)
  * `\a` → bell character untuk mengakhiri escape sequence

#### **2. `${debian_chroot:+($debian_chroot)}`**

* Ini bagian akan menampilkan nama chroot (misalnya `(mychroot)`) jika environment variable `debian_chroot` terisi.
* Berguna jika shell dijalankan dalam container atau recovery mode.
* Kalau tidak dalam chroot, bagian ini tidak tampil sama sekali.

#### **3. `\[\033[01;32m\]\u@\h\[\033[00m\]`**

* Menampilkan teks `username@hostname` dalam warna hijau terang.
* Komponen:
  * `\033[01;32m` → ANSI escape code untuk bold + hijau terang
  * `\u@\h` → menampilkan `username@hostname`
  * `\033[00m` → mengembalikan warna ke default (reset)

#### **4. `:`**

* Tanda pemisah antara hostname dan path direktori.
* Digunakan agar prompt lebih mudah dibaca, contoh hasilnya:
  ```sh
  budiman@osbudiman:/home/budiman$
  ```

#### **5. `\[\033[01;34m\]\w\[\033[00m\]`**

* Menampilkan *irektori kerja saat ini (working directory) dalam warna biru terang.
* Komponen:
  * `\033[01;34m` → ANSI escape code untuk biru terang
  * `\w` → current working directory
  * `\033[00m` → reset warna kembali ke default

#### **6. `\$`**
* Menampilkan simbol akhir prompt:
  * `#` → untuk root
  * `$` → untuk user biasa
* Spasi di akhir membuat tampilan prompt lebih rapi dan terpisah dari perintah yang akan diketik.

### **4. Run QEMU**

Agar semua perubahan berfungsi dengan baik, harus menjalankan QEMU dengan parameter yang sesuai yaitu dengan :

```sh
qemu-system-x86_64 \
  -smp 2 \
  -m 256 \
  -nographic \
  -kernel bzImage \
  -initrd myramdisk.gz \
  -append "console=ttyS0"
```
yaitu dengan tambahan `-append "console=ttyS0"` untuk memberitahu kernel untuk menggunakan ttyS0 sebagai console output utama

- **Screenshot:**

Edit pada file `init`

![soal8_init!](https://i.imgur.com/i0VJV6l.png)


Edit pada file `etc/profile`

![soal8_ps1!](https://i.imgur.com/k4Hnsiw.png)


Contoh tampilan saat di run

![soal8_terminal!](https://i.imgur.com/24YBrGW.png)


### Soal 9

> Ketika mencoba sistem operasi buatanmu, Budiman tidak bisa mengubah text file menggunakan text editor. Budiman pun menyadari bahwa dalam sistem operasi yang kamu buat tidak memiliki text editor. Budimanpun menyuruhmu untuk menambahkan **binary** yang telah disiapkan sebelumnya ke dalam sistem operasinya. Buatlah sehingga sistem operasi Budiman memiliki **binary text editor** yang telah disiapkan!

> _When trying your OS, Budiman cannot edit text files using a text editor. He realizes that the OS you created does not have a text editor. Budiman asks you to add the prepared **binary** into his OS. Make sure Budiman's OS has the prepared **text editor binary**!_

**Answer:**

- **Code:**

  ```
  git clone https://github.com/morisab/budiman-text-editor.git
  cd budiman-text-editor
  ```
  
  `g++ -static main.cpp -o budimans`
  
  ```
  cp budimans ../bin/
  chmod +x ../bin/budimans
  ```

  ```
  cd ..
  find . | cpio -o -H newc | gzip > ../../myramdisk.gz
  ```

  
  

- **Explanation:**

1. Clone Repository

Pertama-tama, clone source code dari text editor ke dalam direktori kerja:
```
git clone https://github.com/morisab/budiman-text-editor.git
cd budiman-text-editor
```
2. Compile Binary Secara Statik

Binary default hasil g++ bersifat dinamis dan tidak kompatibel dengan initramfs yang tidak memiliki shared libraries. Oleh karena itu, binary perlu dikompilasi secara statik:
```
g++ -static main.cpp -o budimans
```
3. Pindahkan ke Direktori /bin

Salin binary budimans ke dalam direktori /bin pada struktur root filesystem initramfs:
```
cp budimans ../bin/
chmod +x ../bin/budimans
```
4. Repack initramfs

Setelah binary berhasil ditambahkan dan diberi hak eksekusi, repack initramfs untuk menghasilkan file myramdisk.gz baru:
```
cd ..
find . | cpio -o -H newc | gzip > ../../myramdisk.gz
```


- **Screenshot:**

  ![Screenshot from 2025-05-23 20-04-16](https://github.com/user-attachments/assets/760187d4-7577-4f0b-b484-ac0ab7773911)

  ![image](https://github.com/user-attachments/assets/1e52beda-1d10-42ca-a1a8-87dae851e266)

  ![image](https://github.com/user-attachments/assets/583c2431-87a7-4f94-9041-274487f7cbf1)

  ![image](https://github.com/user-attachments/assets/ddb4baf3-b622-490a-bd18-348088ddf173)

  ![image](https://github.com/user-attachments/assets/778443fd-0995-4fc1-ad3e-849450183690)

  ![image](https://github.com/user-attachments/assets/c40c0722-51f9-4fd5-b1fa-998015a9c4bc)

  ![image](https://github.com/user-attachments/assets/d3caf124-145d-4423-895c-39755e24f8f1)



### Soal 10

> Setelah seluruh fitur yang diminta Dosen dipenuhi dalam sistem operasi Budiman, sudah waktunya Budiman mengumpulkan tugasnya ini ke Dosen. Akan tetapi, Dosen Budiman tidak mau menerima pengumpulan selain dalam bentuk **.iso**. Untuk terakhir kalinya, Budiman meminta tolong kepadamu untuk mengubah seluruh konfigurasi sistem operasi yang telah kamu buat menjadi sebuah **file .iso**.

> After all the features requested by the lecturer have been implemented in Budiman's OS, it's time for Budiman to submit his assignment. However, Budiman's lecturer only accepts submissions in the form of **.iso** files. For the last time, Budiman asks for your help to convert the entire configuration of the OS you created into a **.iso file**.

**Answer:**

- **Code:**

  ```
  cd osboot
  mkdir -p mylinuxiso/boot/grub
  ```
  ```
  cp bzImage mylinuxiso/boot
  cp myramdisk.gz mylinuxiso/boot
  ```

  
  ```
  cd mylinuxiso/boot/grub
  nano grub.cfg
  ```
  isi `file grub.cfg` dengan kode dibawah
  ```
  set timeout=5
  set default=0
  
  menuentry "MyLinux" {
    linux /boot/bzImage
    initrd /boot/myramdisk.gz
  }
  ```
  ```
  cd ../../..
  grub-mkrescue -o mylinux.iso mylinuxiso
  ```

- **Explanation:**

  1. **Masuk ke direktori `osboot`**:

   ```bash
   cd osboot
   ```

2. **Buat struktur direktori ISO**:

   ```bash
   mkdir -p mylinuxiso/boot/grub
   ```

3. **Salin file kernel dan root filesystem**:

   ```bash
   cp bzImage mylinuxiso/boot
   cp myramdisk.gz mylinuxiso/boot
   ```

4. **Buat file konfigurasi GRUB**:
   Buat file `grub.cfg` di `mylinuxiso/boot/grub` dengan isi:

   ```cfg
   set timeout=5
   set default=0

   menuentry "MyLinux" {
     linux /boot/bzImage
     initrd /boot/myramdisk.gz
   }
   ```

   > File ini akan membuat menu GRUB yang menampilkan pilihan boot bernama "MyLinux", dan mengarahkan sistem untuk menggunakan kernel dan initrd yang sudah kita sediakan.

5. **Buat file ISO bootable**:
   Jalankan perintah berikut dari direktori `osboot`:
   ```bash
   grub-mkrescue -o mylinux.iso mylinuxiso
   ```

- **Screenshot:**

  ![image](https://github.com/user-attachments/assets/88305be6-cf25-452f-a838-3f9497e91fe2)

  ![image](https://github.com/user-attachments/assets/1e35e884-2fb8-4f54-92e6-2e933c5333f0)

  ![image](https://github.com/user-attachments/assets/9a532e49-8caa-416e-b581-a706c8dd63c1)

  ![image](https://github.com/user-attachments/assets/e5aa0fe3-128e-4738-8e6e-f2bf2954a885)


---

Pada akhirnya sistem operasi Budiman yang telah kamu buat dengan susah payah dikumpulkan ke Dosen mengatasnamakan Budiman. Kamu tidak diberikan credit apapun. Budiman pun tidak memberikan kata terimakasih kepadamu. Kamupun kecewa tetapi setidaknya kamu telah belajar untuk menjadi pembuat sistem operasi sederhana yang andal. Selamat!

_At last, the OS you painstakingly created was submitted to the lecturer under Budiman's name. You received no credit. Budiman didn't even thank you. You feel disappointed, but at least you've learned to become a reliable creator of simple operating systems. Congratulations!_
