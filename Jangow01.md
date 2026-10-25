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

4. 
