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

В результаті бачу зображення кішки, яка не містить підказок.

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

