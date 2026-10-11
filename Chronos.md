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
