## Деплой на Ubuntu

### 1. Перенос файлов с Windows на Ubuntu

**Способ A — через `scp` (из PowerShell на Windows):**

```powershell
scp -r C:\Users\user\Desktop\meowbalt-main\* user@ubuntu-ip:/home/user/meowbalt/
```

**Способ B — через Tailscale (если оба устройства в Tailscale):**

```powershell
scp -r C:\Users\user\Desktop\meowbalt-main\* 100.xx.xx.xx:/home/user/meowbalt/
```

_(замени `100.xx.xx.xx` на Tailscale IP твоего Ubuntu)_

**Способ C — через rsync (если есть SSH доступ):**

```powershell
rsync -avz --exclude='venv/' --exclude='*.db' --exclude='__pycache__/' C:\Users\user\Desktop\meowbalt-main/ user@ubuntu-ip:/home/user/meowbalt/
```

---

### 2. На Ubuntu сервере

```bash
# Создай папку
mkdir -p ~/meowbalt
cd ~/meowbalt

# Если файлы ещё не перенёс — скопируй их сюда

# Проверь Python (должен быть 3.10+)
python3 --version

# Создай виртуальное окружение
python3 -m venv venv
source venv/bin/activate

# Установи зависимости
pip install -r requirements.txt

# Запусти сервер
python server.py
```

Сервер запустится на `0.0.0.0:8000` — будет доступен по IP твоего Ubuntu.

---

### 3. Автозапуск через systemd

```bash
sudo nano /etc/systemd/system/meowbalt.service
```

Вставь:

```ini
[Unit]
Description=Meowbalt Video Feed
After=network.target

[Service]
Type=simple
User=bushy
WorkingDirectory=/home/bushy/meowbalt
ExecStart=/home/bushy/meowbalt/venv/bin/python server.py
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

_(замени `bushy` на своё имя пользователя)_

```bash
# Включи и запусти
sudo systemctl enable meowbalt
sudo systemctl start meowbalt
sudo systemctl status meowbalt
```

---

### 4. Как обновлять файлы

**С Windows на Ubuntu (каждый раз при изменениях):**

```powershell
# Из PowerShell:
scp C:\Users\user\Desktop\meowbalt-main\*.html user@ubuntu-ip:/home/user/meowbalt/
scp C:\Users\user\Desktop\meowbalt-main\*.css user@ubuntu-ip:/home/user/meowbalt/
scp C:\Users\user\Desktop\meowbalt-main\*.js user@ubuntu-ip:/home/user/meowbalt/
scp C:\Users\user\Desktop\meowbalt-main\server.py user@ubuntu-ip:/home/user/meowbalt/
scp C:\Users\user\Desktop\meowbalt-main\requirements.txt user@ubuntu-ip:/home/user/meowbalt/
```

Или одной командой (исключая venv и db):

```powershell
scp -r C:\Users\user\Desktop\meowbalt-main\* user@ubuntu-ip:/home/user/meowbalt/
```

После обновления перезапусти сервис:

```bash
sudo systemctl restart meowbalt
```

---

### 5. Проверка что работает

```bash
# На Ubuntu проверь что сервер слушает порт
curl http://localhost:8000/api/health

# С Windows (через Tailscale IP):
# Открой в браузере: http://100.xx.xx.xx:8000
```

---

### Шпаргалка команд

|Действие|Команда|
|---|---|
|Запустить|`python server.py`|
|Остановить|`Ctrl+C`|
|Перезапустить сервис|`sudo systemctl restart meowbalt`|
|Посмотреть логи|`sudo journalctl -u meowbalt -f`|
|Статус|`sudo systemctl status meowbalt`|
|Удалить БД|`rm ~/meowbalt/meowbalt.db`|

Всё! После `scp` + `systemctl restart meowbalt` — сайт обновлён.
