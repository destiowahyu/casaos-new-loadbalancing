# casaos-new-loadbalancing

1. untuk file linux armbian dengan wifi on bisa di [download disini](https://www.mediafire.com/file/2ywqxi302gzrp2i/Armbian_21.08.1_Amlogic-GXL_bullseye_current_5.10.60.img.xz/file)
2. Lihat IP di router ISP
3. Konek menggunakan IP tersebut di terminal
4. Lakukan :

       apt update
       apt upgrade
5. setting ip static untuk eth0 dan eth1, serta lakukan loadbalancing
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


6. Install casa os
   
       curl -fsSL https://get.casaos.io | sudo bash
7. Konfigurasi Docker
   
   **-Install Docker Compose**

       apt install docker-compose
   **-Cek apakah docker compose sudah terinstall**

       docker-compose --version
8. Proses Pembuatan webserver
    
    a. buat direktori **web** di dalam folder **DATA**

        mkdir /DATA/web
    b. Masuk ke direktori **web**

        cd /DATA/web
    c. copy file [docker-compose.yml](https://github.com/destiowahyu/casaos-new-loadbalancing/blob/main/docker-compose.yml) dari direktori github ini ke dalam folder **web** yang telah dibuat tadi

    d. copy juga file [Dockerfile](https://github.com/destiowahyu/casaos-new-loadbalancing/blob/main/Dockerfile) dan masukkan kedalam direktori **web**

    e. buat direktori htdocs sampai htdocs5 dan juga mysql_data

        mkdir htdocs
        mkdir htdocs2
        mkdir htdocs3
        mkdir htdocs4
        mkdir htdocs5
        mkdir mysql_data

    f. jalankan perintah docker compose

        docker-compose up -d --build

    g. Buat docker compose auto start saat boot

    - Buat file unit systemd

          sudo nano /etc/systemd/system/docker-compose@.service
    - Tambahkan ini sebagai isinya

          [Unit]
          Description=Docker Compose Service for %i
          Requires=docker.service
          After=docker.service
              
          [Service]
          Type=oneshot
          RemainAfterExit=yes
          WorkingDirectory=/DATA/web
          ExecStart=/usr/local/bin/docker-compose up -d
          ExecStop=/usr/local/bin/docker-compose down
          TimeoutStartSec=0
              
          [Install]
          WantedBy=multi-user.target

       `WorkingDirectory=/DATA/web` Menyesuaikan dengan lokasi file docker-compose.yml

      - Aktifkan service

            sudo systemctl enable docker-compose@webserver.service
            sudo systemctl start docker-compose@webserver.service

          `webserver` bebas mau dinamai apa

9. Konfigurasi Zerotier supaya bisa di remote
    
    a. install zerotier

       curl -s https://install.zerotier.com | sudo bash
   b. cek dulu
   
       zerotier-cli info
   c. join ke network

       zerotier-cli join <network id>
   Ganti `<networkid>` Menyesuaikan network id di zerotier
11. Supaya bisa remote yang lain juga di jaringan lokal, misal router
    
    a. Aktifkan NAT Masquerade
    diliat dulu interfacenya mau pake `eth0` atau `eth1`
    misal mau pake `eth1` karena biasanya `eth1` pakai usb to lan gigabit, jadinya gini :

        sudo iptables -t nat -A POSTROUTING -o eth1 -j MASQUERADE
        sudo iptables -A FORWARD -i ztc25nztai -o eth1 -m state --state RELATED,ESTABLISHED -j ACCEPT
        sudo iptables -A FORWARD -i eth1 -o ztc25nztai -j ACCEPT

    b. pakai ini supaya pengaturan tersimpan setelah reboot

        sudo netfilter-persistent save
        sudo netfilter-persistent reload



    
    


   


   
