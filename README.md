# DeployHandBook
This is a handbook for deploy python django and aiogram projects

1. Serverga kirish

       ssh root@111.111.111.11
3. Parol kiritish
4. Yangilash

       sudo apt update
       sudo apt upgrade -y
5. Python va kerakli kutubxonalarni o'rnatish

       sudo apt install python3 python3-pip python3-venv -y python3-dev libpq-dev postgresql postgresql-contrib nginx curl
   nginx o'rnatish agar bo'lsa shart emas
   
       sudo apt install nginx -y 
7. Loyihani clone qilish uchun directoryga o'tib olamiz

       cd /var/www/
9. Loyihani gitdan clone qilib olamiz

       git clone https://github.com/your_username/your_project.git
11. Loyiha ichiga kirib olamiz

        cd your_project
13. Vertual muhit o'rnatib uning ichiga kirib olamiz

        python3.7 -m venv venv
        source venv/bin/activate
15. requirements.txt ichidagi kutubxonalarni o'rnatib olamiz

        pip install multidict typing_extensions attr attrs yarl async_timeout idna_ssl aiosignal charset_normalizer
    vertual muhit ichida kerakli kutubxonalarni install qilish
    
        pip install --upgrade pip setuptools
        pip install -r requirements.txt
17. vim editori orqali .env faylini yaratib uning ichiga environ qiymatalarni kiritib saqlaymiz

        ADMINS=2222222222,1111111111
        SUPERADMINS=2222222222,1111111111
        BOT_TOKEN=7373806986:AAasdfafasfasdfafasfsdfaswerE
        PORT=8080
        ip=localhost
        DB_USER=user
        DB_NAME=name
        DB_PASS=password
        DB_HOST=localhost
        DATABASE_URL=postgresql://helperclientdb_user:asdjfSDFADFAdfajsdfskadlfsl54AYvP@dpg-cqtepqrv2p9s73dejdm0-a.oregon-postgres.render.com/db_name
        DJANGO_SECRET_KEY=ogMdTWbttsvzYR2Faasdfasdfew4qmu7yFBDFK
        DEBUG=True
        DEVELOPMENT_MODE=True
    saqlash va chiqish

        Esc :wq
    saqlamasdan chiqish

        Esc :q! 
19. Virtual muhitdan chiqamiz va postgresql ma'lumotlar ba'zasi yaratib olamiz

        deactivate
    psql orqali postgresql ma'lumotlar ba'zasi muhitiga kirib olish

        sudo -u postgres psql
    utf8 da ishlaydigan ma'lumotlar ba'zasi yaratish

        CREATE DATABASE table_name WITH ENCODING 'UTF8' LC_COLLATE='en_US.UTF-8' LC_CTYPE='en_US.UTF-8' TEMPLATE template0;
     ma'lumotlar ba'zasi yaratib olish
         
        CREATE DATABASE cloudproject;
    ba'za uchun user yaratib olamiz
    
        CREATE USER clouduser WITH PASSWORD 'password';
    ma'lumotlar ba'zasidan foydalanish uchun userga ruhsat berish
    
        GRANT ALL PRIVILEGES ON DATABASE cloudproject TO clouduser;
    psql muhitidan chiqib olish
    
        \q  
21. Project muhitiga kirib makemigrations va migrate qilamiz

        python3 manage.py makemigrations
        python3 manage.py migrate
    superuser yaratib olamiz
    
        python3 manage.py createsuperuser 
23. Loyihani tekshirib ko'rish uchun run qilib ko'ramiz
    8000 portini ochamiz
    
        sudo ufw allow 8000
    http://server_domain_or_IP:8000 ko'rinishida browserdan tekshirib ko'ring
    
        python3 manage.py runserver 0.0.0.0:8000  
25. Gunicorn yordamida tekshirib ko'rish

        source venv/bin/activate
    gunicorn o'rnatish, agar bo'lsa shart emas
    
        pip install gunicorn
    gunicorn yordamida loyihani ishga tushirish
    
        gunicorn --bind 0.0.0.0:8000 config.wsgi:application
27. conf service faylini yaratib olish va uni sozlash
    HTTP va HTTPS trafikini ruxsat berish
           
        sudo ufw allow 'Nginx Full'
    yourproject nomli cervice fayl yaratib olamiz va uning ichiga kirib olamiz
    
        sudo nano /etc/systemd/system/yourproject.service 
29. Service faylning ichiga quyidagilarni qo'shamiz
    django loyihasi uchun conf file

        [Unit]
        Description=gunicorn daemon
        After=network.target
    
        [Service]
        User=root
        Group=www-data
        WorkingDirectory=/var/www/Project_name
        ExecStart=/var/www/Project_name/venv/bin/python manage.py runserver 0.0.0.0:8022
    
        [Install]
        WantedBy=multi-user.target
    saqlash
    
        ctrl+O ENTER
    chiqish

        ctrl+X 

    Bot uchun ham xuddi shunday conf fayl yoziladi

        [Unit]
        Description=Aiogram Telegram Bot
        After=network.target
   
        [Service]
        User=root
        Group=www-data
        WorkingDirectory=/var/www/Project_name
        ExecStart=/var/www/Project_name/venv/bin/python app.py
   
        [Install]
        WantedBy=multi-user.target

    

17.Ushbu service faylni ishga tushuramiz

    sudo systemctl stop your_project.service
    sudo systemctl disable your_project.service
   
    sudo systemctl daemon-reload
    sudo systemctl start your_project.service  # agar avval mavjud bo'lsa restart qilamiz
    sudo systemctl enable your_project.service
    sudo systemctl status your_project.service # statusni tekshirib olamiz
    

18. Nginx ni sozlab olamiz
    your project nomli nginx fayl yaratib olamiz va uning ichiga kirami

        sudo nano /etc/nginx/sites-available/your_project
    conf file

        server {
            listen 80;
            server_name aaa.bbb.uz;

            location = /favicon.ico { access_log off; log_not_found off; }
            location /static/ {
                alias /var/www/Project_name/staticfiles/;  # Update this with your actual path
            }

            location /media/ {
                 alias /var/www/Project_name/media/;  # Update this with your actual path
            }

            location / {
               include proxy_params;
               proxy_pass http://localhost:8027;
            }
         }
    nginx conf faylni sites-enabledga ko'chirish

        sudo ln -s /etc/nginx/sites-available/cloudproject /etc/nginx/sites-enabled
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
21. Https larga dostup beramiz 8000 portini yopamiz

        sudo ufw status
        sudo ufw allow 8023/tcp
        sudo ufw delete allow 8000 
        sudo ufw allow 'Nginx Full'



     

  
        
