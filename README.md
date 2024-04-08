# casaos-new-loadbalancing

1. untuk file linux armbian dengan wifi on bisa di [download disini](https://www.mediafire.com/file/2ywqxi302gzrp2i/Armbian_21.08.1_Amlogic-GXL_bullseye_current_5.10.60.img.xz/file)
2. Lihat IP di router ISP
3. Konek menggunakan IP tersebut di terminal
4. Lakukan :

       apt update
6. setting ip static untuk eth0 dan eth1, serta lakukan loadbalancing
   __Lakukan ini dulu__

       sudo apt-get update
       sudo apt-get install ifenslave


   __-Konfigurasi Bounding (gabungan eth0 dan eth0)__
   
       sudo nano /etc/network/interfaces.d/bond0
   
     __-tambahkan kode ini :__

       auto bond0
       iface bond0 inet static
          address 192.168.100.10
          netmask 255.255.255.0
          gateway 192.168.100.1
          dns-nameservers 192.168.100.1 1.1.1.1
          bond-slaves eth0 eth1
          bond-mode balance-rr
          bond-miimon 100
          bond-downdelay 200
          bond-updelay 200


     __-tambah ip static eth0 dan eth1__

         nano /etc/network/interfaces
   
     __-tambahkan kode ini :__
   
       auto eth0
       iface eth0 inet static
          address 192.168.100.8
          netmask 255.255.255.0
          gateway 192.168.100.1
          dns-nameservers 192.168.100.1 1.1.1.1
   
       auto eth1
       iface eth1 inet static
          address 192.168.100.9
          netmask 255.255.255.0
          gateway 192.168.100.1
          dns-nameservers 192.168.100.1 1.1.1.1
   
      __-lalu :__

       sudo systemctl restart networking
       sudo reboot

     __-jadi nanti IPnya__

   **bond0 : 192.168.100.10**
   
   **eth0 : 192.168.100.8**
   
   **eth1 : 192.168.100.9**


8. Install casa os
   
       curl -fsSL https://get.casaos.io | sudo bash
9. Konfigurasi Docker
   
   **-Install Docker Compose**

       apt install docker-compose
   **-Cek apakah docker compose sudah terinstall**

       docker-compose --version
10. Proses Pembuatan webserver
    a. buat direktori **web** di dalam folder **DATA**

        mkdir /DATA/web
    b. Masuk ke direktori **web**

        cd /DATA/web
    c. copy file [docker-compose.yml](https://github.com/destiowahyu/casaos-new-loadbalancing/blob/main/docker-compose.yml) dari direktori github ini ke dalam folder **web** yang telah dibuat tadi
    d. copy juga file [Dockerfile]() dan masukkan kedalam direktori web
    


   


   
