1. Сканування на виявлення IP цільової машини: 

   <img width="1034" height="397" alt="image" src="https://github.com/user-attachments/assets/c96ca55c-f32d-4277-a01d-1a1deb1c45cb" />

В результаті знаходжу IP цільової машини: 10.0.2.24

2. Сканування цільової машини за допомогою інструменту nmap:

   nmap -sC -sV -p- -oN tcp_scan 10.0.2.24

<img width="1295" height="730" alt="image" src="https://github.com/user-attachments/assets/db2cb44a-e599-46d5-b9cf-0fffb6b88ea2" />

3. Розвідка веб сервера
Вводжу адресу знайденої ціьової машини у пошуковий рядок браузера:

 <img width="1410" height="1095" alt="image" src="https://github.com/user-attachments/assets/ec640dfd-443e-4fb8-a29b-a8251a7c031d" />

В результаті знаходжу ще один каталог site, відкривши який виявляю сторінку із зображенням Ivar the Boneless та опис саги.

Розвідка прихованих директорій та файлів за допомогою інструменту Gobuster:

gobuster dir -r -u http://10.0.2.24/site/ -w /usr/share/dirb/wordlists/common.txt -x txt,php,html -o dir-common.log

<img width="1317" height="1021" alt="image" src="https://github.com/user-attachments/assets/8bd0c735-1014-405f-a273-54b88db0a659" />

В результаті виявляю прихований файл war.txt, який спробую підставити в адресний рядок адреси у браузері.

<img width="830" height="281" alt="image" src="https://github.com/user-attachments/assets/5d7c5c05-5541-4e9d-b673-e9827e6d690f" />

Ще одна підказка war-is-over знайдена, яку теж можна підставити в адресну стрічку браузера.

<img width="1353" height="785" alt="image" src="https://github.com/user-attachments/assets/557160f4-f310-4830-808e-99b45deecb5b" />

В результаті знаходжу зашифрований файл, який дуже нагадує Base64.

<img width="1065" height="236" alt="image" src="https://github.com/user-attachments/assets/733ea3ea-f219-48c7-8956-ac3a480512db" />

<img width="1324" height="262" alt="image" src="https://github.com/user-attachments/assets/00dacb45-f0d3-475a-8864-a3df2ea6bb7f" />

В результаті виявляю файл king.

<img width="1046" height="744" alt="image" src="https://github.com/user-attachments/assets/ea4a9912-3901-4853-a55f-e888eb9cd490" />

Так виглядає, що файл захищений паролем.

<img width="834" height="887" alt="image" src="https://github.com/user-attachments/assets/74f327c6-9ceb-4698-9e89-6b158c59b868" />

Спробую видобути пароль до файлу з допомогою інструментів zip2john і John the Ripper:

zip2john output > hash john hash --wordlist=/usr/share/wordlists/rockyou.txt

<img width="1280" height="402" alt="image" src="https://github.com/user-attachments/assets/bf09a4a7-082a-4698-bbd5-b9b92186b1f9" />

В результаті видобуваю пароль ragnarok123 і знаходжу зображення вікінгів:

<img width="1620" height="1130" alt="image" src="https://github.com/user-attachments/assets/cb8a3f1e-4cd8-40ac-9f9f-52ce88828e2e" />

Логічно провести далі стеганографію з зображенням, щоб отримати приховані дані.

Для цього використаю інструмент binwalk:

<img width="1331" height="305" alt="image" src="https://github.com/user-attachments/assets/937ff816-f6d8-4920-adaf-43c58189060d" />

В результаті виявляю користувача user.

Оскільки в результаті розархівування ми виявили директорію _king.extracted, переходжу до неї, щоб продовжити дослідження:
<img width="765" height="1035" alt="image" src="https://github.com/user-attachments/assets/5c4d1e41-316a-4e9f-b2f8-afec549a8122" />

<img width="634" height="280" alt="image" src="https://github.com/user-attachments/assets/8d6f8c72-5aef-4159-8f29-ee1643c4d9b1" />

Перегляну файл user

<img width="485" height="139" alt="image" src="https://github.com/user-attachments/assets/57fa11d2-41af-463e-a3aa-f444bd8cb60d" />

