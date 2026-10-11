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

