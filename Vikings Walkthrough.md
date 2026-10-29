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





