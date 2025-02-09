# Pipe Network Kurulum ve Yapılandırma Rehberi

Bu dökümantasyon, Pipe Network Node'u kurma ve yapılandırma sürecini adım adım açıklamaktadır. Aşağıdaki adımları izleyerek sisteminizi hazırlayabilir ve Pipe POP Node'unu başlatabilirsiniz.

## Sistem Gereksinimleri
- **RAM**: Minimum 4GB, daha yüksek değerler önerilir.
- **Disk Alanı**: Minimum 100GB, 200-500GB önerilir.

## Adım 1: Güncellemeleri Yükleyelim
Aşağıdaki komutlarla sisteminizi güncelleyip gerekli paketleri yükleyebilirsiniz:

``
sudo apt update && sudo apt upgrade -y
sudo apt install curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang aria2 bsdmainutils ncdu unzip libleveldb-dev -y

2. Dizinleri Oluşturalım

mkdir -p /root/pipenetwork
mkdir -p /root/pipenetwork/download_cache
cd /root/pipenetwork


3. Dizinlerin Oluşturulması
Node için gerekli dizinleri oluşturun:


mkdir -p /root/pipenetwork
mkdir -p /root/pipenetwork/download_cache
cd /root/pipenetwork



3. Port Açma
Pipe Network ile iletişim için gerekli portu açın:

ufw allow 8003/tcp



4. Pipe Network Dosyasının İndirilmesi
Eğer Pipe Network'den bir waitlist maili aldıysanız, maildeki URL ile pop dosyasını indirin:

curl -L -o pop "<URL_FROM_EMAIL>"

Mail almadıysanız, alternatif olarak aşağıdaki komutla dosyayı indirin:

wget -O pop "https://dl.pipecdn.app/v0.2.4/pop"



5. Dosyanın Çalıştırılabilir Hale Getirilmesi
pop dosyasını çalıştırılabilir yapmak için aşağıdaki komutu kullanın:

chmod +x pop



6. Kaydolma ve Devam Etme
Pipe Network’e kaydolmak için aşağıdaki komutu çalıştırın. Referral linkinizi kullanarak kaydolduğunuzdan emin olun:

./pop --signup-by-referral-route bd5a975847962ddf



7. pipe-pop Servisinin Yapılandırılması
Bir servis dosyası oluşturun ve nano ile düzenleyin:

nano /etc/systemd/system/pipe-pop.service

Aşağıdaki örnek yapılandırmayı kendi sisteminize göre ayarlayın:

[Unit]
Description=Pipe POP Node Service
After=network.target
Wants=network-online.target

[Service]
User=root
Group=root
ExecStart=/root/pipenetwork/pop --ram=12 --pubKey <Your_Pubkey_Address> --max-disk 300 --cache-dir /var/cache/pop/download_cache
Restart=always
RestartSec=5
LimitNOFILE=65536
LimitNPROC=4096
StandardOutput=journal
StandardError=journal
SyslogIdentifier=dcdn-node
WorkingDirectory=/root/pipenetwork

[Install]
WantedBy=multi-user.target




8. Systemd Yeniden Yüklenmesi ve Servisin Başlatılması
Servisi yeniden yükleyin ve başlatın:

sudo systemctl daemon-reload
sudo systemctl enable pipe-pop
sudo systemctl start pipe-pop



9. Servisin Durumunun Kontrol Edilmesi
Servisin düzgün çalışıp çalışmadığını kontrol edin:

sudo systemctl status pipe-pop



10. Puan ve Uptime Bilgilerinin Kontrol Edilmesi
Node’un puan ve uptime skorlarını görmek için şu komutu kullanabilirsiniz:

./pop --status



11. Log Kontrolü
Node loglarını kontrol etmek için aşağıdaki komutu çalıştırabilirsiniz:

journalctl -u pipe-pop -f
