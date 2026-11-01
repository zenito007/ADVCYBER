1. Відстеження IP адреси цільової машини:
sudo netdiscover -r 10.0.2.0/24

<img width="1350" height="470" alt="image" src="https://github.com/user-attachments/assets/ddf54851-0f86-4841-8269-a74dc2a7a2fd" />

В результаті виявляю ip цільової адреси: 10.0.2.4

2. Відстеження відкритих портів цільової машини: sudo nmap -A --reason 10.0.2.4

<img width="1316" height="1045" alt="image" src="https://github.com/user-attachments/assets/974f9d2d-e0b7-4acc-a848-315440c6e624" />

В результаті виявляю 2 відкритих порти: 80 -веб- і 22 ssh-сервери.

3. 

