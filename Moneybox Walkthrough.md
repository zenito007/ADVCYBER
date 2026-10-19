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
