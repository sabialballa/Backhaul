# Backhaul

مخزن (Mirror) فایل‌های نصب و باینری‌های **Backhaul**؛ ابزار تونل معکوس (Reverse Tunnel) برای اتصال سرور ایران به سرور خارج، مناسب سرورهایی که دسترسی مستقیم به GitHub ندارند.

> A mirror of the Backhaul installer script and prebuilt binaries, for servers that cannot reach GitHub releases directly.

---

## 📦 محتویات مخزن

| فایل | توضیح |
|------|-------|
| `backhaul.sh` | اسکریپت نصب و مدیریت Backhaul |
| `backhaul_amd64.tar.gz` | باینری برای سرورهای x86_64 (AMD64) |
| `backhaul_arm64.tar.gz` | باینری برای سرورهای ARM64 |

---

## 🚀 نصب سریع

روی سرور (با دسترسی root) اجرا کنید:

```bash
bash <(curl -Ls https://raw.githubusercontent.com/sabialballa/Backhaul/main/backhaul.sh)
```

یا ابتدا اسکریپت را دانلود و سپس اجرا کنید:

```bash
wget -O backhaul.sh https://raw.githubusercontent.com/sabialballa/Backhaul/main/backhaul.sh
chmod +x backhaul.sh
sudo ./backhaul.sh
```

---

## 🔧 نصب دستی

۱. معماری سرور را بررسی کنید:

```bash
uname -m
```

- خروجی `x86_64` → فایل `backhaul_amd64.tar.gz`
- خروجی `aarch64` → فایل `backhaul_arm64.tar.gz`

۲. دانلود و استخراج (نمونه برای AMD64):

```bash
wget https://raw.githubusercontent.com/sabialballa/Backhaul/main/backhaul_amd64.tar.gz
tar -xzf backhaul_amd64.tar.gz
chmod +x backhaul
sudo mv backhaul /usr/local/bin/
```

۳. بررسی نصب:

```bash
backhaul -v
```

---

## ⚙️ نمونه کانفیگ

**سرور (معمولاً ایران):**

```toml
[server]
bind_addr = "0.0.0.0:3080"
transport = "tcp"
token = "YOUR_SECRET_TOKEN"
log_level = "info"
ports = ["443", "8443"]
```

**کلاینت (سرور خارج):**

```toml
[client]
remote_addr = "IRAN_SERVER_IP:3080"
transport = "tcp"
token = "YOUR_SECRET_TOKEN"
log_level = "info"
```

اجرا:

```bash
backhaul -c /etc/backhaul/config.toml
```

> مقدار `token` را حتماً تغییر دهید و در دو طرف یکسان باشد.

---

## 🛠 اجرا به‌صورت سرویس (systemd)

```ini
# /etc/systemd/system/backhaul.service
[Unit]
Description=Backhaul Reverse Tunnel
After=network.target

[Service]
ExecStart=/usr/local/bin/backhaul -c /etc/backhaul/config.toml
Restart=always
RestartSec=3
LimitNOFILE=1048576

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now backhaul
sudo systemctl status backhaul
```

---

## ❓ عیب‌یابی

- **اتصال برقرار نمی‌شود:** پورت تونل را در فایروال هر دو سرور باز کنید و مطمئن شوید `token` یکسان است.
- **خطای معماری (Exec format error):** فایل مناسب معماری سرور را دانلود کرده‌اید؟ (`uname -m`)
- **مشاهده لاگ:** `journalctl -u backhaul -f`

---

## 🙏 منبع و اعتبار

این مخزن صرفاً یک آینه (Mirror) برای دسترسی راحت‌تر است. پروژه اصلی و مستندات کامل را در مخزن رسمی Backhaul ببینید و برای هر باگ یا قابلیت جدید به آن‌جا مراجعه کنید.

## ⚠️ سلب مسئولیت

استفاده از این ابزار بر عهده خود شماست. لطفاً قوانین کشور و ارائه‌دهنده سرور خود را رعایت کنید.
