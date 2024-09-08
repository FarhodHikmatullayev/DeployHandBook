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
   python3 -m venv venv
   source venv/bin/activate
10. requirements.txt ichidagi kutubxonalarni o'rnatib olamiz
    pip install -r requirements.txt
11. Virtual muhitdan chiqamiz va postgresql ma'lumotlar ba'zasi yaratib olamiz
    deactivate
    sudo -u postgres psql # psql orqali postgresql ma'lumotlar ba'zasi muhitiga kirib olish
    CREATE DATABASE cloudproject; # ma'lumotlar ba'zasi yaratib olish
    CREATE USER clouduser WITH PASSWORD 'password'; # ba'za uchun user yaratib olamiz
    GRANT ALL PRIVILEGES ON DATABASE cloudproject TO clouduser;  # ma'lumotlar ba'zasidan foydalanish uchun userga ruhsat berish
    \q  # psql muhitidan chiqib olish
12. Project muhitiga kirib makemigrations va migrate qilamiz
    python3 manage.py makemigrations
    python3 manage.py migrate
    python3 manage.py createsuperuser # superuser yaratib olamiz
13. Loyihani tekshirib ko'rish uchun run qilib ko'ramiz
    sudo ufw allow 8000  # 8000 portini ochamiz
    python3 manage.py runserver 0.0.0.0:8000  # http://server_domain_or_IP:8000 ko'rinishida browserdan tekshirib ko'ring
14. Gunicorn yordamida tekshirib ko'rish
    source venv/bin/activate
    pip install gunicorn # agar bo'lsa shart emas
    gunicorn --bind 0.0.0.0:8000 config.wsgi:application
15. conf service faylini yaratib olish va uni sozlash
    # muhitdan chiqganingizni tekshirib ko'ring
    sudo ufw allow 'Nginx Full'  # HTTP va HTTPS trafikini ruxsat berish
    sudo nano /etc/systemd/system/yourproject.service # yourproject nomli cervice fayl yaratib olamiz va uning ichiga kirib olamiz
16. Service faylning ichiga quyidagilarni qo'shamiz
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

17.Ushbu service faylni ishga tushuramiz
  sudo systemctl daemon-reload
  sudo systemctl start your_project.service  # agar avval mavjud bo'lsa restart qilamiz
  sudo systemctl enable your_project.service
  sudo systemctl status your_project.service # statusni tekshirib olamiz

18. Nginx ni sozlab olamiz
    sudo nano etc/nginx/sites-available/your_project # your project nomli nginx fayl yaratib olamiz va uning ichiga kiramiz

      server {
          server_name farhod.mega-center.uz;
  
          location = /favicon.ico {
              access_log off;
              log_not_found off;
          }
  
          location /static/ {
              alias /var/www/MarketingTeamBot/staticfiles;
          }
  
          location / {
              include proxy_params;
              proxy_pass http://localhost:8022;
          }
  
      listen 443 ssl; # managed by Certbot
      ssl_certificate /etc/letsencrypt/live/farhod.mega-center.uz/fullchain.pem; # managed by Certbot
      ssl_certificate_key /etc/letsencrypt/live/farhod.mega-center.uz/privkey.pem; # managed by Certbot
      include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
      ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
  
  }
  
  server {
      if ($host = farhod.mega-center.uz) {
          return 301 https://$host$request_uri;
      } # managed by Certbot
  
  
          listen 80;
          server_name farhod.mega-center.uz;
      return 404; # managed by Certbot
  
  
  }

19. Keyin esa bu faylni /etc/nginx/sites-enabled/ ichiga simlink qilamiz
    sudo ln -s /etc/nginx/sites-available/your_project /etc/nginx/sites-enabled/
    sudo nginx -t  # tekshirish
    sudo systemctl restart nginx

20. Https larga dostup beramiz 8000 portini yopamiz
       sudo ufw delete allow 8000 \n
       sudo ufw allow 'Nginx Full'



     

  
        
