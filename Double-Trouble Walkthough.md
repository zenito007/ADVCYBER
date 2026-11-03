1. Відстеження IP адреси цільової машини:
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

Далі налаштовую правила на Kali, щоб зробити port forwarding для мережі CTF_Network.

<img width="1841" height="1657" alt="image" src="https://github.com/user-attachments/assets/4b1f2dd6-ed74-4610-855c-23c15b28b4ab" />

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

Підключаю BurpSuit, Запускаємо його і залогінююсь в адмінку:

