1. Відстеження IP адреси цільової машини:
sudo netdiscover -r 10.0.2.0/24

<img width="1350" height="470" alt="image" src="https://github.com/user-attachments/assets/ddf54851-0f86-4841-8269-a74dc2a7a2fd" />

В результаті виявляю ip цільової адреси: 10.0.2.4

2. Відстеження відкритих портів цільової машини: sudo nmap -A --reason 10.0.2.4

<img width="1316" height="1045" alt="image" src="https://github.com/user-attachments/assets/974f9d2d-e0b7-4acc-a848-315440c6e624" />

В результаті виявляю 2 відкритих порти: 80 -веб- і 22 ssh-сервери.

3. Розвідка вебсервера.

   Вводжу знайдену адресу пошуковому вікні веббраузера http://10.0.2.4

   <img width="1177" height="948" alt="image" src="https://github.com/user-attachments/assets/4374fd19-8a58-41b8-b5b9-ca1a8639f20e" />

   В результаті виявляю  безкоштовний вебзастосунок qdPM з відкритим кодом для управління проєктами.

   Відстежую приховані директорії з допомогою інтсрументу Gobuster:

   gobuster dir -u http://10.0.2.4/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

<img width="1335" height="986" alt="image" src="https://github.com/user-attachments/assets/f049792f-cb9d-40ac-a8bf-ffa4838f3e1e" />

В результаті виявляю приховані директорії, які варто перевірити на підказку.
