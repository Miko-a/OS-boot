[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/V7fOtAk7)
|    NRP     |      Name      |
| :--------: | :------------: |
| 5025241179 | Erlangga Rizqi Dwi Raswanto |
| 5025241193  | Farrel Jatmiko Aji |
| 5025241220 | Davin Adiputra Suryolaksana |

# Praktikum Modul 4 _(Module 4 Lab Work)_

</div>

### Daftar Soal _(Task List)_

- [Task 1 - FUSecure](/task-1/)

- [Task 2 - LawakFS++](/task-2/)

- [Task 3 - Drama Troll](/task-3/)

- [Task 4 - LilHabOS](/task-4/)

### Laporan Resmi Praktikum Modul 4 _(Module 4 Lab Work Report)_
---

##  [Task 1 - FUSecure](/task-1/)

### a. Setup Direktori dan Pembuatan User

Langkah pertama dalam rencana Yuadi adalah mempersiapkan infrastruktur dasar sistem keamanannya.

1. Buat sebuah "source directory" di sistem Anda (contoh: `/home/shared_files`). Ini akan menjadi tempat penyimpanan utama semua file.

2. Di dalam source directory ini, buat 3 subdirektori: `public`, `private_yuadi`, `private_irwandi`. Buat 2 Linux users: `yuadi` dan `irwandi`. Anda dapat memilih password mereka.
   | User | Private Folder |
   | ------- | --------------- |
   | yuadi | private_yuadi |
   | irwandi | private_irwandi |

Yuadi dengan bijak merancang struktur ini: folder `public` untuk berbagi materi kuliah dan referensi yang boleh diakses siapa saja, sementara setiap orang memiliki folder private untuk menyimpan jawaban praktikum mereka masing-masing.

#### **Penyelesaian :**

Membuat folder-folder yang dibutuhkan :
```sh
sudo mkdir -p /home/shared_files/{public,private_yuadi,private_irwandi}
```

Menambahkan user dan set passwordnya    :
```sh
sudo useradd -m yuadi
sudo useradd -m irwandi
sudo passwd yuadi #0123456789
sudo passwd irwandi #9876543210
```

Mengubah kepemilikan dari masing masing folder private  :
```sh
sudo chown yuadi:yuadi /home/shared_files/private_yuadi
sudo chown irwandi:irwandi /home/shared_files/private_irwandi
```