В результаті знаходжу облікові дані: 
користувач: floki
пароль:     f@m0usboatbuilde7

Далі по логіці можу спробувати проникнути на цільову машину по ssh протоколу через 22 порт:

<img width="1230" height="742" alt="image" src="https://github.com/user-attachments/assets/838ca110-79f8-41eb-99f2-d16d707587e0" />

В результаті користувач і пароль підходять, тому я всередині.

Переглядаю домашній каталог з допомогою команд ls -la:

<img width="850" height="540" alt="image" src="https://github.com/user-attachments/assets/ddfb1553-839d-458b-a10a-b9052f5eeccd" />

В результаті виявляю файл boat.

Переглядаю файл, щоб знайти підказку:

<img width="505" height="215" alt="image" src="https://github.com/user-attachments/assets/535e70e5-8fa3-4f5f-95bf-333551df052b" />

В результаті виявляю підказку на гіпотезу Коллатца і 29 просте число, яке дорівнює 109.

Виводжу всі числа з числа 109 за допомогою скрипта пайтон. Записую скрипт у редактрі пайтон і зберігаю.

<img width="779" height="425" alt="image" src="https://github.com/user-attachments/assets/7ab79222-82bd-4014-a944-ac139f22757a" />

<img width="1326" height="314" alt="image" src="https://github.com/user-attachments/assets/49daadc1-68ea-4c54-b398-54b3a991dd81" />

Далі перетворюю набір чисел у ASCII символи, для отримання паролю з допомогою скрипта пайтон:

<img width="1286" height="667" alt="image" src="https://github.com/user-attachments/assets/e4c89cd5-75b9-4ec3-a62f-7b6d330ae386" />

<img width="741" height="361" alt="image" src="https://github.com/user-attachments/assets/559891d0-0c99-4706-9b27-a482bb7b882b" />

В результаті отримую пароль: mR)|>^/Gky[gz=\.F#j5P(
Проте ще не відомо якому користувачу радежить цей пароль.

Логічно, спробую перевірити користувача ragnar перейшовши на нього з допомогою команди su ragnar:

<img width="829" height="174" alt="image" src="https://github.com/user-attachments/assets/8cc61f6e-290b-423c-a0e2-cb6f57b71b57" />

При перевірці каталогу ragnar в директорії home, знаходжу рядок символів у файлі user.txt і пайтонівський файл  rpyc_classic.py.

<img width="1195" height="1030" alt="image" src="https://github.com/user-attachments/assets/8c30f963-e1f1-40b8-a0e7-ef119c256835" />

Оскільки rpyc_classic є найбільш небезпечною бібліотекою RPC, це дозволить мені запускати будь які команди від root.

При підготовці я переношу публічний ключ Kali на цільову машину і змінюю його ім'я у authorized_keys.

Спочатку генерую ключі в Kali з допомогою команди ssh-keygen:

<img width="1050" height="600" alt="image" src="https://github.com/user-attachments/assets/9f85e988-c0df-4870-bf46-6a10ed406168" />

Далі запускаю apache сервер на kali.

<img width="1320" height="900" alt="image" src="https://github.com/user-attachments/assets/ba00381c-4b2f-4252-9836-a33022b6a746" />

Переношу публічний ключ на вебсервер: sudo cp .ssh/id_ed25519.pub /var/www/html 

<img width="718" height="140" alt="image" src="https://github.com/user-attachments/assets/82d0ec5d-1cb0-45dd-bf33-beb46f1a266b" />

Оскільки публічний ключ вже є на цільовій машині, переношу його в домашню директорію користувача ragnar:

<img width="1286" height="694" alt="image" src="https://github.com/user-attachments/assets/4b993f7a-be0a-449f-bf87-dc4d08c94a39" />

Далі пишу скрипт для rpyc на пайтоні в директорії /tmp

<img width="1041" height="1045" alt="image" src="https://github.com/user-attachments/assets/144dde2f-ed0b-4cf3-b073-48fdf10053b5" />

Далі спробую під'єднатись до цільової машини користувача root через ssh з kali:

ssh -i ./ssh_vikings root@10.0.2.24

<img width="1321" height="1122" alt="image" src="https://github.com/user-attachments/assets/2a8eb7a8-05e8-44e2-b214-584a36c533e2" />


