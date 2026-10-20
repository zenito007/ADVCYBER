Крок 1: Запуск цільових машин у VirtualBox
<img width="2266" height="1450" alt="image" src="https://github.com/user-attachments/assets/2bae8141-462f-490b-ab96-1245a4670671" />

Крок 2: Знаходження IP-адреси цільової машини:

Для знаходження IP-адреси цільової машини використовую команду:

sudo netdiscover

<img width="1325" height="330" alt="image" src="https://github.com/user-attachments/assets/c969c773-5975-4449-abbf-0de11109802a" />

В результаті знаходжу IP цільової машини: 10.0.2.21

Крок 3: Сканування відкритих портів
Використовую інструмент nmap для сканування відкритих портів

<img width="1449" height="1158" alt="image" src="https://github.com/user-attachments/assets/f1baf7db-3775-4525-9878-522fb5976a95" />

В результаті бачу 3 відкритих порти:
21/tcp open  ftp 
22/tcp open  ssh
80/tcp open  http Apache

Крок 4: Перевірка на доступність системи по протоколу FTP через порт 21
Оскільки першим знайденим відкритим портом є 21, спробую проникнути на цільову машину використовуючи протокол FTP. 
Для цього я скористаюсь користувачем anonymous як ім'я користувача без вводу пароля. 
Запускаю команду:

ftp 10.0.2.21
<img width="630" height="410" alt="image" src="https://github.com/user-attachments/assets/4af84fa6-1e89-4239-bd11-18add937fc0c" />

Проникнувши на цільову машину, перевіряю вміст з допомогою команди ls


В результаті виявляю файл trytofind.jpg

<img width="880" height="462" alt="image" src="https://github.com/user-attachments/assets/62766b61-1632-4db0-a714-c3e123cf19e5" />

Крок 5: Дослідження виявленого файлу

Завантажую знайдений файл для подальшої перевірки. Для цього використовую команду get:

get trytofind.jpg
<img width="1608" height="497" alt="image" src="https://github.com/user-attachments/assets/d701a33f-9b8b-4344-b889-c7d2890354f8" />

І виходжу використовуючи команду quit:
<img width="785" height="174" alt="image" src="https://github.com/user-attachments/assets/3f2eea94-a181-433e-9060-d0b945babf0e" />

В результаті файл успішно завантажено і ми повернулись в калі.

Далі відкриваю завантажений файл у домашній папці калі. 
<img width="1304" height="1684" alt="image" src="https://github.com/user-attachments/assets/f1bd4f37-87d9-4baf-a927-41c908b73c3f" />

В результаті бачу зображення кішки.

Крок 6: Аналіз веб-додатку на порті 80

Щоб перевірити наступний знайдений порт 80, вводжу в браузері адресу IP цільової машини.
<img width="802" height="526" alt="image" src="https://github.com/user-attachments/assets/6c197251-50c4-4a7f-93d3-97233c2df5c2" />

В результаті відкривається домашня сторінка MoneyBox http-сервіса цільової машини.

Не знайшовши нічого на веб сторінці спробую перебір каталогів порту 80 використовуючи комінду dirb. 

dirb http://10.0.2.21
<img width="904" height="768" alt="image" src="https://github.com/user-attachments/assets/da608c60-59ab-4fc9-99bc-84a4c05944c5" />

В результаті знаходжу каталог blogs

Тож продовжу аналіз прихованого каталогу додавши каталого blogs до IP адреси: http://10.0.2.21/blogs
<img width="762" height="438" alt="image" src="https://github.com/user-attachments/assets/2e37cba9-f516-40f7-8609-21e800299195" />

Далі перевірю вихідний код сторінки клікнувши правою кнопкою мишки і обравши View page source або настиснувши одночасно Ctrl+U
<img width="1411" height="1461" alt="image" src="https://github.com/user-attachments/assets/035ad106-e65f-4925-a645-3ea7ee41a1db" />

В результаті у вихідному коді знаходжу підказку до таємного каталогу "3xtr4ctd4t4", який який можна розшифрувати "extractdata". В результаті проведених досліджени ми отримали:

Крок 7: Аналіз зображення, отримання прихованих даних

Спробую дослідити отримане зображення з допомогою стеганографії
Для цього встановлюю інструмент steghide з допомогою команди

sudo apt install steghide

В результаті steghide успішно встановлено.
<img width="1121" height="798" alt="image" src="https://github.com/user-attachments/assets/0bcff8d9-b8e0-4703-97a4-e0688b96e026" />

Далі спробую вилучити приховані дані з файла з допомогою встановленого інструменту steghide та знайденого ключа 3xtr4ctd4t4 в якості пароля.

Запускаю команду steghide extract -sf trytofind.jpg
<img width="551" height="128" alt="image" src="https://github.com/user-attachments/assets/0d22b787-3b5f-4334-b7cd-19cd1620af42" />

В результаті було виявлено файл data.txt

Переглядаю вміст каталогу з допомогою команди ls
<img width="1285" height="440" alt="image" src="https://github.com/user-attachments/assets/44c6071b-4907-4c1b-96e7-66d737942d5e" />

Крок 8: Перевірка виявленого файлу

Перевіряю вміст файлу data.txt з допомогою команди:

cat data.txt

<img width="1111" height="213" alt="image" src="https://github.com/user-attachments/assets/4aa5b10e-62f1-4538-bf7a-02839a849228" />

В результаті виявляю повідомлення про вразливий пароль на цільовій машині, який необхідно змінити. Також виявлено користувача renu.  

