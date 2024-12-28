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

Ubuntu sunucunuza Docker ve Docker Compose kurulumu için gerekli komutları paylaşıyorum:

1. Önce eski Docker sürümlerini kaldıralım (eğer varsa):
```bash
sudo apt-get remove docker docker-engine docker.io containerd runc
```

2. Gerekli paketleri yükleyelim:
```bash
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg lsb-release
```

3. Docker'ın GPG anahtarını ekleyelim:
```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

4. Docker repository'sini ekleyelim:
```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

5. Docker Engine'i yükleyelim:
```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

6. Docker Compose'u yükleyelim:
```bash
sudo apt-get update
sudo apt-get install docker-compose-plugin
```

7. Docker'ın düzgün çalıştığını kontrol edelim:
```bash
sudo docker run hello-world
```

8. (Opsiyonel) Docker'ı sudo olmadan kullanabilmek için kullanıcınızı docker grubuna ekleyin:
```bash
sudo usermod -aG docker $USER
```
Bu komutu çalıştırdıktan sonra oturumu kapatıp tekrar açmanız gerekecektir.

9. Docker ve Docker Compose versiyonlarını kontrol edebilirsiniz:
```bash
docker --version
docker compose version
```

Bu komutları sırasıyla çalıştırdığınızda Docker ve Docker Compose sisteminize kurulmuş olacaktır. Herhangi bir sorunla karşılaşırsanız belirtebilirsiniz.


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
