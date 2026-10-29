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



