# meowbalt — Look as you want, express emotions as you want

селфхост решения для тех - кто хочет занятся делом, а не проводить часы в поисках лучшего контента на ютубе

## Быстрый старт (Windows)

### 1. Установи Python
Скачай с [python.org](https://www.python.org/downloads/) — при установке **поставь галочку "Add Python to PATH"**

### 2. Открой терминал в папке проекта
```bash
cd c:\Users\bushy\Desktop\meowbalt-main
```

### 3. Создай виртуальное окружение
```bash
python -m venv venv
```

### 4. Активируй его
```bash
# Windows CMD
venv\Scripts\activate.bat

# Windows PowerShell
venv\Scripts\Activate.ps1
```

### 5. Установи зависимости
```bash
pip install -r requirements.txt
```

### 6. Запусти сервер
```bash
python server.py
```

Сервер запустится на `http://localhost:8000`

### 7. Открой сайт
Просто открой `index.html` в браузере — он автоматически подключится к API.

---

## API Endpoints

| Метод | Путь | Описание |
|-------|------|----------|
| GET | `/api/videos` | Получить все видео |
| POST | `/api/videos` | Добавить видео |
| DELETE | `/api/videos` | Очистить ленту |
| GET | `/api/health` | Проверка сервера |

---

## Деплой на Ubuntu сервер

### 1. Скопируй файлы на сервер
```bash
scp -r meowbalt-main/ user@your-server:/home/user/meowbalt
```

### 2. На сервере установи Python и зависимости
```bash
sudo apt update
sudo apt install python3 python3-pip python3-venv
cd /home/user/meowbalt
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### 3. Запусти через systemd (автозапуск)
```bash
sudo nano /etc/systemd/system/meowbalt.service
```

Содержимое:
```ini
[Unit]
Description=Meowbalt API Server
After=network.target

[Service]
Type=simple
User=your-username
WorkingDirectory=/home/user/meowbalt
ExecStart=/home/user/meowbalt/venv/bin/python server.py
Restart=always

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable meowbalt
sudo systemctl start meowbalt
sudo systemctl status meowbalt
```

### 4. Настрой Nginx (опционально, для домена)
```bash
sudo apt install nginx
sudo nano /etc/nginx/sites-available/meowbalt
```

```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        root /home/user/meowbalt;
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

```bash
sudo ln -s /etc/nginx/sites-available/meowbalt /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

---

## Структура проекта

```
meowbalt-main/
├── server.py          # FastAPI бэкенд
├── requirements.txt   # Python зависимости
├── meowbalt.db        # SQLite база (создаётся автоматически)
├── index.html         # Главная (добавление видео)
├── feed.html          # Лента видео
├── settings.html      # Настройки
├── updates.html       # Обновления
├── about.html         # О проекте
├── style.css          # Общие стили
├── feed.css           # Стили ленты
├── about.css          # Стили about/settings
├── settings.css       # Стили настроек
└── lang.js            # Система языков
```
