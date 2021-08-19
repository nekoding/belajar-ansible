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

## Menjalankan command di ansible

Untuk dapat menjalankan command linux di ansible 