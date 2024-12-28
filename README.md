# SixGPT Miner Kurulum Rehberi
Bu rehber, SixGPT projesi için bir miner kurulumu yaparken izlenecek adımları içermektedir. Screen komutları ile çalışmaya uygun şekilde hazırlanmıştır.

# Ön Hazırlık Aşaması

- Ubuntu 20.04 veya üstü bir Linux sunucu. Ben hem Volara hem de SixGPT aynı sunucuda çalıştırıyorum, sunucu Hetzner CX32.
- Vana cüzdanı oluşturun ya da mevcut Vana cüzdanınıza minimum 0.1 $VANA atın. (Ben 0.5 $VANA attım, uzunca süre yetecektir.)
- https://sixgpt.xyz sitesine girerek oluşturduğunuz cüzdanınızı bağlayın. **Not: Kurulumdan önce muhakkak bu işlemi gerçekleştirin.**

# Kurulum Kodları

**1. Sunucuyu Güncelleyin**

```bash
sudo apt update && sudo apt upgrade -y 
```

**2. Gerekli Paketleri Yükleyin**

```bash
sudo apt install curl wget git screen build-essential -y
```

**3. Docker Kurulumunu Gerçekleştirin**

```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
sudo add-apt-repository "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu focal stable"
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y
```

Docker Compose kurmak için:
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

**4. Arka Planda Çalıştırmak İçin Screen Açın**

Screen komutu ile işlemleri arka planda yürütebilmek için yeni bir oturum başlatın:
```bash
screen -S sixgpt
```

**5. Proje Dosyalarını Çekin ve Dosyaya Giriş Yapın**

```bash
git clone https://github.com/sixgpt/miner.git
cd miner
```

**6. .env Dosyasını Oluşturun**

```bash
touch .env
nano .env
```

Bu aşamadan sonra oluşturduğumuz .env uzantısının içine gerekli bilgileri yazalım. Bunu kopyaladıktan sonra üstteki kısma kullanacağınız cüzdanın Private Key'ini yazın ve bunu oluşturduğunuz .env klasörüne yapıştırın.

```bash
VANA_PRIVATE_KEY=<CUZDAN_PRIVATE_KEY>
VANA_NETWORK=mainnet
OLLAMA_API_URL=http://ollama:11434/api
```

Daha sonra CTRL + C yapın Y + ENTER yaparak dosyayı kaydedip kapatın.

**7. Docker ile Miner Çalıştırmaya Başlayın**

```bash
docker-compose up -d
```

**8. Miner Loglarını Görüntüleyin**

```bash
docker-compose logs -f
```

Kurulumu Screen ile yaptığımız için bu aşamadan sonra CTRL + A + D basarak ekranı kapatabilirsiniz. Arka planda çalışmaya devam edecektir. Eğer durdurup tekrar çalıştırmanız gerekirse aşağıdaki kodları kodları kullanabilirsiniz.

Durdurmak İçin
```bash
docker-compose down
```

Tekrar Çalıştırmak İçin
```bash
docker-compose up -d
```

**9. Logları Kontrol Etmeniz Gerekirse**

Sunucuya giriş yaptıktan sonra, 
```bash
screen -ls
```

Çıktıların içinde 
xxxx.sixgpt (xx/xx/xxxx xx:xx:xx AM/PM)    (Detached)
Şeklinde bir çıktı göreceksiniz. Baştaki 4 haneli rakamı aşağıdaki kodun devamına yazın.

```bash
screen -r xxxx
```

Ekranı tekrar kapatmak için CTRL + A + D kombinasyonunu kullanın.
