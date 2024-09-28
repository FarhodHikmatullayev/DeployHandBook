# DeployHandBook
This is a handbook for deploy python django and aiogram projects

1. Serverga kirish
   ssh root@185.217.131.71
2. Parol kiritish
3. Yangilash
   sudo apt update
   sudo apt upgrade -y
4. Python va kerakli kutubxonalarni o'rnatish
   sudo apt install python3 python3-pip python3-venv -y python3-dev libpq-dev postgresql postgresql-contrib nginx curl
   sudo apt install nginx -y # nginx o'rnatish agar bo'lsa shart emas
6. Loyihani clone qilish uchun directoryga o'tib olamiz
   cd /var/www/
7. Loyihani gitdan clone qilib olamiz
   git clone https://github.com/your_username/your_project.git
8. Loyiha ichiga kirib olamiz
   cd your_project
9. Vertual muhit o'rnatib uning ichiga kirib olamiz
   python3.7 -m venv venv
   source venv/bin/activate
10. requirements.txt ichidagi kutubxonalarni o'rnatib olamiz
    pip install multidict typing_extensions attr attrs yarl async_timeout idna_ssl aiosignal charset_normalizer
    pip install --upgrade pip setuptools
    pip install -r requirements.txt
12. vim editori orqali .env faylini yaratib uning ichiga environ qiymatalarni kiritib saqlaymiz
      DMINS=2023386058,5757849246,39325540,2128982044,404185899,1497650313,6433057712
      SUPERADMINS=2023386058,39325540
      BOT_TOKEN=7373806986:AAEYrS5ifkfLxn5d38rLtVYbeGW3uCh2z_c
      PORT=8080
      ip=localhost
      DB_USER=farhod
      DB_NAME=helperclientdb
      DB_PASS=farhod004
      DB_HOST=localhost
      DATABASE_URL=postgresql://helperclientdb_user:0WTr00mcDSxblFbhsho0cDgRB454AYvP@dpg-cqtepqrv2p9s73dejdm0-a.oregon-postgres.render.com/helperclientdb
      DJANGO_SECRET_KEY=ogMdTWbttsvzYR2F1Ku9K4qmu7yFBDFK
      DEBUG=True
      DEVELOPMENT_MODE=True

    Esc :wq saqlash va chiqish
    Esc :q! saqlamasdan chiqish
13. Virtual muhitdan chiqamiz va postgresql ma'lumotlar ba'zasi yaratib olamiz
    deactivate
    sudo -u postgres psql: # psql orqali postgresql ma'lumotlar ba'zasi muhitiga kirib olish

    CREATE DATABASE table_name WITH ENCODING 'UTF8' LC_COLLATE='en_US.UTF-8' LC_CTYPE='en_US.UTF-8' TEMPLATE template0;  # utf8 da ishlaydigan ma'lumotlar ba'zasi yaratish

    CREATE DATABASE cloudproject; # ma'lumotlar ba'zasi yaratib olish
    CREATE USER clouduser WITH PASSWORD 'password'; # ba'za uchun user yaratib olamiz
    GRANT ALL PRIVILEGES ON DATABASE cloudproject TO clouduser;  # ma'lumotlar ba'zasidan foydalanish uchun userga ruhsat berish
    \q  # psql muhitidan chiqib olish
15. Project muhitiga kirib makemigrations va migrate qilamiz
    python3 manage.py makemigrations
    python3 manage.py migrate
    python3 manage.py createsuperuser # superuser yaratib olamiz
16. Loyihani tekshirib ko'rish uchun run qilib ko'ramiz
    sudo ufw allow 8000  # 8000 portini ochamiz
    python3 manage.py runserver 0.0.0.0:8000  # http://server_domain_or_IP:8000 ko'rinishida browserdan tekshirib ko'ring
17. Gunicorn yordamida tekshirib ko'rish
    source venv/bin/activate
    pip install gunicorn # agar bo'lsa shart emas
    gunicorn --bind 0.0.0.0:8000 config.wsgi:application
18. conf service faylini yaratib olish va uni sozlash
    # muhitdan chiqganingizni tekshirib ko'ring
    sudo ufw allow 'Nginx Full'  # HTTP va HTTPS trafikini ruxsat berish
    sudo nano /etc/systemd/system/yourproject.service # yourproject nomli cervice fayl yaratib olamiz va uning ichiga kirib olamiz
19. Service faylning ichiga quyidagilarni qo'shamiz
    [Unit]
    Description=gunicorn daemon
    After=network.target
    
    [Service]
    User=root
    Group=www-data
    WorkingDirectory=/var/www/MarketingTeamBot
    ExecStart=/var/www/MarketingTeamBot/venv/bin/python manage.py runserver 0.0.0.0:8022
    
    [Install]
    WantedBy=multi-user.target

    ctrl+O ENTER # saqlash
    ctrl+X chiqish

    Bot uchun ham xuddi shunday conf fayl yoziladi

    [Unit]
    Description=Aiogram Telegram Bot
    After=network.target
   
    [Service]
    User=root
    Group=www-data
    WorkingDirectory=/var/www/Tebe_bot
    ExecStart=/var/www/Tebe_bot/venv/bin/python app.py
   
    [Install]
    WantedBy=multi-user.target

    

17.Ushbu service faylni ishga tushuramiz

  sudo systemctl stop helperclientbot.service
  sudo systemctl disable helperclientbot.service
   
  sudo systemctl daemon-reload
  sudo systemctl start your_project.service  # agar avval mavjud bo'lsa restart qilamiz
  sudo systemctl enable your_project.service
  sudo systemctl status your_project.service # statusni tekshirib olamiz
    

18. Nginx ni sozlab olamiz
    sudo nano /etc/nginx/sites-available/your_project # your project nomli nginx fayl yaratib olamiz va uning ichiga kiramiz

      server {
         listen 80;
         server_name education.mega-center.uz;

         location = /favicon.ico { access_log off; log_not_found off; }
         location /static/ {
             alias /var/www/EducationCenterbot/staticfiles/;  # Update this with your actual path
         }

         location /media/ {
              alias /var/www/EducationCenterbot/media/;  # Update this with your actual path
         }

         location / {
            include proxy_params;
            proxy_pass http://localhost:8027;
         }
      }
18. SSL sertifikat olish
   https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-20-04

   sudo nginx -t
   sudo systemctl reload nginx
   sudo ufw status
   sudo certbot --nginx -d example.com -d www.example.com
   sudo systemctl status certbot.timer
   sudo certbot renew --dry-run

19. Keyin esa bu faylni /etc/nginx/sites-enabled/ ichiga simlink qilamiz
    sudo ln -s /etc/nginx/sites-available/your_project /etc/nginx/sites-enabled/
    sudo nginx -t  # tekshirish
    sudo systemctl restart nginx

20. Https larga dostup beramiz 8000 portini yopamiz
       sudo ufw status
       sudo ufw allow 8023/tcp
       sudo ufw delete allow 8000 
       sudo ufw allow 'Nginx Full'



     

  
        
