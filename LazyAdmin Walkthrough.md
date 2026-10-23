1. Запуск Kali Linux та цільової машини у Virtual Box
<img width="1454" height="1399" alt="image" src="https://github.com/user-attachments/assets/edf46428-dd33-45b8-ae81-aac8023474e9" />

В результаті машини запущені.

2. Пошук цільової машини з допомогою команди netdiscover:

<img width="972" height="405" alt="image" src="https://github.com/user-attachments/assets/ae788703-ee65-4e3b-9582-1b979446a256" />

В результаті знаходимо IP цільової машини: 10.0.2.15

2. Сканування сервісів, що працюють на цільовій машині та відкритих портів

Виконуємо команду nmap -sC -sV -p-  10.0.2.15

<img width="1331" height="1528" alt="image" src="https://github.com/user-attachments/assets/7e181591-9eb1-4d4a-b9ec-bb003c6364ec" />

В результаті знаходжу відкриті порти на наступних сервісах:

PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http        Apache httpd 2.4.7 ((Ubuntu))
3306/tcp open  mysql       MySQL (unauthorized)
6667/tcp open  irc         InspIRCd
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)

3. Пошук прихованих каталогів/директорій/файлів з допомогою команди -gobuster, -dirb та -nikto.

<img width="780" height="605" alt="image" src="https://github.com/user-attachments/assets/4f647aca-b185-4227-a2c6-5f80c2d76b40" />

<img width="1343" height="1470" alt="image" src="https://github.com/user-attachments/assets/7680fb84-fb7b-4d0d-8412-5bdf79d1117a" />


У результаті перевірки виявлено, що:

WordPress, встановлений за шляхом /wordpress.

Сторінку з виводом функції phpinfo(), доступну за шляхом /info.php.

Встановлений вебінтерфейс phpMyAdmin для керування базами даних.

4. Аналіз вордпрес

   Спробую перевірити 

   <img width="1337" height="1143" alt="image" src="https://github.com/user-attachments/assets/7af47930-86ea-4b98-8707-ef7c568b09eb" />

Дослідження сервісу SMB (Самба)

enum4linux -a 10.0.2.15

<img width="1223" height="566" alt="image" src="https://github.com/user-attachments/assets/c2642a03-325c-4014-ad6c-c0684da27eba" />

В результаті знаходжу обліковий запис локального користувача: S-1-22-1-1000 Unix User\togie (Local User)

5. Дослідження файлів на SMB

   Спробую зайти на SMB з командного рядка: smbclient //10.0.2.15/share$ -U Togie
<img width="992" height="1180" alt="image" src="https://github.com/user-attachments/assets/ba957ecb-605b-4af7-94fe-95123705d484" />

   В результаті знаходжу файл wp-config.php

Відкривши файл я виявляю пароль до БД MySQL:

   <img width="1618" height="1022" alt="image" src="https://github.com/user-attachments/assets/28656343-f560-4e47-8008-ebf88212b743" />

Також знаходжу пароль у файлі deets.txt

<img width="1019" height="396" alt="image" src="https://github.com/user-attachments/assets/8e4f78a3-62e1-4464-a0a8-3e4748f87746" />

<img width="1140" height="225" alt="image" src="https://github.com/user-attachments/assets/794e5dd6-c40f-4aa8-abf8-438e98690918" />

 
6. Дослідження ssh

Спробую залогінитись через ssh протокол

ssh togie@10.0.2.15

<img width="1067" height="692" alt="image" src="https://github.com/user-attachments/assets/7425afbf-a1f7-43c8-bf81-1ddec11b1fa4" />

В результаті мені вдалось проникнути.

Тож перевіряю дозволи:

sudo -l
<img width="1124" height="211" alt="image" src="https://github.com/user-attachments/assets/1c0b9d1e-bb22-47dd-a4d9-b381444b72bb" />

В результаті виявляю, що у користувача Togie є всі дозволи.

Тому логічно спробую перейти на root.

sudo -i

<img width="352" height="73" alt="image" src="https://github.com/user-attachments/assets/0de3594f-fe4c-486b-82e1-ddb6704b543c" />

Переходжу в каталог root cd /root

<img width="668" height="308" alt="image" src="https://github.com/user-attachments/assets/cc9686ba-5844-4003-bbe9-7fb9f01a9ce3" />

В результаті знаходжу файл proof.txt, зайшовши в який виявляю рандомні рядки

<img width="846" height="749" alt="image" src="https://github.com/user-attachments/assets/ed9c8935-7af8-4fa2-9160-960067b480fd" />

Фінальний прапорець знайдено.