![setupdirmakeuser](https://i.imgur.com/FngbxMH.png)

### b. Akses Mount Point

Selanjutnya, Yuadi ingin memastikan sistem filenya mudah diakses namun tetap terkontrol.

FUSE mount point Anda (contoh: `/mnt/secure_fs`) harus menampilkan konten dari `source directory` secara langsung. Jadi, jika Anda menjalankan `ls /mnt/secure_fs`, Anda akan melihat `public/`, `private_yuadi/`, dan `private_irwandi/`.

#### **Penyelesaian :**

1. Menggunakan contoh kode FUSE di [modul](https://github.com/arsitektur-jaringan-komputer/Modul-Sisop/blob/master/Modul4/README-ID.md#membuat-program-fuse) yang sudah memiliki fungsi `getattr` dan `readdir` untuk menampilkan semua isi direktori, lalu edit bagian dirpath agar sesuai

```c
...
static  const  char *dirpath = "/home/shared_files";
...
```
Compile kode menggunakan : 
```sh
gcc -Wall `pkg-config fuse --cflags` FUSecure.c -o FUSecure `pkg-config fuse --libs`
```
2. Mount FS

```sh
sudo mkdir /mnt/secure_fs
sudo ./FUSecure /mnt/secure_fs -o allow_other
```
3. Cek direktori
```sh
ls -l /mnt/secure_fs
```

![mountfs](https://i.imgur.com/GmslrKS.png)

### c. Read-Only untuk Semua User

Yuadi sangat kesal dengan kebiasaan Irwandi yang suka mengubah atau bahkan menghapus file jawaban setelah menyalinnya untuk menghilangkan jejak plagiat. Untuk mencegah hal ini, dia memutuskan untuk membuat seluruh sistem menjadi read-only.

1. Jadikan seluruh FUSE mount point **read-only untuk semua user**.
2. Ini berarti tidak ada user (termasuk `root`) yang dapat membuat, memodifikasi, atau menghapus file atau folder apapun di dalam `/mnt/secure_fs`. Command seperti `mkdir`, `rmdir`, `touch`, `rm`, `cp`, `mv` harus gagal semua.

"Sekarang Irwandi tidak bisa lagi menghapus jejak plagiatnya atau mengubah file jawabanku," pikir Yuadi puas.

#### **Penyelesaian :**

1. Menambahkan fungsi command seperti yang diatas tetapi dengan return `EROFS` yaitu error dengan warning "Read-only file system"

```c
...
static int xmp_mkdir(const char *path, mode_t mode) {
    return -EROFS;  
}

static int xmp_rmdir(const char *path) {
    return -EROFS;
}

static int xmp_unlink(const char *path) {
    return -EROFS;
}

static int xmp_mknod(const char *path, mode_t mode, dev_t rdev) {
    return -EROFS;
}

static int xmp_create(const char *path, mode_t mode, struct fuse_file_info *fi) {
    return -EROFS;
}

static int xmp_rename(const char *from, const char *to) {
    return -EROFS;
}

static int xmp_write(const char *path, const char *buf, size_t size, off_t offset, struct fuse_file_info *fi) {
    return -EROFS;
}

static int xmp_open(const char *path, struct fuse_file_info *fi) {
    if ((fi->flags & O_WRONLY) || (fi->flags & O_RDWR)) {
        return -EACCES;  
    }

    char fpath[1000];
    sprintf(fpath, "%s%s", dirpath, path);
    int fd = open(fpath, O_RDONLY);
    if (fd == -1) return -errno;
    close(fd);
    return 0;
}
...
```
Lalu mengupdate stuct `fuse_operations` untuk menggunakan command diatas di dalam FUSE

```c
static struct fuse_operations xmp_oper = {
    .getattr = xmp_getattr,
    .readdir = xmp_readdir,
    .read = xmp_read,
    // START C.
    .open = xmp_open,
    .write = xmp_write,
    .mknod = xmp_mknod,
    .create = xmp_create,
    .mkdir = xmp_mkdir,
    .rmdir = xmp_rmdir,
    .unlink = xmp_unlink,
    .rename = xmp_rename,
    // END C.
};
```

![ROFS](https://i.imgur.com/6UyoP2D.png)

### d. Akses Public Folder

Meski ingin melindungi jawaban praktikumnya, Yuadi tetap ingin berbagi materi kuliah dan referensi dengan Irwandi dan teman-teman lainnya.

Setiap user (termasuk `yuadi`, `irwandi`, atau lainnya) harus dapat **membaca** konten dari file apapun di dalam folder `public`. Misalnya, `cat /mnt/secure_fs/public/materi_kuliah.txt` harus berfungsi untuk `yuadi` dan `irwandi`.

#### **Penyelesaian :**

Sebenarnya pada kode yang terbaru soal a-c saat ini semua user sudah bisa mengakses folder `public` karena tidak ada pembatasan akses berdasarkan UID. Tetapi karena soal e perlu menambahkan pengecekan UID untuk folder `private_...`, maka dalam penyelesaian ini saya menambahkan kode agar folder `public` selalu lolos dalam pengecekan UID pada soal e nantinya.

Pada fungsi `xmp_open` tambahkan pengecekan jika path yang di gunakan adalah direktori `public`

```c
static int xmp_open(const char *path, struct fuse_file_info *fi) {
    if ((fi->flags & O_WRONLY) || (fi->flags & O_RDWR)) {
        return -EACCES;  
    }

    // START D.
    if (strncmp(path, "/public/", 8) == 0) {
        char fpath[1000];
        sprintf(fpath, "%s%s", dirpath, path);
        int fd = open(fpath, O_RDONLY);
        if (fd == -1) return -errno;
        close(fd);
        return 0;
    }
    // END D.

    char fpath[1000];
    sprintf(fpath, "%s%s", dirpath, path);
    int fd = open(fpath, O_RDONLY);
    if (fd == -1) return -errno;
    close(fd);
    return 0;
}
```

![(D)eez](https://i.imgur.com/8k6c21M.png)
![cekuserlain](https://i.imgur.com/jtvrBaj.png)

### e. Akses Private Folder yang Terbatas

Inilah bagian paling penting dari rencana Yuadi - memastikan jawaban praktikum benar-benar terlindungi dari plagiat.

1. File di dalam `private_yuadi` **hanya dapat dibaca oleh user `yuadi`**. Jika `irwandi` mencoba membaca file jawaban praktikum di `private_yuadi`, harus gagal (contoh: permission denied).
2. Demikian pula, file di dalam `private_irwandi` **hanya dapat dibaca oleh user `irwandi`**. Jika `yuadi` mencoba membaca file di `private_irwandi`, harus gagal.

"Akhirnya," senyum Yuadi, "Irwandi tidak bisa lagi menyalin jawaban praktikumku, tapi dia tetap bisa mengakses materi kuliah yang memang kubuat untuk dibagi."

#### **Penyelesaian :**

Memodifikasi fungsi `xmp_open` untuk memeriksa UID dari user yang sedang mengakses, sehingga jika user bukan pemilik folder private yang sedang diakses, maka operasi open dan read gagal dengan kode error `EACCESS`

```c
static int xmp_open(const char *path, struct fuse_file_info *fi) {
    if ((fi->flags & O_WRONLY) || (fi->flags & O_RDWR)) {
        return -EACCES;  
    }

        // START E.
    uid_t uid = fuse_get_context()-> uid;

    if (strncmp(path, "/private_yuadi/", 15) == 0 && uid != 1001) {
        return -EACCES;
    }

    if (strncmp(path, "/private_irwandi/", 17) == 0 && uid != 1002) {
        return -EACCES;
    }
        // END E.

    // START D.
    if (strncmp(path, "/public/", 8) == 0) {
        char fpath[1000];
        sprintf(fpath, "%s%s", dirpath, path);
        int fd = open(fpath, O_RDONLY);
        if (fd == -1) return -errno;
        close(fd);
        return 0;
    }
    // END D.

    char fpath[1000];
    sprintf(fpath, "%s%s", dirpath, path);
    int fd = open(fpath, O_RDONLY);
    if (fd == -1) return -errno;
    close(fd);
    return 0;
}
```

Test akses sebagai user irwandi (UID : 1002)
![testdiuserirwandi](https://i.imgur.com/BbSO7CZ.png)


##  [Task 2 - LawakFS++](/task-1/)
### Buat ROFS (Read Only File System) FUSE
Copy isi file fuseCustom.c yang ada di [playgroud](https://github.com/arsitektur-jaringan-komputer/Modul-Sisop/blob/master/Modul4/playground/fuseCustom.c) 
```c
#define FUSE_USE_VERSION 28
#include <fuse.h>
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <dirent.h>
#include <errno.h>
#include <sys/time.h>

static  const  char *dirpath = "/home/pin/sisop/FUSE";

static  int  xmp_getattr(const char *path, struct stat *stbuf)
{
    int res;
    char fpath[1000];

    sprintf(fpath,"%s%s",dirpath,path);

    res = lstat(fpath, stbuf);

    if (res == -1) return -errno;

    return 0;
}



static int xmp_readdir(const char *path, void *buf, fuse_fill_dir_t filler, off_t offset, struct fuse_file_info *fi)
{
    char fpath[1000];

    if(strcmp(path,"/") == 0)
    {
        path=dirpath;
        sprintf(fpath,"%s",path);
    } else sprintf(fpath, "%s%s",dirpath,path);

    int res = 0;

    DIR *dp;
    struct dirent *de;
    (void) offset;
    (void) fi;

    dp = opendir(fpath);

    if (dp == NULL) return -errno;

    while ((de = readdir(dp)) != NULL) {
        struct stat st;

        memset(&st, 0, sizeof(st));

        st.st_ino = de->d_ino;
        st.st_mode = de->d_type << 12;
        res = (filler(buf, de->d_name, &st, 0));

        if(res!=0) break;
    }

    closedir(dp);

    return 0;
}



static int xmp_read(const char *path, char *buf, size_t size, off_t offset, struct fuse_file_info *fi)
{
    char fpath[1000];
    if(strcmp(path,"/") == 0)
    {
        path=dirpath;

        sprintf(fpath,"%s",path);
    }
    else sprintf(fpath, "%s%s",dirpath,path);

    int res = 0;
    int fd = 0 ;

    (void) fi;

    fd = open(fpath, O_RDONLY);

    if (fd == -1) return -errno;

    res = pread(fd, buf, size, offset);

    if (res == -1) res = -errno;

    close(fd);

    return res;
}



static struct fuse_operations xmp_oper = {
    .getattr = xmp_getattr,
    .readdir = xmp_readdir,
    .read = xmp_read,
};



int  main(int  argc, char *argv[])
{
    umask(0);

    return fuse_main(argc, argv, &xmp_oper, NULL);
}
```
Karena fuseCustom.c ini hanya `getattr`, `readdir`, dan `read` maka perlu ditambahkan struct untuk `open` dan `access`. Tambahkan kode ini ke dalam lawak.c:
```c
static int xmp_open(const char *path, struct fuse_file_info *fi)
{
    char fpath[1000];
    sprintf(fpath, "%s%s", dirpath, path);

    int fd = open(fpath, O_RDONLY);
    if (fd == -1)
        return -errno;

    close(fd);
    return 0;
}

static int xmp_access(const char *path, int mask)
{
    char fpath[1000];
    sprintf(fpath, "%s%s", dirpath, path);

    int res = access(fpath, mask);
    if (res == -1)
        return -errno;

    return 0;
}
```
Soal juga meminta untuk memblokir system call:
- `write()`
- `truncate()`
- `create()`
- `unlink()`
- `mkdir()`
- `rmdir()`
- `rename()`

Maka perlu untuk `return -EROFS` untuk setiap system call diatas dengan menambahkan kode:
```c
static int xmp_write(const char *path, const char *buf, size_t size, off_t offset, struct fuse_file_info *fi) {
    return -EROFS;
}
static int xmp_truncate(const char *path, off_t size) {
    return -EROFS;
}
static int xmp_create(const char *path, mode_t mode, struct fuse_file_info *fi) {
    return -EROFS;
}
static int xmp_unlink(const char *path) {
    return -EROFS;
}
static int xmp_mkdir(const char *path, mode_t mode) {
    return -EROFS;
}

static int xmp_rmdir(const char *path) {
    return -EROFS;
}
static int xmp_rename(const char *from, const char *to) {
    return -EROFS;
}
```
yang terakhir, tambahkan setiap fungsinya ke dalam struct `fuse_operations` menjadi:
```c
static struct fuse_operations xmp_oper = {
    .getattr = xmp_getattr,
    .readdir = xmp_readdir,
    .read = xmp_read,
    .open = xmp_open,
    .access=xmp_access,
    .write=xmp_write,
    .truncate=xmp_truncate,
    .create=xmp_create,
    .unlink=xmp_unlink,
    .mkdir=xmp_mkdir,
    .rmdir=xmp_rmdir,
    .rename=xmp_rename,
    
};
```
setelah itu compile dengan 
```bash
gcc -Wall `pkg-config fuse --cflags` lawak.c -o lawakFS `pkg-config fuse --libs`
```
hasil:
![image](https://i.imgur.com/u3kjqLH.png)
![image](https://i.imgur.com/7pJDox5.png)


### a. Ekstensi File Tersembunyi
Disini kita buat 2 fungtion baru yaitu 
```c
char* find_real_name(const char *directory, const char *fake_name) {
    DIR *dp = opendir(directory);
    if (!dp) return NULL;

    struct dirent *de;
    while ((de = readdir(dp)) != NULL){
        if (de->d_type == DT_REG){//cek apakah data entri yg sekarang merupakan file atau folder, link, dll
            // copy nama file asli
            char tmp[1000];
            strcpy(tmp, de->d_name);

            // hapus extension
            char *dot = strrchr(tmp, '.');
            if (dot) *dot = '\0';//kalau '.' ditemukan, ganti '.' pake '\0'(null terminator) biar string file name nya berhenti di titik

            // compare fake_name (yg gk ada ekstensi)
            if (strcmp(tmp, fake_name) == 0){
                // bikin path ke file asli
                static char result[1000];
                sprintf(result, "%s/%s", directory, de->d_name);
                closedir(dp);
                return result;
            }
        }
    }

    closedir(dp);
    return NULL;  // not found
}
``` 
```c
char *resolve_path(const char *virtual_path) {
    static char resolved_path[PATH_MAX];
    char full_dir[1000];

    // ambil nama file (tanpa /)
    const char *filename = strrchr(virtual_path, '/');
    filename = filename ? filename + 1 : virtual_path;

    // ambil direktori relatif
    char temp_path[1000];
    strcpy(temp_path, virtual_path);
    dirname(temp_path); // modify temp_path jadi path folder

    // gabungin ke path asli
    sprintf(full_dir, "%s%s", dirpath, strcmp(temp_path, "/") == 0 ? "" : temp_path);

    DIR *dp = opendir(full_dir);
    if (!dp) return NULL;

    struct dirent *de;
    while ((de = readdir(dp)) != NULL) {
        if (de->d_type == DT_REG) {
            char name_no_ext[1000];
            strcpy(name_no_ext, de->d_name);
            char *dot = strrchr(name_no_ext, '.');
            if (dot) *dot = '\0';

            if (strcmp(name_no_ext, filename) == 0) {
//                sprintf(resolved_path, "%s/%s", full_dir, de->d_name);
                snprintf(resolved_path, sizeof(resolved_path), "%s/%s", full_dir, de->d_name);
                closedir(dp);
                return resolved_path;
            }
        }
    }

    closedir(dp);
    return NULL;
}
```

#### menjadi
```c
#define FUSE_USE_VERSION 28
#include <fuse.h>
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <dirent.h>
#include <errno.h>
#include <sys/time.h>
#include <libgen.h>
#include <limits.h> 

static  const  char *dirpath = "/home/pin/sisop/FUSE";

char* find_real_name(const char *directory, const char *fake_name) {
    DIR *dp = opendir(directory);
    if (!dp) return NULL;

    struct dirent *de;
    while ((de = readdir(dp)) != NULL) {
        if (de->d_type == DT_REG) {
            // copy nama file asli
            char tmp[1000];
            strcpy(tmp, de->d_name);

            // hapus extension
            char *dot = strrchr(tmp, '.');
            if (dot) *dot = '\0';

            // bandingkan dengan fake_name (yg gak ada ekstensi)
            if (strcmp(tmp, fake_name) == 0) {
                // buat path lengkap ke file aslinya
                static char result[1000];  // static biar gak ke-free pas return
                sprintf(result, "%s/%s", directory, de->d_name);
                closedir(dp);
                return result;
            }
        }
    }

    closedir(dp);
    return NULL;  // not found
}

char *resolve_path(const char *virtual_path) {
    static char resolved_path[PATH_MAX];
    char full_dir[1000];

    // ambil nama file (tanpa /)
    const char *filename = strrchr(virtual_path, '/');
    filename = filename ? filename + 1 : virtual_path;

    // ambil direktori relatif
    char temp_path[1000];
    strcpy(temp_path, virtual_path);
    dirname(temp_path); // modify temp_path jadi path folder

    // gambungin ke path asli
    sprintf(full_dir, "%s%s", dirpath, strcmp(temp_path, "/") == 0 ? "" : temp_path);

    DIR *dp = opendir(full_dir);
    if (!dp) return NULL;

    struct dirent *de;
    while ((de = readdir(dp)) != NULL) {
        if (de->d_type == DT_REG) {
            char name_no_ext[1000];
            strcpy(name_no_ext, de->d_name);
            char *dot = strrchr(name_no_ext, '.');
            if (dot) *dot = '\0';

            if (strcmp(name_no_ext, filename) == 0) {
//                sprintf(resolved_path, "%s/%s", full_dir, de->d_name);
                snprintf(resolved_path, sizeof(resolved_path), "%s/%s", full_dir, de->d_name);
                closedir(dp);
                return resolved_path;
            }
        }
    }

    closedir(dp);
    return NULL;
}



static  int  xmp_getattr(const char *path, struct stat *stbuf)
{
    int res;
    char fpath[1000];

    // coba akses file langsung (path lengkap)
    sprintf(fpath, "%s%s", dirpath, path);
    res = lstat(fpath, stbuf);
    if (res == 0) return 0;

    // kalau failed, coba cari versi yang di hide extension nya
    const char *filename = strrchr(path, '/');
    filename = filename ? filename + 1 : path;

    // ambil folder relatif
    char full_dir[1000];
    strcpy(full_dir, dirpath);
    char temp_path[1000];
    strcpy(temp_path, path);
    dirname(temp_path); // ganti temp_path langsung

    // tambah ke full_dir kalau bukan root
    if (strcmp(path, "/") != 0) {
        strcat(full_dir, temp_path);
    }

    // cari file asli dari nama yang disingkat
    // coba cari file dulu
    char *real_file_path = find_real_name(full_dir, filename);
    if (real_file_path) {
        res = lstat(real_file_path, stbuf);
        if (res == -1) return -errno;
        return 0;
    }

    // kalau gak nemu file, cek apakah itu direktori
    char full_path[1000];
    sprintf(full_path, "%s%s", dirpath, path);
    res = lstat(full_path, stbuf);
    if (res == -1) return -ENOENT;

    return 0;

}



static int xmp_readdir(const char *path, void *buf, fuse_fill_dir_t filler, off_t offset, struct fuse_file_info *fi)
{
    char fpath[1000];

    if(strcmp(path,"/") == 0)
    {
        path=dirpath;
        sprintf(fpath,"%s",path);
    } else sprintf(fpath, "%s%s",dirpath,path);

    int res = 0;

    DIR *dp;
    struct dirent *de;
    (void) offset;
    (void) fi;

    dp = opendir(fpath);

    if (dp == NULL) return -errno;

    while ((de = readdir(dp)) != NULL) {
        struct stat st;

        memset(&st, 0, sizeof(st));

        st.st_ino = de->d_ino;
        st.st_mode = de->d_type << 12;
//        res = (filler(buf, de->d_name, &st, 0));


        // cek if file (bukan folder)
        if (de->d_type == DT_DIR) {
            st.st_mode = S_IFDIR | 0755;  // tandain ini folder
            res = filler(buf, de->d_name, &st, 0);
        } else if (de->d_type == DT_REG) {
            st.st_mode = S_IFREG | 0644;  // tandain ini file biasa

            // potong ekstensi
            char hidden_name[1000];
            strcpy(hidden_name, de->d_name);
            char *dot = strrchr(hidden_name, '.');
            if (dot != NULL) *dot = '\0';

            res = filler(buf, hidden_name, &st, 0);
        } else {
            // fallback
            st.st_mode = de->d_type << 12;
            res = filler(buf, de->d_name, &st, 0);
        }

        if(res!=0) break;
    }

    closedir(dp);

    return 0;
}



static int xmp_read(const char *path, char *buf, size_t size, off_t offset, struct fuse_file_info *fi)
{
    char *real_path;

    if (strcmp(path, "/") == 0) {
        real_path = (char *)dirpath;
    } else {
        real_path = resolve_path(path);
        if (!real_path) return -ENOENT;
    }

    int fd = open(real_path, O_RDONLY);
    if (fd == -1) return -errno;

    int res = pread(fd, buf, size, offset);
    if (res == -1) res = -errno;

    close(fd);
    return res;
}

static int xmp_open(const char *path, struct fuse_file_info *fi)
{
    char *resolved = resolve_path(path);
    if (!resolved) return -ENOENT;

    int fd = open(resolved, O_RDONLY);
    if (fd == -1) return -errno;

    close(fd);
    return 0;
}

static int xmp_access(const char *path, int mask)
{
    int res;
    char fpath[1000];
    char *resolved;

    // coba dulu path lengkapif direktori or root ("/"))
    sprintf(fpath, "%s%s", dirpath, path);
    res = access(fpath, mask);
    if (res == 0) {
        // kalo pathnya ada (misalnya "/home/pin/sisop/FUSE/"), langsung return
        return 0;
    }

    // kalu failed, mungkin ini file tanpa ekstensi
    resolved = resolve_path(path);
    if (!resolved) {
        // if ga nemu sama sekali, error
        return -errno;
    }

    // `access` ke file yg sudah ditemukan
    res = access(resolved, mask);
    if (res == -1) {
        return -errno;
    }

    return 0;
}

static int xmp_write(const char *path, const char *buf, size_t size, off_t offset, struct fuse_file_info *fi) {
    return -EROFS;
}
static int xmp_truncate(const char *path, off_t size) {
    return -EROFS;
}
static int xmp_create(const char *path, mode_t mode, struct fuse_file_info *fi) {
    return -EROFS;
}
static int xmp_unlink(const char *path) {
    return -EROFS;
}
static int xmp_mkdir(const char *path, mode_t mode) {
    return -EROFS;
}

static int xmp_rmdir(const char *path) {
    return -EROFS;
}
static int xmp_rename(const char *from, const char *to) {
    return -EROFS;
}




static struct fuse_operations xmp_oper = {
    .getattr = xmp_getattr,
    .readdir = xmp_readdir,
    .read = xmp_read,
    .open = xmp_open,
    .access=xmp_access,
    .write=xmp_write,
    .truncate=xmp_truncate,
    .create=xmp_create,
    .unlink=xmp_unlink,
    .mkdir=xmp_mkdir,
    .rmdir=xmp_rmdir,
    .rename=xmp_rename,
};



int  main(int  argc, char *argv[])
{
    umask(0);

    return fuse_main(argc, argv, &xmp_oper, NULL);
}
```
setelah itu compile dengan 
```bash
gcc -Wall `pkg-config fuse --cflags` lawak.c -o lawakFS `pkg-config fuse --libs`
```
hasil :
![image](https://i.imgur.com/NMXwOVL.png)

### b. Akses Berbasis Waktu untuk File Secret
Pertama, tambahkan header `<time.h>` lalu buat fungsi untuk mengecek apakah sekarang jam kerja atau tidak
```c
static int jamkerja() {
    time_t now = time(NULL);
    struct tm *local_time = localtime(&now);
    int hour = local_time->tm_hour;
    return (hour >= 8 && hour < 18);
}
```
kemudian edit `access()`, `getattr()`, dan `readdir()`.
Tambahkan kode,
```c
    char tmppath[1000];
    strcpy(tmppath, path);
    if (strcmp(basename(tmppath), "secret") == 0 && !jamkerja()) {
        return -ENOENT;
    }
```
ke `access()` dan `getattr()`. Ini akan membuat file secret tidak bisa dibuka saat diluar jam kerja namun akan ada message error saat di `ls`.
![image](https://i.imgur.com/RcmCDFC.png)



Untuk itu, `readdir()` juga perlu dimodifikasi agar file secret tidak muncul saat di `ls`:
```c
	    if (strcmp(hidden_name, "secret") == 0 && !jamkerja()) {
                continue;
            }
```
Kode ini menghilangkan message error saat `ls` diluar jam kerja.
menjadi:
![image](https://i.imgur.com/L0oD4W8.png)
saat diakses pada jam kerja:
![image](https://i.imgur.com/70iy1x6.png)

### c. Filtering Konten Dinamis
Full code 2.c
```c
#define FUSE_USE_VERSION 28
#include <fuse.h>
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <dirent.h>
#include <errno.h>
#include <sys/time.h>
#include <libgen.h>
#include <limits.h>
#include <time.h>
#include <stdlib.h>
#include <ctype.h>

static const char b64_table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";

char *base64_encode(const unsigned char *data, size_t input_length, size_t *output_length) {
    *output_length = 4 * ((input_length + 2) / 3);
    char *encoded_data = malloc(*output_length + 1);
    if (encoded_data == NULL) return NULL;

    for (size_t i = 0, j = 0; i < input_length;) {
        uint32_t octet_a = i < input_length ? data[i++] : 0;
        uint32_t octet_b = i < input_length ? data[i++] : 0;
        uint32_t octet_c = i < input_length ? data[i++] : 0;
        uint32_t triple = (octet_a << 0x10) + (octet_b << 0x08) + octet_c;
        encoded_data[j++] = b64_table[(triple >> 3 * 6) & 0x3F];
        encoded_data[j++] = b64_table[(triple >> 2 * 6) & 0x3F];
        encoded_data[j++] = b64_table[(triple >> 1 * 6) & 0x3F];
        encoded_data[j++] = b64_table[(triple >> 0 * 6) & 0x3F];
    }

    static const int mod_table[] = {0, 2, 1};
    for (int i = 0; i < mod_table[input_length % 3]; i++) {
        encoded_data[*output_length - 1 - i] = '=';
    }
    
    encoded_data[*output_length] = '\0';
    return encoded_data;
}


static const char *dirpath = "/home/pin/sisop/FUSE";

static int jamkerja() {
    time_t now = time(NULL);
    struct tm *local_time = localtime(&now);
    int hour = local_time->tm_hour;
    return (hour >= 8 && hour < 18);
}

char* find_real_name(const char *directory, const char *fake_name) {
    DIR *dp = opendir(directory);
    if (!dp) return NULL;

    struct dirent *de;
    while ((de = readdir(dp)) != NULL) {
        if (de->d_type == DT_REG) {
            char tmp[1000];
            strcpy(tmp, de->d_name);

            char *dot = strrchr(tmp, '.');
            if (dot) *dot = '\0';

            if (strcmp(tmp, fake_name) == 0) {
                static char result[PATH_MAX];
                snprintf(result, sizeof(result), "%s/%s", directory, de->d_name);
                closedir(dp);
                return result;
            }
        }
    }

    closedir(dp);
    return NULL;
}

char *resolve_path(const char *virtual_path) {
    static char resolved_path[PATH_MAX];
    char full_dir[1000];

    const char *filename = strrchr(virtual_path, '/');
    filename = filename ? filename + 1 : virtual_path;

    char temp_path[1000];
    strcpy(temp_path, virtual_path);
    dirname(temp_path);

    sprintf(full_dir, "%s%s", dirpath, strcmp(temp_path, "/") == 0 ? "" : temp_path);

    DIR *dp = opendir(full_dir);
    if (!dp) return NULL;

    struct dirent *de;
    while ((de = readdir(dp)) != NULL) {
        if (de->d_type == DT_REG) {
            char name_no_ext[1000];
            strcpy(name_no_ext, de->d_name);
            char *dot = strrchr(name_no_ext, '.');
            if (dot) *dot = '\0';

            if (strcmp(name_no_ext, filename) == 0) {
                snprintf(resolved_path, sizeof(resolved_path), "%s/%s", full_dir, de->d_name);
                closedir(dp);
                return resolved_path;
            }
        }
    }

    closedir(dp);
    return NULL;
}

static int xmp_getattr(const char *path, struct stat *stbuf)
{
    char tmppath[1000];
    strcpy(tmppath, path);
    if (strcmp(basename(tmppath), "secret") == 0 && !jamkerja()) {
        return -ENOENT;
    }
    
    int res;
    char fpath[1000];
    sprintf(fpath, "%s%s", dirpath, path);
    res = lstat(fpath, stbuf);
    if (res == 0) return 0;

    char *real_file_path = resolve_path(path);
    if (real_file_path) {
        res = lstat(real_file_path, stbuf);
        if (res == -1) return -errno;
        return 0;
    }

    return -ENOENT;
}

static int xmp_readdir(const char *path, void *buf, fuse_fill_dir_t filler, off_t offset, struct fuse_file_info *fi)
{
    char fpath[1000];
    if(strcmp(path,"/") == 0) {
        path=dirpath;
        sprintf(fpath,"%s",path);
    } else sprintf(fpath, "%s%s",dirpath,path);

    int res = 0;
    DIR *dp;
    struct dirent *de;
    (void) offset;
    (void) fi;
    dp = opendir(fpath);
    if (dp == NULL) return -errno;

    while ((de = readdir(dp)) != NULL) {
        struct stat st;
        memset(&st, 0, sizeof(st));
        st.st_ino = de->d_ino;
        st.st_mode = de->d_type << 12;

        if (strcmp(de->d_name, ".") == 0 || strcmp(de->d_name, "..") == 0) {
            continue;
        }

        if (de->d_type == DT_DIR) {
            res = filler(buf, de->d_name, &st, 0);
        } else if (de->d_type == DT_REG) {
            char hidden_name[1000];
            strcpy(hidden_name, de->d_name);
            char *dot = strrchr(hidden_name, '.');
            if (dot != NULL) *dot = '\0';
        
            if (strcmp(hidden_name, "secret") == 0 && !jamkerja()) {
                continue;
            }
            
            res = filler(buf, hidden_name, &st, 0);
        }
        if(res!=0) break;
    }

    closedir(dp);
    return 0;
}

int strcicmp(char const *a, char const *b) {
    for (;; a++, b++) {
        int d = tolower((unsigned char)*a) - tolower((unsigned char)*b);
        if (d != 0 || !*a)
            return d;
    }
}


static int xmp_read(const char *path, char *buf, size_t size, off_t offset, struct fuse_file_info *fi)
{
    char *real_path = resolve_path(path);
    if (!real_path) {
        return -ENOENT;
    }

    (void) fi;
    int fd = open(real_path, O_RDONLY);
    if (fd == -1) {
        return -errno;
    }

    off_t file_size = lseek(fd, 0, SEEK_END);
    lseek(fd, 0, SEEK_SET);

    char *original_content = malloc(file_size + 1);
    if (!original_content) {
        close(fd);
        return -ENOMEM;
    }
    ssize_t read_bytes = read(fd, original_content, file_size);
    if (read_bytes == -1) {
        free(original_content);
        close(fd);
        return -errno;
    }
    original_content[file_size] = '\0';
    close(fd);


    char *processed_content = NULL;
    size_t processed_size = 0;

    int is_binary = 0;
    for (int i = 0; i < read_bytes; i++) {
        if (original_content[i] == '\0') {
            is_binary = 1;
            break;
        }
    }

    if (is_binary) {
        processed_content = base64_encode((unsigned char*)original_content, file_size, &processed_size);
    } else {
        const char *lawak_words[] = {"sisop", "calc", NULL};
        const char *replacement = "lawak";
        
        char* new_content = malloc(file_size * (strlen(replacement) + 1) + 1);
        if (!new_content) {
            free(original_content);
            return -ENOMEM;
        }

        char *writer = new_content;
        char *reader = original_content;

        while (*reader) {
            int replaced = 0;
            for (int i = 0; lawak_words[i] != NULL; i++) {
                size_t word_len = strlen(lawak_words[i]);
                if (strncasecmp(reader, lawak_words[i], word_len) == 0) {
                    strcpy(writer, replacement);
                    writer += strlen(replacement);
                    reader += word_len;
                    replaced = 1;
                    break;
                }
            }
            if (!replaced) {
                *writer++ = *reader++;
            }
        }
        *writer = '\0';
        
        processed_content = new_content;
        processed_size = strlen(processed_content);
    }
    
    free(original_content);

    int res = 0;
    if (offset < processed_size) {
        size_t available_size = processed_size - offset;
        size_t copy_size = (size < available_size) ? size : available_size;
        memcpy(buf, processed_content + offset, copy_size);
        res = copy_size;
    } else {
        res = 0;
    }

    free(processed_content);

    return res;
}


static int xmp_open(const char *path, struct fuse_file_info *fi)
{
    char *resolved = resolve_path(path);
    if (!resolved) return -ENOENT;
    
    int res = access(resolved, R_OK);
    if (res == -1) return -errno;

    return 0;
}

static int xmp_access(const char *path, int mask)
{
    char tmppath[1000];
    strcpy(tmppath, path);
    if (strcmp(basename(tmppath), "secret") == 0 && !jamkerja()) {
        return -ENOENT;
    }

    char fpath[1000];
    sprintf(fpath, "%s%s", dirpath, path);
    int res = access(fpath, mask);
    if (res == 0) return 0; 

    char *resolved = resolve_path(path);
    if (!resolved) {
        return -ENOENT;
    }
    
    res = access(resolved, mask);
    if (res == -1) {
        return -errno;
    }

    return 0;
}

static int xmp_write(const char *path, const char *buf, size_t size, off_t offset, struct fuse_file_info *fi) {
    (void)path; (void)buf; (void)size; (void)offset; (void)fi;
    return -EROFS;
}
static int xmp_truncate(const char *path, off_t size) {
    (void)path; (void)size;
    return -EROFS;
}
static int xmp_create(const char *path, mode_t mode, struct fuse_file_info *fi) {
    (void)path; (void)mode; (void)fi;
    return -EROFS;
}
static int xmp_unlink(const char *path) {
    (void)path;
    return -EROFS;
}
static int xmp_mkdir(const char *path, mode_t mode) {
    (void)path; (void)mode;
    return -EROFS;
}
static int xmp_rmdir(const char *path) {
    (void)path;
    return -EROFS;
}
static int xmp_rename(const char *from, const char *to) {
    (void)from; (void)to;
    return -EROFS;
}

static struct fuse_operations xmp_oper = {
    .getattr = xmp_getattr,
    .readdir = xmp_readdir,
    .read = xmp_read,
    .open = xmp_open,
    .access = xmp_access,
    .write = xmp_write,
    .truncate = xmp_truncate,
    .create = xmp_create,
    .unlink = xmp_unlink,
    .mkdir = xmp_mkdir,
    .rmdir = xmp_rmdir,
    .rename = xmp_rename,
};

int main(int argc, char *argv[])
{
    umask(0);
    return fuse_main(argc, argv, &xmp_oper, NULL);
}

```
#### Function biner
function base64_encode untuk mengubah data biner menjadi format string base64 sesuai dengan yang diminta di soal
```c
char *base64_encode(const unsigned char *data, size_t input_length, size_t *output_length)
{
    *output_length = 4 * ((input_length + 2) / 3);
    char *encoded_data = malloc(*output_length + 1);
    if (encoded_data == NULL) return NULL;

    for (size_t i = 0, j = 0; i < input_length;) {
        uint32_t octet_a = i < input_length ? data[i++] : 0;
        uint32_t octet_b = i < input_length ? data[i++] : 0;
        uint32_t octet_c = i < input_length ? data[i++] : 0;

        uint32_t triple = (octet_a << 16) + (octet_b << 8) + octet_c;

        encoded_data[j++] = b64_table[(triple >> 18) & 0x3F];
        encoded_data[j++] = b64_table[(triple >> 12) & 0x3F];
        encoded_data[j++] = b64_table[(triple >> 6) & 0x3F];
        encoded_data[j++] = b64_table[triple & 0x3F];
    }

    static const int mod_table[] = {0, 2, 1};
    for (int i = 0; i < mod_table[input_length % 3]; i++) {
        encoded_data[*output_length - 1 - i] = '=';
    }

    encoded_data[*output_length] = '\0';
    return encoded_data;
}
```
#### mengubah xmp_read
```c
static int xmp_read(const char *path, char *buf, size_t size, off_t offset, struct fuse_file_info *fi)
{
    char *real_path = resolve_path(path);
    if (!real_path) {
        return -ENOENT;
    }

    (void) fi;
    int fd = open(real_path, O_RDONLY);
    if (fd == -1) {
        return -errno;
    }

    off_t file_size = lseek(fd, 0, SEEK_END);
    lseek(fd, 0, SEEK_SET);

    char *original_content = malloc(file_size + 1);
    if (!original_content) {
        close(fd);
        return -ENOMEM;
    }
    ssize_t read_bytes = read(fd, original_content, file_size);
    if (read_bytes == -1) {
        free(original_content);
        close(fd);
        return -errno;
    }
    original_content[file_size] = '\0';
    close(fd);


    char *processed_content = NULL;
    size_t processed_size = 0;

    int is_binary = 0;
    for (int i = 0; i < read_bytes; i++) {
        if (original_content[i] == '\0') {
            is_binary = 1;
            break;
        }
    }

    if (is_binary) {
        processed_content = base64_encode((unsigned char*)original_content, file_size, &processed_size);
    } else {
        const char *lawak_words[] = {"sisop", "calc", NULL};
        const char *replacement = "lawak";
        
        char* new_content = malloc(file_size * (strlen(replacement) + 1) + 1);
        if (!new_content) {
            free(original_content);
            return -ENOMEM;
        }

        char *writer = new_content;
        char *reader = original_content;

        while (*reader) {
            int replaced = 0;
            for (int i = 0; lawak_words[i] != NULL; i++) {
                size_t word_len = strlen(lawak_words[i]);
                if (strncasecmp(reader, lawak_words[i], word_len) == 0) {
                    strcpy(writer, replacement);
                    writer += strlen(replacement);
                    reader += word_len;
                    replaced = 1;
                    break;
                }
            }
            if (!replaced) {
                *writer++ = *reader++;
            }
        }
        *writer = '\0';
        
        processed_content = new_content;
        processed_size = strlen(processed_content);
    }
    
    free(original_content);

    int res = 0;
    if (offset < processed_size) {
        size_t available_size = processed_size - offset;
        size_t copy_size = (size < available_size) ? size : available_size;
        memcpy(buf, processed_content + offset, copy_size);
        res = copy_size;
    } else {
        res = 0;
    }

    free(processed_content);

    return res;
}
```
##### Cek biner
```c
int is_binary = 0;
for (int i = 0; i < read_bytes; i++) {
    if (original_content[i] == '\0') {
        is_binary = 1;
        break;
    }
}
```

looping ini mengecek apakah file mengandung karakter null (`'\0'`) sebagai indikator bahwa file bersifat biner. kalau iya, biner akan diproses  `base64 encoding`


##### Base64 encoding (untuk file biner)

```c
processed_content = base64_encode((unsigned char*)original_content, file_size, &processed_size);
```

##### Filtering kata lawak (untuk file txt)

```c
const char *lawak_words[] = {"sisop", "calc", NULL};
const char *replacement = "lawak";
```

* loop `while(*reader){...}` untuk membaca setiap char dari `original_content`
* kalau ditemukan salah satu kata dalam `lawak_words`, maka kata tersebut di replace dengan `replacement`.
---

#### kembalikan hasil ke user

```c
if (offset < processed_size) {
    ...
    memcpy(buf, processed_content + offset, copy_size);
}
```

setelah semuanya selesai, hasilnya disalin ke buffer `buf`, hitung nilai `offset` dan `size` sesuai permintaan dari FUSE.

hasil:
![image](https://i.imgur.com/lB5ZCAA.png)
![tw](https://i.imgur.com/mzxQyIb.png)

### d. Logging Akses
#### Buat file `.log` di `/var/log/`
```c
sudo touch /var/log/lawakfs.log
sudo chown $USER /var/log/lawakfs.log
sudo chmod 644 /var/log/lawakfs.log
```
`644` agar hanya owner/root yang bisa mengedit `.log` dan yang lain hanya bisa membaca

#### buat function untuk menulis ke `log`
```c

void log_action(const char *action, const char *path) {
    FILE *log_file = fopen("/var/log/lawakfs.log", "a");
    if (!log_file) {
      log_file = fopen("/var/log/lawakfs.log", "w");
      if (!log_file) return;
    }
    if (!log_file) return;

    time_t rawtime;
    struct tm *timeinfo;
    char timebuf[64];

    time(&rawtime);
    timeinfo = localtime(&rawtime);
    strftime(timebuf, sizeof(timebuf), "%Y-%m-%d %H:%M:%S", timeinfo);

    uid_t uid = getuid();

    fprintf(log_file, "[%s] [%d] [%s] [%s]\n", timebuf, uid, action, path);
    fclose(log_file);
}
```
Setelah membuat function log, panggil fungsi nya pada setiap function seperti getattr, read, access, open, dll.
contoh :
```c
log_action("READ", path)
```
untuk menulis action read di log dengan `path` sebagai path yang akan di tulis di .log

hasil:
![log](https://i.imgur.com/9Sbq9qH.png)

### e. konfigurasi

#### membuat struct config
ini berisi data-data yang telah diisi user di lawak.config
```c
typedef struct {
    char *filter_words[100];
    int filter_word_count;
    char secret_file_basename[256];
    int access_start_hour;
    int access_end_hour;
} Config;

Config config;
```
#### membuat fungsi laod config
function ini akan load .config dan mengisi struct config
```c
void load_config(const char *config_path) {
    FILE *file = fopen(config_path, "r");
    if (!file) {
        perror("Gagal buka config");
        exit(1);
    }

    char line[1024];
    while (fgets(line, sizeof(line), file)) {
        char *eq = strchr(line, '=');
        if (!eq) continue;

        *eq = '\0';
        char *key = line;
        char *value = eq + 1;

        value[strcspn(value, "\r\n")] = 0;

        if (strcmp(key, "FILTER_WORDS") == 0) {
            char *token = strtok(value, ",");
            int i = 0;
            while (token && i < 100) {
                config.filter_words[i] = strdup(token);
                i++;
                token = strtok(NULL, ",");
            }
            config.filter_word_count = i;
        } else if (strcmp(key, "SECRET_FILE_BASENAME") == 0) {
            strncpy(config.secret_file_basename, value, sizeof(config.secret_file_basename));
        } else if (strcmp(key, "ACCESS_START") == 0) {
            sscanf(value, "%d", &config.access_start_hour);
        } else if (strcmp(key, "ACCESS_END") == 0) {
            sscanf(value, "%d", &config.access_end_hour);
        }
    }

    fclose(file);
}

```
#### mengubah kode2 yang sebelumnya
Karena jam, kata `secret` dan kata lawak sudah bergantung pada .config, maka code seperti:
```c
    return (hour >= 8 && hour < 18);
```
diubah menjadi
```c
    return (hour >= config.access_start_hour && hour < config.access_end_hour);
}
```
pada `getattr`, kata "seccret" diganti menjadi berdasarkan struct config:
```c
if (strcmp(basename(tmppath), "secret") == 0 && !jamkerja()) {
        return -ENOENT;
}
```
menjadi
```c
	if (strcmp(basename(tmppath), config.secret_file_basename) == 0 && !jamkerja()) {
	    return -ENOENT;
	}
```
Pada xmp_read juga diganti agar mereplace sesuai yang ada di .config (`config.filter_words`)
```
    else {
        const char *replacement = "lawak";
        
        char* new_content = malloc(file_size * (strlen(replacement) + 1) + 1);
        if (!new_content) {
            free(original_content);
            return -ENOMEM;
        }

        char *writer = new_content;
	char *reader = original_content;

	while (*reader) {
	    int replaced = 0;
	    for (int i = 0; i < config.filter_word_count; i++) {
		size_t word_len = strlen(config.filter_words[i]);
		if (strncasecmp(reader, config.filter_words[i], word_len) == 0) {
		    strcpy(writer, replacement);
		    writer += strlen(replacement);
		    reader += word_len;
		    replaced = 1;
		    break;
		}
	    }
```
dan seterusnya untuk function yang lain.
hasil :
![conf](https://i.imgur.com/xaG1che.png)
![res](https://i.imgur.com/KCunvJP.png)

##  [Task 3 - Drama Troll](/task-1/)

# **Drama Troll**

Dalam sebuah perusahaan penuh drama fandom, seorang karyawan bernama DainTontas telah menyinggung komunitas Gensh _ n, Z _ Z, dan Wut \* ering secara bersamaan. Akibatnya, dua rekan kerjanya, SunnyBolt dan Ryeku, merancang sebuah troll dengan gaya khas: membuat filesystem jebakan menggunakan FUSE.

Mereka membutuhkan bantuanmu, ahli Sistem Operasi, untuk menciptakan filesystem kustom yang bisa mengecoh DainTontas, langsung dari terminal yang dia cintai.

## **a. Pembuatan User**

Buat 3 user di sistem sebagai berikut yang merepresentasikan aktor-aktor yang terlibat dalam _trolling_ kali ini, yaitu:

- DainTontas
- SunnyBolt
- Ryeku

Gunakan `useradd` dan `passwd` untuk membuat akun lokal. User ini akan digunakan untuk mengakses filesystem FUSE yang kamu buat.


![Create_User](https://github.com/user-attachments/assets/8422812d-38af-4ab5-ac64-fe03c8ea753b)

![user_list](https://github.com/user-attachments/assets/ed4fc199-ce9d-45c5-9110-ce82c38e6d95)

## **b. Jebakan Troll**

Untuk menjebak DainTontas, kamu harus menciptakan sebuah filesystem FUSE yang dipasang di `/mnt/troll`. Di dalamnya, hanya akan ada dua file yang tampak sederhana:

- `very_spicy_info.txt` - umpan utama.
- `upload.txt` - tempat DainTontas akan "secara tidak sadar" memicu jebakan.

![make_mnt_troll](https://github.com/user-attachments/assets/2fd0ceda-74ce-42ff-abdb-d3def4ec2543)



## **c. Jebakan Troll (Berlanjut)**

Setelah membuat dua file tersebut, SunnyBolt dan Ryeku memintamu untuk membuat jebakan yang telah dirancang oleh mereka. SunnyBolt dan Ryeku yang merupakan kolega lama DainTontas dan tahu akan hal-hal memalukan yang dia pernah lakukan, akan menaruh aib-aib tersebut pada file `very_spicy_info.txt`. Apabila dibuka dengan menggunakan command `cat` oleh user selain DainTontas, akan memberikan output seperti berikut:

```
DainTontas' personal secret!!.txt
```

Untuk mengecoh DainTontas, apabila DainTontas membuka `very_spicy_info.txt`, isinya akan berupa seperti berikut:

```
Very spicy internal developer information: leaked roadmap.docx
```

Untuk bagian ini, saya menulis program troll_fs.c yang hanya membuat dua file: very_spicy_info.txt dan upload.txt. Fokus dari bagian ini adalah implementasi file very_spicy_info.txt dengan isi berbeda tergantung user yang mengakses.

### **Berikut adalah penjelasan kode terkait:**

```c
#define FUSE_USE_VERSION 31
#include <fuse3/fuse.h>
#include <string.h>
#include <errno.h>
#include <stdio.h>
#include <unistd.h>
#include <pwd.h>
#include <sys/stat.h>
```

Baris di atas menyertakan header yang dibutuhkan untuk FUSE, operasi file, manipulasi string, dan informasi user.

```c
static const char *very_spicy_path = "/very_spicy_info.txt";
static const char *upload_path = "/upload.txt";
static const char *secret_text = "Very spicy internal developer information: leaked roadmap.docx";
static const char *bait_text = "DainTontas' personal secret!!.txt";
```

Inisialisasi path dan konten untuk dua file. `secret_text` akan ditampilkan hanya untuk DainTontas, sedangkan `bait_text` untuk user lain.
```c

static const char* get_username() {
    uid_t uid = fuse_get_context()->uid;
    struct passwd *pw = getpwuid(uid);
    return pw ? pw->pw_name : "";
}
```

Fungsi ini mengambil nama user yang sedang mengakses filesystem, berdasarkan UID.

```c
static int troll_getattr(const char *path, struct stat *st, struct fuse_file_info *fi) {
    memset(st, 0, sizeof(struct stat));
    if (strcmp(path, "/") == 0) {
        st->st_mode = S_IFDIR | 0755;
        st->st_nlink = 2;
    } else if (strcmp(path, very_spicy_path) == 0 || strcmp(path, upload_path) == 0) {
        st->st_mode = S_IFREG | 0666;
        st->st_nlink = 1;
        st->st_size = 1024;
    } else {
        return -ENOENT;
    }
    return 0;
}
```


Fungsi ini mendefinisikan atribut file. Jika path adalah direktori root /, maka bertipe direktori. Jika path salah satu dari dua file, maka dianggap file reguler dengan ukuran dummy 1024 byte.

```c
static int troll_readdir(const char *path, void *buf, fuse_fill_dir_t filler,
                      off_t offset, struct fuse_file_info *fi, enum fuse_readdir_flags flags) {
    if (strcmp(path, "/") != 0)
        return -ENOENT;

    filler(buf, ".", NULL, 0, 0);
    filler(buf, "..", NULL, 0, 0);
    filler(buf, "very_spicy_info.txt", NULL, 0, 0);
    filler(buf, "upload.txt", NULL, 0, 0);
    return 0;
}
```

Fungsi ini menangani isi direktori `/`. Di sini hanya dua file yang akan terlihat oleh user, `very_spicy_info.txt` dan `upload.txt`

```c
static int troll_open(const char *path, struct fuse_file_info *fi) {
    if (strcmp(path, very_spicy_path) != 0 && strcmp(path, upload_path) != 0)
        return -ENOENT;
    return 0;
}
```

Fungsi ini akan memvalidasi file yang dibuka. Jika bukan salah satu dari dua file, maka akan error (`ENOENT` -> Error NO ENTry).

```c
static int troll_read(const char *path, char *buf, size_t size, off_t offset, struct fuse_file_info *fi) {
    const char *username = get_username();
    const char *content = "";
    size_t len;

    if (strcmp(path, very_spicy_path) == 0) {
        if (strcmp(username, "DainTontas") == 0)
            content = secret_text;
        else
            content = bait_text;
    } else if (strcmp(path, upload_path) == 0) {
        content = "Nothing..";
    } else {
        return -ENOENT;
    }

    len = strlen(content);
    if (offset >= len)
        return 0;
    if (offset + size > len)
        size = len - offset;
    memcpy(buf, content + offset, size);
    return size;
}
```

Fungsi utama yang mengatur isi file. Di sinilah isi file berbeda tergantung nama user yang sedang membaca `very_spicy_info.txt`. 

Pertama-tama ambil username saat ini, dengan fungsi `ger_username()`. Lihat pada bagian percabangan `if else`. Jika path yang dituju user adalah `very_spicy_path`, maka cek usernamenya apakah DainTontas atau tidak. Jika yang pertama maka isi variabel `content` (variabel yang menyimpan teks yang akan di tampilkan di file) akan menjadi `secret_text`, jika yang kedua maka akan menjadi `bait_text`.

Di percabangan selanjutnya, jika path yang dituju adalah `upload_path`, sebenarnya tidak perlu melakukan apapun. Namun, saya mengisi nya dengan tulisan `Nothing..`.

![Notes_Image](https://github.com/user-attachments/assets/a4f39b10-708f-4ead-b94e-9b78f4a2b1a2)


Lanjut ke bagian penulisan

```c
len = strlen(content);
    if (offset >= len)
        return 0;
    if (offset + size > len)
        size = len - offset;
    memcpy(buf, content + offset, size);
    return size;
```

Lalu di bagian bawahnya, kita akan mengisi filenya dengan bantuan fungsi `memcpy()`

Referensi memcpy: https://en.cppreference.com/w/cpp/string/byte/memcpy

Dari referensi, `memcpy` memiliki 3 parameter, **dest**, **src**, **count**.

```c
len = strlen(content);
```
Menghitung panjang string content (jumlah karakter hingga null-terminator \0), ini penting agar kita tahu seberapa banyak data yang tersedia untuk dibaca.

```c
if (offset >= len)
    return 0;
```
Jika offset (posisi baca yang diminta oleh sistem) sudah melebihi panjang data yang tersedia (len), artinya tidak ada lagi data yang bisa dibaca.

Maka fungsi langsung mengembalikan 0, yang menandakan EOF (End of File).

```c
if (offset + size > len)
    size = len - offset;
```

Kalau jumlah data yang ingin dibaca (size) ditambah dengan offset melebihi panjang konten, kita potong size supaya tidak melewati akhir string.

Ini mencegah memcpy membaca memori di luar batas content.

```c
memcpy(buf, content + offset, size);
```
Menyalin data sebanyak size byte dari content mulai dari posisi offset ke buffer buf, yang akan dikirim ke proses pemanggil (misalnya cat).

![cat_vsi txt](https://github.com/user-attachments/assets/499672fc-bf25-485a-96d4-60dd81a38633)


## **d. Trap**

Suatu hari, DainTontas mengecek PC dia dan menemukan bahwa ada sebuah file baru, `very_spicy_info.txt`. Dia kemudian membukanya dan menemukan sebuah leak roadmap. Karena dia ingin menarik perhatian, dia akan mengupload file tersebut ke sosial media `Xitter` dimana _followernya_ dapat melihat dokumen yang bocor tersebut. Karena dia pernah melakukan hal ini sebelumnya, dia punya script bernama `upload.txt` yang dimana ketika dia melakukan `echo` berisi `upload` pada file tersebut, dia akan otomatis mengupload sebuah file ke akun `Xitter` miliknya.

Namun ini semua adalah rencana SunnyBolt dan Ryeku. Atas bantuanmu, mereka telah mengubah isi dari `upload.txt`. Apabila DainTontas melakukan upload dengan metode `echo` miliknya, maka dia dinyatakan telah "membocorkan" info leak tersebut. Setelah DainTontas membocorkan file tersebut, semua file berekstensi `.txt` akan selalu memunculkan sebuah ASCII Art dari kalimat

```
Fell for it again reward
```

Setelah seharian penuh mencoba untuk membetulkan PC dia, dia baru sadar bahwa file `very_spicy_info.txt` yang dia sebarkan melalui script uploadnya tersebut teryata berisikan aib-aib dia. Dia pun ditegur oleh SunnyBolt dan Ryeku, dan akhirnya dia pun tobat.

### Penjelasan

Bagian ini merupakan pengembangan jebakan yang akan aktif ketika user DainTontas melakukan `echo "upload" > upload.txt`. Ketika itu terjadi, seluruh file .txt di filesystem akan berubah isinya menjadi ASCII art "Fell for it again reward".

Untuk mengimplementasikannya, program menggunakan flag has_fallen, yang diubah melalui fungsi write:

```c
static int has_fallen = 0;
```
    
Variabel has_fallen adalah flag global yang menunjukkan apakah jebakan telah aktif.

```c
static int troll_write(const char *path, const char *buf, size_t size, off_t offset, struct fuse_file_info *fi) {
    if (strcmp(path, upload_path) == 0 && strstr(buf, "upload") != NULL) {
        has_fallen = 1;
    }
    return size;
}
```

Fungsi write mendeteksi jika upload.txt ditulisi dengan string "upload". Jika ya, maka has_fallen diset ke 1.

Lalu fungsi read akan menyesuaikan isi file .txt:

```c
static const char *ascii_art = "Fell for it again reward\n";

static int troll_read(...) {
    ...
    if (has_fallen && strstr(path, ".txt") && strcmp(username, "DainTontas")) {
        content = ascii_art;
    }
    ...
}
```

Ada tiga kondisi agar jebakan aktif,

Jika jebakan sudah aktif (has_fallen == 1), file yang dibaca berakhiran `.txt`, dan usernya adalah `DainTontas`, maka isi file yang ditampilkan adalah ASCII art jebakan.

Ini membuat efek bahwa semua file teks sudah "dirusak" oleh upload yang dilakukan DainTontas sendiri.

Dengan cara ini, filesystem FUSE akan menciptakan jebakan yang seolah-olah permanen selama program berjalan, dan bisa diperluas menjadi persist menggunakan file .state di luar mount point.

![uploading](https://github.com/user-attachments/assets/499a8fcb-d1cb-4d49-9340-65a40e56efdc)


---

### Kompilasi dan Eksekusi 

Kompilasi dengan 
```
gcc -Wall troll_fs.c -o troll_fs `pkg-config fuse3 --cflags --libs`
```

Lalu Eksekusi dengan 
```
sudo ./troll_fs -o allow_other mnt/troll/
```

`allow_other` digunakan agar user lain dapat mengakses file.







##  [Task 4 - LilHabOS](/task-4/)

### a. Implementasikan fungsi `printString`, `readString`, dan `clearScreen` di `kernel.c` yang akan menampilkan dan membaca string di layar.

- `printString`: Menampilkan string yang diakhiri null menggunakan `int 10h` dengan `AH=0x0E`.
- `readString`: Membaca karakter dari keyboard menggunakan `int 16h` dengan `AH=0x00` sampai Enter ditekan. Termasuk penanganan Backspace dasar.
- `clearScreen`: Membersihkan layar dan mengatur kursor ke pojok kiri atas `(0, 0)` menggunakan `int 10h` dengan `AH=0x06` dan `AH=0x02`. Buffer video untuk warna karakter akan diubah menjadi putih.

**Penyelesaian:**

#### 1. Fungsi `printString`

```c
void printString(char* str) {
    int i = 0;
    while (str[i] != '\0') {
        interrupt(0x10, 0x0E00 | str[i], 0, 0, 0); // AH=0x0E, AL=karakter
        i++;
    }
}
```

- Fungsi ini menerima parameter pointer ke string (`char* str`)
- Menggunakan loop `while` untuk iterasi setiap karakter dalam string
- Kondisi loop: `str[i] != '\0'` - berhenti saat mencapai null terminator
- Memanggil `interrupt(0x10, 0x0E00 | str[i], 0, 0, 0)` untuk setiap karakter
  - `0x10`: Interrupt BIOS untuk layanan video
  - `0x0E00 | str[i]`:
    - `AH = 0x0E` (layanan Write Character in TTY Mode)
    - `AL = str[i]` (karakter yang akan ditampilkan)
- Karakter ditampilkan satu per satu secara berurutan

#### 2. Fungsi `readString`

```c
void readString(char* buf) {
    int i = 0;
    char c;
    while (1) {
        c = interrupt(0x16, 0x0000, 0, 0, 0); // AH=0x00, tunggu input
        if (c == 0x0D) { // Enter
            break;
        } else if (c == 0x08) { // Backspace
            if (i > 0) {
                i--;
                printString("\b \b"); // Hapus karakter di layar
            }
        } else {
            buf[i++] = c;
            interrupt(0x10, 0x0E00 | c, 0, 0, 0); // Tampilkan karakter
        }
    }
    buf[i] = '\0';
}
```

- Fungsi menerima buffer untuk menyimpan input (`char* buf`)
- Menggunakan infinite loop (`while(1)`) untuk membaca input karakter demi karakter
- `interrupt(0x16, 0x0000, 0, 0, 0)`:
  - `0x16`: Interrupt BIOS untuk layanan keyboard
  - `AH = 0x00`: Fungsi Read Character from Keyboard (menunggu input)
- **Penanganan karakter khusus:**
  - `0x0D` (Enter): Keluar dari loop input
  - `0x08` (Backspace):
    - Cek apakah ada karakter yang bisa dihapus (`i > 0`)
    - Kurangi indeks buffer (`i--`)
    - Tampilkan sequence `"\b \b"` (backspace, spasi, backspace) untuk menghapus karakter di layar
  - **Karakter normal**:
    - Simpan ke buffer (`buf[i++] = c`)
    - Tampilkan di layar menggunakan interrupt video
- Setelah selesai, tambahkan null terminator (`buf[i] = '\0'`)

#### 3. Fungsi `clearScreen`

```c
void clearScreen() {
    // Clear layar dengan scroll seluruh area layar
    interrupt(0x10, 0x0600 | 0, 0x0700, 0, (24 << 8) | 79);
    // Set kursor ke posisi 0,0
    interrupt(0x10, 0x0200, 0, 0, 0);
}
```

- **Langkah 1 - Clear Screen:**
  - `interrupt(0x10, 0x0600 | 0, 0x0700, 0, (24 << 8) | 79)`
  - `AH = 0x06`: Fungsi Scroll Up Window
  - `AL = 0`: Jumlah baris yang di-scroll (0 = clear seluruh window)
  - `0x0700`:
    - `BH = 0x07`: Atribut warna (putih text pada background hitam)
  - `(24 << 8) | 79`:
    - `DH = 24`: Baris terakhir (row 24, 0-indexed = 25 baris total)
    - `DL = 79`: Kolom terakhir (column 79, 0-indexed = 80 kolom total)
    - Mendefinisikan area layar standar 80x25

- **Langkah 2 - Reset Cursor:**
  - `interrupt(0x10, 0x0200, 0, 0, 0)`
  - `AH = 0x02`: Fungsi Set Cursor Position
  - Register lain = 0: Posisi kursor di (0,0) - pojok kiri atas

**Ringkasan Fungsi:**
1. **`printString`**: Mengambil setiap huruf dari kata, menampilkan satu-satu ke layar
2. **`readString`**: Menunggu user mengetik, menyimpan huruf yang diketik, jika Enter maka selesai
3. **`clearScreen`**: Membersihkan layar menjadi kosong, menempatkan kursor di pojok kiri atas

![BahLilOS](https://i.imgur.com/Com7tSZ.png)

---

### b. Lengkapi implementasi fungsi-fungsi di [`std_lib.h`](./include/std_lib.h) dalam [`std_lib.c`](./src/std_lib.c).

Fungsi-fungsi di atas dapat digunakan untuk menyederhanakan implementasi fungsi `printString`, `readString`, `clearScreen`, dan fungsi-fungsi lebih lanjut yang dijelaskan pada tugas berikutnya.

**Penyelesaian:**

Fungsi-fungsi di `std_lib.h`: div, mod, memcpy, strlen, strcmp, strcpy, dan clear akan diimplementasikan di `std_lib.c`

#### 1. Fungsi `div` - Pembagian

```c
int div(int a, int b) {
    int result = 0;
    while (a >= b) {
        a = a - b;
        result++;
    }
    return result;
}
```

- Melakukan pembagian tanpa menggunakan operator `/`
- Menggunakan metode pengurangan berulang
- Selama `a >= b`, kurangi `a` dengan `b` dan tambah counter `result`

#### 2. Fungsi `mod` - Sisa Bagi

```c
int mod(int a, int b) {
    while (a >= b) {
        a = a - b;
    }
    return a;
}
```

- Mencari sisa pembagian tanpa menggunakan operator `%`
- Mirip dengan `div`, tetapi mengembalikan nilai `a` yang tersisa

#### 3. Fungsi `memcpy` - Copy Memory

```c
void memcpy(byte* src, byte* dst, unsigned int size) {
    unsigned int i;
    for (i = 0; i < size; i++) {
        dst[i] = src[i];
    }
}
```

- Menyalin data dari alamat `src` ke alamat `dst`
- Menyalin sebanyak `size` bytes
- Menggunakan loop untuk copy byte per byte
- Untuk menyalin array, string, atau data apapun

#### 4. Fungsi `strlen` - Panjang String

```c
unsigned int strlen(char* str) {
    unsigned int len = 0;
    while (str[len] != '\0') {
        len++;
    }
    return len;
}
```

- Menghitung panjang string (jumlah karakter)
- Loop sampai menemukan null terminator (`\0`)

#### 5. Fungsi `strcmp` - Bandingkan String

```c
bool strcmp(char* str1, char* str2) {
    int i = 0;
    while (str1[i] != '\0' && str2[i] != '\0') {
        if (str1[i] != str2[i]) {
            return false;
        }
        i++;
    }
    return str1[i] == str2[i];
}
```

- Membandingkan dua string apakah sama atau tidak
- Loop untuk membandingkan setiap karakter
- Jika ada karakter yang berbeda → return `false`
- Setelah loop, cek apakah kedua string sama-sama selesai

#### 6. Fungsi `strcpy` - Copy String

```c
void strcpy(char* dst, char* src) {
    int i = 0;
    while (src[i] != '\0') {
        dst[i] = src[i];
        i++;
    }
    dst[i] = '\0';
}
```

- Menyalin string dari `src` ke `dst`
- Loop sampai menemukan null terminator di `src`
- Jangan lupa tambahkan null terminator di akhir `dst`

#### 7. Fungsi `clear` - Bersihkan Buffer

```c
void clear(byte* buf, unsigned int size) {
    unsigned int i;
    for (i = 0; i < size; i++) {
        buf[i] = 0;
    }
}
```

- Mengisi buffer dengan nilai 0 (kosongkan)
- Loop sebanyak `size` untuk mengisi setiap byte dengan 0

![implementasistd_lib.h](https://i.imgur.com/VKLzydF.png)

---

### c. Implementasikan perintah `echo`

Perintah ini mengambil argumen yang diberikan (karakter keyboard) untuk perintah `echo` dan mencetaknya ke shell.

### c. Implementasikan perintah `grep`

Perintah ini mencari baris yang cocok dengan pola dalam inputnya dan mencetak baris yang cocok. `grep` hanya akan mengambil satu argumen menggunakan piping (`|`) dari perintah `echo`. Output harus berupa bagian dari argumen yang di-pipe yang diteruskan ke `grep`. Jika argumen tidak cocok, mengembalikan `NULL`

Contoh penggunaan:
```bash
$> echo <argument> | grep <argument>
```

**Penyelesaian:**

#### 1. Fungsi `commandEcho` - Perintah Echo

```c
void commandEcho(char* arg) {
    printString(arg);
    printString("\r\n");
}
```

- Fungsi menerima parameter `arg` yang berisi text yang akan ditampilkan
- `printString(arg)`: Menampilkan text ke layar
- `printString("\r\n")`: Menambah baris baru (carriage return + line feed)
- **Fungsi sederhana**: Hanya menampilkan apa yang diketik user

**Contoh Penggunaan:**
```bash
$> echo Hello World
Hello World
```

#### 2. Fungsi `commandGrep` - Perintah Grep

```c
void commandGrep(char* piped, char* pattern) {
    int i = 0, k = 0;
    char line[128];
    
    while (1) {
        // Salin sampai '\n' atau '\0'
        k = 0;
        while (piped[i] != '\0' && piped[i] != '\n') {
            line[k++] = piped[i++];
        }
        line[k] = '\0';

        // Cek apakah cocok dengan pola
        if (strlen(line) > 0 && strstr(line, pattern)) {
            printString(line);
            printString("\r\n");
        }

        if (piped[i] == '\0') break;
        i++; // lewati '\n'
    }
}
```

- **Parameter:**
  - `piped`: Text yang diterima dari command sebelumnya (dari echo)
  - `pattern`: Kata yang dicari dalam text
- **Proses:**
  1. **Parse per baris**: Ambil text satu baris (sampai `\n` atau `\0`)
  2. **Cek pattern**: Gunakan `strstr()` untuk cari pattern dalam baris
  3. **Print hasil**: Jika ketemu, tampilkan baris tersebut
  4. **Loop**: Lanjut ke baris berikutnya sampai selesai

#### 3. Fungsi Pendukung `strstr` - Cari Substring

```c
bool strstr(char* haystack, char* needle) {
    int hLen = strlen(haystack);
    int nLen = strlen(needle);
    int i, j;

    for (i = 0; i <= hLen - nLen; i++) {
        j = 0;
        while (j < nLen && haystack[i + j] == needle[j]) {
            j++;
        }
        if (j == nLen) return true;
    }
    return false;
}
```

- Mencari `needle` (pattern yang dicari) dalam `haystack` (text)
- Loop melalui setiap posisi dalam `haystack`
- Di setiap posisi, cek apakah `needle` cocok karakter per karakter
- Return `true` jika ketemu, `false` jika tidak

#### 4. Fungsi `startsWith` - Cek Prefix

```c
bool startsWith(char* str, char* prefix) {
    int i = 0;
    while (prefix[i] != '\0') {
        if (str[i] != prefix[i]) {
            return false;
        }
        i++;
    }
    return true;
}
```

- Mengecek apakah string dimulai dengan prefix tertentu
- **Kegunaan**: Untuk cek apakah command dimulai dengan "echo " atau "grep "

#### 5. Parsing Command dalam `executeCommand`

```c
// Handle echo | grep
if (strlen(token1) > 0 && strlen(token2) > 0 && strlen(token3) == 0) {
    if (startsWith(token1, "echo ") && startsWith(token2, "grep ")) {
        char* echoArg = token1 + 5;    // Skip "echo "
        char* grepArg = token2 + 5;    // Skip "grep "
        commandGrep(echoArg, grepArg);
        return;
    }
}

// Handle command tanpa piping
if (strlen(token1) > 0 && strlen(token2) == 0 && strlen(token3) == 0) {
    if (startsWith(token1, "echo ")) {
        char* arg = token1 + 5;        // Skip "echo "
        commandEcho(arg);
        return;
    }
}
```

- **Parsing piping**: Split input berdasarkan karakter `|`
- **Token extraction**: Ambil bagian setelah "echo " dan "grep "
- **Routing**: Panggil fungsi yang sesuai berdasarkan command

![echo&grep](https://i.imgur.com/lOcAE00.png)

--- 
### d. Implementasikan Perintah `wc`
Perintah ini menghitung baris, kata, dan karakter dalam inputnya. `wc` tidak memerlukan argumen karena mendapat input dari pipe (`|`) dari perintah sebelumnya. Output harus berupa hitungan akhir dari argumen yang di-pipe yang diteruskan ke `wc`. Jika argumen tidak cocok, mengembalikan `NULL` atau `0`

Contoh penggunaan:
```bash
$> echo <argument> | wc
$> echo <argument> | grep <argument> | wc
```

**Penyelesaian:**

#### 1. Fungsi `commandWc` - Perintah Word Count

```c
void commandWc(char* piped) {
    int lineCount = 0, wordCount = 0, charCount = 0;
    bool inWord = false;
    int i = 0;

    // Hitung jumlah karakter, kata, dan baris
    while (piped[i] != '\0') {
        char c = piped[i];

        // Hitung karakter
        charCount++;

        // Cek apakah spasi (untuk menghitung kata)
        if (c == ' ' || c == '\n') {
            if (inWord) {
                inWord = false;
                wordCount++;
            }
        } else {
            inWord = true;
        }

        // Cek newline (untuk menghitung baris)
        if (c == '\n') {
            lineCount++;
        }

        i++;
    }

    // Jika terakhir adalah kata tanpa spasi di belakang, hitung sebagai satu kata
    if (inWord) {
        wordCount++;
    }

    // Tampilkan hasil
    printString("Jumlah baris: ");
    printNumber(lineCount);
    printString(", Jumlah kata: ");
    printNumber(wordCount);
    printString(", Jumlah karakter: ");
    printNumber(charCount);
    printString("\r\n");
}
```

- **Parameter:** `piped` - text yang diterima dari command sebelumnya
- **Variable penghitung:**
  - `lineCount`: Menghitung jumlah baris (berdasarkan karakter `\n`)
  - `wordCount`: Menghitung jumlah kata (berdasarkan pemisahan spasi)
  - `charCount`: Menghitung jumlah karakter total
  - `inWord`: Flag untuk mengetahui apakah sedang dalam kata

- **Proses penghitungan:**
  1. **Karakter**: Setiap karakter yang dibaca (`charCount++`)
  2. **Kata**: Ketika bertemu spasi/newline dan sebelumnya dalam kata
  3. **Baris**: Ketika bertemu karakter newline (`\n`)
  4. **Kata terakhir**: Jika text berakhir tanpa spasi, tetap dihitung sebagai kata

#### 2. Fungsi `printNumber` - Menampilkan Angka

```c
void printNumber(int num) {
    char buf[12];
    int i = 0, j;
    bool neg = false;
    if (num < 0) {
        neg = true;
        num = -num;
    }
    if (num == 0) {
        buf[i++] = '0';
    } else {
        while (num > 0) {
            buf[i++] = (char) ('0' + mod(num, 10));
            num /= 10;
        }
        if (neg) buf[i++] = '-';
    }
    buf[i] = '\0';
    // Reverse
    for (j = 0; j < i/2; j++) {
        char temp = buf[j];
        buf[j] = buf[i - j - 1];
        buf[i - j - 1] = temp;
    }
    printString(buf);
}
```

- Mengkonversi integer menjadi string untuk ditampilkan
- **Proses:**
  1. Handle angka negatif
  2. Handle angka 0
  3. Ekstrak digit satu per satu (dari belakang)
  4. Reverse string hasil untuk mendapat urutan yang benar
- **Kegunaan:** Menampilkan hasil penghitungan dari `commandWc`

#### 3. Parsing Command dalam `executeCommand` untuk WC

```c
// Handle echo | grep | wc
if (strlen(token1) > 0 && strlen(token2) > 0 && strlen(token3) > 0) {
    if (startsWith(token1, "echo ") && startsWith(token2, "grep ") && strcmp(token3, "wc") == true) {
        char* echoArg = token1 + 5;
        char* grepArg = token2 + 5;

        char hasilGrep[128];
        clear(hasilGrep, 128);

        if (strstr(echoArg, grepArg)) {
            strcpy(hasilGrep, echoArg);
        }

        commandWc(hasilGrep);
        return;
    }
}

// Handle echo | wc
if (strlen(token1) > 0 && strlen(token2) > 0 && strlen(token3) == 0) {
    if (startsWith(token1, "echo ") && strcmp(token2, "wc") == true) {
        char* echoArg = token1 + 5;
        commandWc(echoArg);
        return;
    }
}
```

- **Triple piping** (`echo | grep | wc`):
  1. Ambil output dari echo
  2. Filter dengan grep
  3. Hitung hasil filter dengan wc
- **Double piping** (`echo | wc`):
  1. Ambil output dari echo langsung
  2. Hitung dengan wc

## Contoh Penggunaan:

### 1. Echo dengan WC
```bash
$> echo Hello World
Hello World

$> echo Hello World | wc
Jumlah baris: 0, Jumlah kata: 2, Jumlah karakter: 11
```

### 2. Echo dengan Grep dan WC
```bash
$> echo Hello World Test | grep World
Hello World Test

$> echo Hello World Test | grep World | wc
Jumlah baris: 0, Jumlah kata: 3, Jumlah karakter: 16
```

### 3. Echo dengan Grep (Tidak Ketemu) dan WC
```bash
$> echo Hello World | grep xyz | wc
Jumlah baris: 0, Jumlah kata: 0, Jumlah karakter: 0
```

![EchoGrepWc](https://i.imgur.com/GN2AczX.png)

---

### e. Buat otomatisasi untuk mengompilasi dengan melengkapi file [`makefile`](./makefile).

Untuk mengompilasi program, perintah `make build` akan digunakan. Semua hasil program yang dikompilasi akan disimpan di direktori [`bin/`](./bin). Untuk menjalankan program, perintah `make run` akan digunakan.

**Penyelesaian:**

##### 1. Struktur Makefile

File makefile yang digunakan sudah mengatur proses build menjadi beberapa tahapan, yaitu:

- **prepare**: Membuat image floppy kosong (`floppy.img`) menggunakan `dd`.
- **bootloader**: Meng-assemble bootloader dari file ASM ke binary.
- **stdlib**: Meng-compile library C ke object file.
- **kernel**: Meng-assemble kernel ASM dan meng-compile kernel C ke object file.
- **link**: Melakukan linking semua object file menjadi satu file kernel, lalu menulis kernel ke image floppy.
- **build**: Menjalankan semua tahapan di atas secara berurutan.
- **run**: Menjalankan sistem menggunakan Bochs.
- **clean**: Menghapus semua file hasil kompilasi di direktori `bin/`.

##### 2. Penjelasan Isi Makefile

```makefile
# Compiler and linker
ASM=nasm
CC=bcc
LD=ld86

# Flags
ASM_FLAGS=-f as86
CC_FLAGS=-ansi -c -o
LD_FLAGS=-d -o

# Directories
SRC_DIR=src
INCLUDE_DIR=include
BIN_DIR=bin

# Target files
BOOTLOADER=$(BIN_DIR)/bootloader.bin
KERNEL_ASM=$(BIN_DIR)/kernel_asm.o
KERNEL_C=$(BIN_DIR)/kernel.o
STDLIB=$(BIN_DIR)/std_lib.o
SYSTEM_IMAGE=$(BIN_DIR)/floppy.img
```

- Mendefinisikan tools, flags, dan direktori yang digunakan.
- Menentukan nama file hasil kompilasi.

---

##### 3. Proses Build

**a. Membuat Image Floppy**

```makefile
prepare:
	@echo "Creating disk image..."
	dd if=/dev/zero of=$(SYSTEM_IMAGE) bs=512 count=2880
```
- Membuat file image kosong berukuran 1.44MB (2880 sektor x 512 byte).

**b. Meng-assemble Bootloader**

```makefile
bootloader:
	@echo "Assembling bootloader..."
	$(ASM) -f bin $(SRC_DIR)/bootloader.asm -o $(BOOTLOADER)
	dd if=$(BOOTLOADER) of=$(SYSTEM_IMAGE) bs=512 count=1 conv=notrunc
```
- Bootloader di-assemble ke format binary dan ditulis ke sektor pertama image.

**c. Compile Standard Library**

```makefile
stdlib:
	@echo "Compiling standard library..."
	$(CC) $(CC_FLAGS) $(STDLIB) -I$(INCLUDE_DIR) $(SRC_DIR)/std_lib.c
```
- Library C dikompilasi menjadi object file.

**d. Compile dan Assemble Kernel**

```makefile
kernel:
	@echo "Assembling kernel assembly code..."
	$(ASM) $(ASM_FLAGS) $(SRC_DIR)/kernel.asm -o $(KERNEL_ASM)
	@echo "Compiling kernel C code..."
	$(CC) $(CC_FLAGS) $(KERNEL_C) -I$(INCLUDE_DIR) $(SRC_DIR)/kernel.c
```
- Kernel ASM dan C dikompilasi menjadi object file.

**e. Linking dan Menulis Kernel ke Image**

```makefile
link:
	@echo "Linking kernel..."
	$(LD) $(LD_FLAGS) $(BIN_DIR)/kernel $(KERNEL_C) $(KERNEL_ASM) $(STDLIB)
	@echo "Writing kernel to disk image..."
	dd if=$(BIN_DIR)/kernel of=$(SYSTEM_IMAGE) bs=512 seek=1 conv=notrunc
```
- Semua object file di-link menjadi satu file kernel, lalu kernel ditulis ke sektor berikutnya pada image.

**f. Build dan Run**

```makefile
build: prepare bootloader stdlib kernel link
	@echo "Build completed successfully"

run:
	@echo "Running system in Bochs..."
	bochs -f bochsrc.txt -q
```
- `make build` menjalankan seluruh proses build.
- `make run` menjalankan sistem menggunakan Bochs.

**g. Clean**

```makefile
clean:
	@echo "Cleaning build files..."
	rm -f $(BIN_DIR)/*.o $(BIN_DIR)/*.bin $(BIN_DIR)/kernel
```
- Menghapus semua file hasil kompilasi.

---

##### 4. Contoh Penggunaan

**Build:**
```sh
make build
```
Output:
```
Creating disk image...
Assembling bootloader...
Compiling standard library...
Assembling kernel assembly code...
Compiling kernel C code...
Linking kernel...
Writing kernel to disk image...
Build completed successfully
```

**Run:**
```sh
make run
```
Output:
```
Running system in Bochs...
```
(Sistem operasi akan dijalankan di emulator Bochs)

**Clean:**
```sh
make clean
```
Output:
```
Cleaning build files...
```

---
![cmd](https://i.imgur.com/VPblByX.png)

---