Крок 9: Встановлення файлу rockyou.
Переходжу в словник rockyou в каталог /usr/share/wordlists/ і відображаю список файлів. Для цього запускаю команду:
cd /usr/share/wordlists

<img width="1085" height="217" alt="image" src="https://github.com/user-attachments/assets/d3909271-80b1-45f8-a6b0-955655bd6266" />

Крок 10: Підбір пароля для користувача renu

Підбираю пароль використовуючи протокол ssh з допомогою інтсрументу hydra.

hydra -l renu -P /usr/share/wordlists/rockyou.txt -f 10.0.2.21 ssh .

<img width="1306" height="408" alt="image" src="https://github.com/user-attachments/assets/a10f7d7a-8017-4a75-a91a-64eec18a49f1" />

В результаті мені вдається отримати пароль 987654321 користувача renu.

Крок 11: Дослідження профіля користувача renu

На цьому кроці я можу використати пароль користувача renu щоб проникнути до цільової машини по протоколу ssh. 
Тож запускаю команду та вводжу пароль 987654321:

ssh renu@10.02.21

<img width="1023" height="542" alt="image" src="https://github.com/user-attachments/assets/901295ad-088c-482b-ba15-24dffe565760" />

В результаті, я увійшов до цільової машини під користувачем renu. 

Перше що мені потрібно це переглянути список файлів в каталогу. Для цього використаю команду ls:

<img width="428" height="109" alt="image" src="https://github.com/user-attachments/assets/832ca0c0-52b1-441b-b772-2f71d72b3b3e" />

В результаті мені вдалось виявити 2 файли.

Перегляну вміст файлу user1.txt. Запускаю команду:

cat user1.txt

<img width="498" height="154" alt="image" src="https://github.com/user-attachments/assets/a396237a-5488-41b0-a7a8-50b57d6af5b7" />

В результаті я виявляю прапорець.

Крок 12: Дослідження інших користувачів системи
Переходжу в домашній каталог щоб перевірити чи є ще інші користувачі з допомогою команд cd та ls.

cd ..
ls

<img width="366" height="125" alt="image" src="https://github.com/user-attachments/assets/8e3b7bb8-1dc7-41c2-974c-7d5fa37c40bd" />

В результаті виявляю іншого користувача lily.

Далі переходжу до каталогу lily та переглядаю список файлів включно з прихованими. Виконаємо команди:

cd lily
ls -la

<img width="718" height="365" alt="image" src="https://github.com/user-attachments/assets/bb5fda43-c8e5-4c96-bfbf-94844cca22cd" />

В результаті виявляю прихований файл user2.txt.

Переглядаю вміст файлу з допомогою команди cat.

cat user2.txt

<img width="639" height="208" alt="image" src="https://github.com/user-attachments/assets/9bc95c3e-c693-4003-8a71-199c99baeba9" />

В результаті виявляю ще один прапорець.

Крок 13: Підвищення привілеїв до root

Переходжу до папки .ssh щоб переглянути список файлів. Для цього виконую команди:

cd .ssh
ls

<img width="440" height="121" alt="image" src="https://github.com/user-attachments/assets/6ba07ddf-38a1-45ee-8a8b-9d036696cfec" />

В результаті виявляю файл authorized_keys.

Переглядаю вміст файлу з допомогою команди cat.
<img width="1332" height="213" alt="image" src="https://github.com/user-attachments/assets/02ebce42-e2af-41ac-b378-a386d3d02836" />

В результаті виявляю публічний ключ користувача renu. Тобто користувач renu може залогуватися під користувачем lily.

Спробую проникнути в систему під користувачем lily використовуючи протокол ssh. Запускаю команду:

ssh lily@10.0.2.21

<img width="1115" height="443" alt="image" src="https://github.com/user-attachments/assets/8fe5757d-f519-45f7-b1df-c4729ac26518" />

В результаті мені вдається увійшли в цільову систему використовуючи протокол ssh під користувачем lily не вводивши пароля. 

<img width="1144" height="338" alt="image" src="https://github.com/user-attachments/assets/e7c36464-faa4-46b8-963d-684a25bddf3f" />

Перевіряю привілеї користувача з допомогою команди sudo -l:

<img width="1122" height="303" alt="image" src="https://github.com/user-attachments/assets/ef85312b-f7e5-40b4-853b-871faccaa2cf" />

В результаті виявляю, що користувач lily має права sudo і може виконувати команди perl від імені root не вводивши пароля.

Запускаю команду perl, щоб запустити нову оболонку:

sudo perl -e 'exec "/bin/bash";' .

<img width="1175" height="223" alt="image" src="https://github.com/user-attachments/assets/5c0416ab-3fca-477b-a2d9-5744d183bafb" />

В результаті бачу нову оболонку bash з правами root. Я отримав доступ до всієї системи та можу виконувати будь-які дії. 
Оскільки ми ввели директиву NOPASSWD, то sudo не потребує введення пароля користувача lily.

Переглядаю список файлів в профілі користувача root. Запускаю команди:

cd ~
ls -la

<img width="802" height="332" alt="image" src="https://github.com/user-attachments/assets/1dd72b44-dcbc-4294-a14e-2eba33609363" />

В результаті виявляю приховану папку.local та файл .root.txt

Переглядаю вміст файлу .root.txt з допомогою команди

cat .root.txt

<img width="598" height="450" alt="image" src="https://github.com/user-attachments/assets/74a958a0-2d2b-43ac-9420-fac89882ceee" />

В результаті я виявляю фінальний файл з прапорцем.

Фінальний прапорець отримано! CTF завершено!
