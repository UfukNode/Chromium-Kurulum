## Chromium Docker Kurulumu (VPS/Linux Cihazlar İçin)

Boundless Signal gibi tarayıcıdan çalışan işlemleri, GUI olmayan bir VPS ya da Linux cihaz üzerinde kullanabilmek için hazırlanmıştır.
Kurulum sonrası `localhost` ya da sunucu IP üzerinden erişim sağlayarak google benzeri tarayıcıyı uzaktan kullanabilirsiniz.

---

### 🔧 Gereksinimler

* Ubuntu 20.04 / 22.04
* Docker & Docker Compose

---

## 1- Docker Kurulumu

```bash
sudo apt update -y && sudo apt upgrade -y

# Eski docker varsa kaldır
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do 
  sudo apt-get remove $pkg; 
done

# Gerekli bağımlılıklar
sudo apt-get install ca-certificates curl gnupg -y
sudo install -m 0755 -d /etc/apt/keyrings

# GPG key ekle
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Docker deposu ekle
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Docker kurulumu
sudo apt update -y
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

# Kontrol
docker --version
```

---

## 2- Lokasyonu Kontrol Et:

```bash
realpath --relative-to /usr/share/zoneinfo /etc/localtime
```

---

## 3- Chromium Dizini Oluştur

```bash
mkdir ~/chromium && cd ~/chromium
nano docker-compose.yaml
```

---

## 4- Docker Compose Ayarları

Aşağıdaki içeriği yapıştır:

```yaml
version: "3.3"
services:
  chromium:
    image: lscr.io/linuxserver/chromium:latest
    container_name: chromium
    security_opt:
      - seccomp:unconfined
    environment:
      - CUSTOM_USER=kullanıcı-adı-belirle
      - PASSWORD=şifre-belirle
      - PUID=1000
      - PGID=1000
      - TZ=yukarıda-çıkan-lokasyonu-gir
      - CHROME_CLI=https://x.com/UfukDegen
    volumes:
      - /home/$USER/chromium/config:/config
    ports:
      - 3010:3000
      - 3011:3001
    shm_size: "1gb"
    restart: unless-stopped
```

- "TZ" kısmına yukarıda çıkan lokasyonu gir.
- "kullanıcı-adı-belirle" kısmına bir kullanıcı adı gir (farketmez, unutma yeter).
- "şifre-belirle" kısmına yeni bir şifre belirle ve gir (farketmez, unutma yeter).

Kaydetmek için: `CTRL + X` > `Y` > `Enter`

---

## 4. Tarayıcıyı Başlat

```bash
cd ~/chromium
docker compose up -d
```

---

## 5. Tarayıcıya Erişim

- **Kendi cihazındaysan tarayıcıya aşağıdaki bağlantıyı gir:**
  `http://localhost:3010`
  `https://localhost:3011`

- **VPS üzerindeyse VPS IP'ni <sunucu-ip> kısmına gir ve aşağıdaki bağlantıyı gir:**
  `http://<sunucu-ip>:3010`
  `https://<sunucu-ip>:3011`

- Tarayıcıya girdikten sonra yukarıda belirlemiş olduğun giriş bilgilerini gir.

---
