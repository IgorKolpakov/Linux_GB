Задание:

Создать два произвольных файла. Первому присвоить права на чтение и запись для владельца и группы, только на чтение — для всех. 
touch file1
touch file2
sudo chmod 664 file1
Второму присвоить права на чтение и запись только для владельца. Сделать это в численном и символьном виде.
sudo chmod 600 file2
sudo chmod u=rw file2

Назначить новых владельца и группу для директории целиком.
sudo chown root:root sdir/

Управление пользователями:
a. создать пользователя, используя утилиту useradd и adduser;
sudo useradd -s /bin/bash -m -d /home/testuser testuser
sudo passwd testuser

sudo adduser testtest

b. удалить пользователя, используя утилиту userdel.
sudo userdel -f -r testtest
sudo userdel -f -r testuser


Управление группами:
a. создать группу с использованием утилит groupadd и addgroup;
sudo groupadd newgroup 
sudo addgroup groupnew



b. попрактиковаться в смене групп у пользователей;
sudo chown oleg:groupnew sdir/

c. добавить пользователя в группу, не меняя основной;
sudo usermod -aG newgroup oleg


5.Создать пользователя с правами суперпользователя. Сделать так, чтобы sudo не требовал пароль для выполнения команд.
Зайти sudo visudo
oleg    ALL=(ALL:ALL) NOPASSWD: ALL

Результат:
Текст команд, которые применялись при выполнении задания. Присылаем в формате текстового документа: задание и команды для решения (без вывода). Формат - PDF (один файл на все задания).