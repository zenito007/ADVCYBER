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

Налаштовуємо браузер для роботи через проксі-сервер Burp Suite.

Перехоплюємо запит до ресурсу:

text
http://10.0.2.19:8000/date?format=...
Змінюю значення заголовка User-Agent на:

Chronos
Надсилаю відредагований запит на сервер.

Результат: сервер повертає відповідь із поточною датою та часом.
<img width="926" height="452" alt="image" src="https://github.com/user-attachments/assets/04c8e3cb-228b-45e3-9e7b-087a4e03f2ad" />
           
