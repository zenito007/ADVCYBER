1. Запуск віртуальних машин

   <img width="743" height="454" alt="image" src="https://github.com/user-attachments/assets/927627a1-2ef9-4759-9f59-2e088d6de017" />

   Результат: запустивши цільову машину, бачу IP адресу: 10.0.2.23

   Використовую наступні мережеві налаштування для NAT Мережі Test:
   <img width="536" height="256" alt="image" src="https://github.com/user-attachments/assets/9a5be6da-74be-4be1-9de3-f9259af58cd2" />

2. Розвідка цільової машини
Запускаю nmap та/або netdiscover інструменти для виявлення/підтвердження IP адреси цільової машини
<img width="810" height="427" alt="image" src="https://github.com/user-attachments/assets/65997849-65b0-4736-ba83-eee6bd76457e" />
<img width="887" height="407" alt="image" src="https://github.com/user-attachments/assets/984cd8ec-114d-498f-9508-7697ce7bacb2" />

В результаті виявляю/підтверджую IP цільової машини: 10.0.2.23

3.Сканування цільової машини з метою виявлення відкритих портів: nmap -sV -sC 10.0.2.23

<img width="1173" height="536" alt="image" src="https://github.com/user-attachments/assets/ea0832f4-825b-43f7-9ad9-0a3577f6a0d5" />

В результаті виявляю 2 відкритих порти: 

21/tcp open  ftp     vsftpd 3.0.3

80/tcp open  http    Apache httpd 2.4.18

4. Розвідка веб додатку на порті 80.
   
   Відкриваю браузер та вводжу IP адресу цільової машини 10.0.2.23, щоб дослідити веб сторінку.

   <img width="1194" height="530" alt="image" src="https://github.com/user-attachments/assets/05f1ca6c-6699-4771-98c7-aee80a8fb2f0" />

В результаті виявляю папку/каталог, який мені вдалось виявити без додаткових інструментів.

В меню сторінки бачу сторінку під назвою Buscar, яка може містити підказку або напрям подальшого пошуку.
Перейшовши на сторінку Buscar, бачу в URL гіперпосилання із змінною, яке варто перевірити на вразливість.
Спробую ввести значення стандартними лінуксовими командами в URL для змінної buscar: pwd, ls-la і т.д.

<img width="923" height="314" alt="image" src="https://github.com/user-attachments/assets/b00f95f7-366d-4d11-a454-42b4c2b0c827" />

В результаті бачу, що вивід каталогів та файлів виконується на клієнті. 

<img width="1808" height="525" alt="image" src="https://github.com/user-attachments/assets/bde95ef5-0012-4eea-a001-f90b2fae3045" />
Логічно необхідно перевірити вміст директорії

<img width="1813" height="364" alt="image" src="https://github.com/user-attachments/assets/346cf667-2a5e-49c2-bdfa-f3b3fad73d12" />

В результаті знаходжу креди користувача "jangow01" та пароль "abygurl69".

5. Проникнення через FTP протокол
Оскільки на первинному кроці виявлено відкритий 21 порт, спробую зайти на цільову машину через ftp протокол:

<img width="771" height="413" alt="image" src="https://github.com/user-attachments/assets/6170a9ac-8625-46fb-ae5f-4e5cce4d6ea3" />

В результаті мені вдаєтья проникнути на ціьову машину використовуючи знайдені креди/

Далі важливо провести розвідку домашньої директорії користувача jangow01. 

В результаті виявляю прапорець у файлі user.txt і завантажую його на локальну машину для подальшої перевірки:

<img width="2640" height="778" alt="image" src="https://github.com/user-attachments/assets/686a6767-e51f-4720-9156-e152b9c3130b" />

Оскільки у мене є необхідні креди (облікові дані користувача), я можу залогуватись напряму на цільову машину:

<img width="890" height="587" alt="image" src="https://github.com/user-attachments/assets/b6c5b010-7202-4b0d-9f14-910e48f2234e" />

Успішно зайшовши на цільову машину, спробую підвищити привілеї використовуючи Linpeas.

Для цього закачую файл linpeas.sh на цільову машину з каталогу /usr/share/peass/linpeas

<img width="1361" height="359" alt="image" src="https://github.com/user-attachments/assets/12cef185-340f-4421-b4bf-6e24b5f879cd" />

В результаті файл linpeas.sh завантажений на цільову машину.

Далі на цільовій машині надаю права на виконання файлу з допомогою команди chmod +x linpeas.sh ./linpeas.sh >> linpeas.log, а результати зберігаю у файлі журналу linpeas.log для подальшої перевірки:

Щоб підвищити мені знадобиться відома вразливість для кернела 4.4.0 цільової машини у Exploit Database і знаходжу її за посиланням: https://www.exploit-db.com/exploits/45010

<img width="3313" height="1131" alt="image" src="https://github.com/user-attachments/assets/6fe5d2a3-2c9f-4312-a762-aa0eca6b7290" />


Зберігаю експлойт, та копіюю його на цільову машину з допомогою FTP

<img width="2127" height="1355" alt="image" src="https://github.com/user-attachments/assets/ebd11d5f-7244-4b61-aab8-22fe2ffa3da3" />

Далі компілюю експлойт з допомогою команди gcc

gcc 45010.c

Далі виконую команду gcc 45010.с -o 45010

<img width="1062" height="707" alt="image" src="https://github.com/user-attachments/assets/09424f06-63b1-4e4b-a399-f792d2b0c614" />

Та запускаю файл у командному рядку цільової машини ./45010 

В результаті отримую привілеї root.


