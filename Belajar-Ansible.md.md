# Ansible
Digunakan untuk melakukan otomatisasi dalam melakukan konfigurasi di dalam sebuah server.

## Persiapan

Download ansible di linux mint :

```sh

sudo apt install ansible

```

Download OpenSSH

```sh

sudo apt install openssh-server

```

## Langkah - langkah

1. Buat SSH Key Pair untuk melakukan koneksi ke server

```sh

ssh-keygen -t ed25519 -C " Comment "

```

2. Menginstall ssh public key ke dalam server

```sh

ssh-copy-id -i PUBLIC_KEY_PATH IP_ADDRESS_SERVER

```

Jika nama dari Public key file bukan nama default ( id_ed25519 ) maka gunakan command berikut untuk melakukan koneksi ke server dengan custom public key

```sh

ssh -i PUBLIC_KEY_PATH username@IP_SERVER

```

Apabila ingin melakukan koneksi ssh dengan mudah dapat menjalankan command berikut :

```sh

alias ssha = 'eval ${ssh-agent} && ssh-add'

```

3. Buat sebuah file yang nantinya isi dengan alamat domain / ip dari server yang akan diremote

```

192.168.x.x
192.168.x.x

```

setelah itu jalankan command

```sh

ansible all --key-file PUBLIC_KEY_PATH -i NAMA_FILE_YANG_NYIMPAN_IP_SERVER -m NAMA_MODUL

```

contoh : 

```sh

ansible all --key-file ~/.ssh/id_ed25519.pub -i enggar-server -m ping

```

Command diatas akan melakukan PING ke dalam server untuk mengecek apakah bisa terhubung / tidak.

## Konfigurasi

Untuk contoh konfigurasi dari ansible dapat ditemukan di file `/etc/ansible/ansible.cfg` . Jika ingin melakukan override maka tinggal buat saja klone dari konfigurasi tersebut dan jalankan command

```sh

ansible all -m NAMA_MODUL

```

Nantinya secara otomatis ansible akan menjalankan konfigurasi yang ada di folder dengan nama file `ansible.cfg` tersebut dan tidak menggunakan konfigurasi dari file `/etc/ansible/ansible.cfg`.

## Menjalankan command di ansible (ad-hoc)

Untuk dapat menjalankan command linux di ansible dapat dengan cara

```sh

ansible all -m apt -a update_cache=true --become --ask-become-pass

```

breakdown command :  

`-m apt`  : Menggunakan modul apt. apt merupakan modul yang digunakan untuk menginstall package di server berbasis ubuntu / debian.   
  
`-a update_cache=true` : Mengirimkan argument untuk melakukan update repository didalam server / setara dengan command `apt update`  
  
`--become --ask-become-pass` : Menjalankan command dengan melakukan privilege escalation apabila dibutuhkan. 

Karena menjalankan dalam mode `all` maka command akan gagal apabila password dari user `sudo` tiap server berbeda beda.
Untuk lebih lengkap mengenai penjelasan fungsi dari masing - masing flag bisa dibaca disini saja [Ansible adhoc guide](https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html#intro-adhoc)

## Menulis ansible playbook

Buat sebuah file dengan nama `install_apache.yml`

lalu isikan seperti ini :

```yml
---

- hosts: all            // Menargetkan semua hosts server 
  become: true          // Melakukan privelege escalation ke user yang dapat melakukan command
  tasks:                // Mendifiniskan task yang akan dilakukan oleh ansible

  - name: install apache2 package   // Title dari proses yang dijalankan
    apt:                // Mengeksekusi module apt
      name: apache2     // Nama package yang akan diinstall
      state: latest     // Memastikan versi dari package paling baru

  - name: xxx           // Title dari proses yang dijalankan
    apt:                // Mengeksekusi module apt
      name: xxx         // Nama package yang akan diinstall
      state: latest     // Memastikan versi dari package paling baru
      

```

Dokumentasi dari module apt : [apt ansible module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html)


State : 
 - latest : Mencari paling baru
 - absent : Menghapus

Dokumentasi penggunaan state di ansible : [state ansible doc](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/file_module.html#parameter-state)

Untuk menjalankan command di playbook gunakan command 

```sh

ansible-playbook --ask-become-pass install_apache.yml

```

