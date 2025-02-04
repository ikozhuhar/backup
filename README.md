<p align="center">
<img src="https://github.com/ikozhuhar/backup/blob/main/img/backup.png">
</p>

<br/>

### :diamond_shape_with_a_dot_inside: <a name='toc'>Содержание</a>

1. [Тестовый стенд](#1)
2. [Установка и настройка borgbackup](#2)
3. [Установка borgbackup на сервер ](#3)
4. [Установка borgbackup на клиенте](#4)
5. [Создание репозитория](#5)
6. [Создание резервной копии](#6)
7. [Восстановление из резервной копии](#7)
8. [Автоматизация с помощью systemd](#8)
9. [Ручное резервное копирование](#9)
10. [Дополнительные источники](#recommended_sources)


<br/>

### [:diamond_shape_with_a_dot_inside:](#toc) <a name='1'>Тестовый стенд</a>

![image](https://github.com/user-attachments/assets/ecd995b2-da3f-4f61-b28d-fc495362a613)


<br/>

### [:diamond_shape_with_a_dot_inside:](#toc) <a name='2'>Установка borgbackup</a>

Установка как на сервере, так и на клиенте выполняется одинаково:

```ruby
sudo -i
apt update
apt install borgbackup
```

<br/>

### [:diamond_shape_with_a_dot_inside:](#toc) <a name='3'>Установка borgbackup на сервер</a>

```ruby
sudo -i
apt update
apt install borgbackup
```

![image](https://github.com/user-attachments/assets/8e4b67df-7ecf-43d9-a74b-7194cb55dcb6)

Все действия системы ПО borgbackup выполняются от имени пользователя borg (не путать с операциями конфигурирования и управления самого ПО - они выполняются от имени root).

Репозитории хранения данных borg располагает в своей домашней директории.

Исходя из этого требуется создать данного системного пользователя с соответствующей домашней директорией и подготовить структуру домашней директории для организации доступа клиентов по ключам:

```ruby
# Создаем домашний каталог и директрорию для ключей
useradd -m borg
mkdir ~borg/.ssh
touch ~borg/.ssh/authorized_keys
chown -R borg:borg ~borg/.ssh

# Созадаем каталог /var/backup
mkdir /var/backup
chown borg:borg /var/backup/
```

Конфигурирование сервера на этом можно считать завершенным, за исключением того, что в дальнейшем потребуется добавить публичные клиентские ключи для доступа к borg серверу.


<br/>

### [:diamond_shape_with_a_dot_inside:](#toc) <a name='4'>Установка borgbackup на клиенте</a>

```
sudo -i
apt update
apt install borgbackup
```

![image](https://github.com/user-attachments/assets/1ec2760a-1729-4db1-a221-f01155998567)

```ruby
# Генерируем SSH-ключ
ssh-keygen
```
![image](https://github.com/user-attachments/assets/92e1dbed-3304-4e87-800c-be8055eae65d)


После генерации ключа его нужно будет скопировать и разместить на сервере в файл /home/borg/.ssh/authorized_keys

<br/>

### [:diamond_shape_with_a_dot_inside:](#toc) <a name='5'>Создание репозитория</a>

Инициализируем репозиторий borg на backup сервере с client сервера:

```ruby
# Для создания простого (нешифрованного) репозитория ввести команду
borg init -e none borg@192.168.56.160:/var/backup/

# Для создания зашифрованного репозитория ввести команду
borg init --encryption=repokey borg@192.168.56.160:/var/backup/
```

Если создание репозитория будет отклонено, то необходимо проверить:

- наличие соединения с сервером по SSH (на клиенте выполнить команду `ssh user@192.168.56.160` );
- права доступа для borg к своему домашнему каталогу (на сервере выполнить команду `chown -R borg:borg /home/borg` );
- соответствие имени репозитория, указанного в команде с открытым ключом на сервере, фактическому имени создаваемого репозитория.

После успешного выполнения команды, проверить на сервере наличие созданного репозитория.

<br/>

### [:diamond_shape_with_a_dot_inside:](#toc) <a name='6'>Создание резервной копии</a>

```ruby
# Созадание копии всей директории
borg create --stats --list borg@192.168.56.160:/var/backup/::"etc-{now:%Y-%m-%d_%H:%M:%S}" /etc

# Создание копии отдельных файлов
borg create --stats --list borg@192.168.56.160:/var/backup/::"etc-{now:%Y-%m-%d_%H:%M:%S}" /etc/shadow /etc/passwd /etc/group
```

![image](https://github.com/user-attachments/assets/797d273a-0910-4b2f-8976-5c93fe0e8afd)
![image](https://github.com/user-attachments/assets/64fc6cd2-0815-45b0-910f-248c91a8aee5)

_Смотрим, что у нас получилось_

```ruby
borg list borg@192.168.56.160:/var/backup/
```

![image](https://github.com/user-attachments/assets/5f309f55-7156-472b-8605-6bd984daf266)

_Смотрим список файлов_

```ruby
borg list borg@192.168.56.160:/var/backup/::etc-2025-01-11_11:12:46
```
![image](https://github.com/user-attachments/assets/805c30a7-4bbc-46ad-9353-2e7b91e23d4c)
![image](https://github.com/user-attachments/assets/3ef67d64-35ae-43a2-84d0-9d4d02283aac)

<br/>

### [:diamond_shape_with_a_dot_inside:](#toc) <a name='7'>Восстановление данных из резервной копии</a>

Перед восстановлением, рекомендуется создать отдельную директорию и перейти в нее:

```ruby
mkdir /mnt/borgrestore
cd /mnt/borgrestore
```

_Для восстановления всех файлов из доступного архива ввести команду:_

```ruby
borg extract borg@192.168.56.160:/var/backup/::etc-2025-01-11_11:12:46
```

_Для восстановления отдельных файлов или директорий из архива ввести команду:_

```ruby
borg extract borg@192.168.56.160:/var/backup/::etc-2025-01-11_11:12:46 etc/passwd etc/shadow etc/group
```

_Для примера попробуем сделать копию файлов passwd, shadow, group и восстановить их._

```ruby
borg create --stats --list borg@192.168.56.160:/var/backup/::"etc-{now:%Y-%m-%d_%H:%M:%S}" /etc/shadow /etc/passwd /etc/group
```

![image](https://github.com/user-attachments/assets/af3105ab-8e1a-4041-b084-8a0f9c45b25a)

_Смотрим результат на сервере_

```ruby
borg list borg@192.168.56.160:/var/backup/
```
![image](https://github.com/user-attachments/assets/c2a6d0fd-acbe-4b93-994a-ef0f34d997ce)

_Востанавливаем файлы_

```ruby
borg extract borg@192.168.56.160:/var/backup/::etc-2025-01-11_21:11:59 etc/passwd etc/shadow etc/group
```
![image](https://github.com/user-attachments/assets/ef82af85-a081-4d64-8bf5-2470d490bbe8)

```ruby
batcat ./etc/passwd
```
![image](https://github.com/user-attachments/assets/8a51af50-4125-48ae-bed1-6d54c79609d5)



_Монтирование резервной копии в локальную папку (альтернативный вариант восстановления данных)_

_Borg позволяет смонтировать репозиторий в локальную файловую систему и работать с архивом как с обычной папкой. Для этого требуется создать точку (директорию) монтирования и подмонтировать в нее требуемую резервную копию с сервера:_

```ruby
mkdir /mnt/borgmount
borg extract borg@192.168.56.160:/var/backup/::etc-2025-01-11_11:12:46 /mnt/borgmount
```

<br/>

### [:diamond_shape_with_a_dot_inside:](#toc) <a name='8'>Автоматизация создания бэкапов с помощью systemd</a>

```ruby
 vi /etc/systemd/system/borg-backup.service
```
![image](https://github.com/user-attachments/assets/fb1083e7-b897-4e0f-af40-28aa1abd3c25)

```ruby
vi /etc/systemd/system/borg-backup.timer
```
![image](https://github.com/user-attachments/assets/cf394fd3-a269-4490-ba5e-086b1cea3d20)


_Включаем и запускаем службу таймера_

```ruby
systemctl enable borg-backup.timer 
systemctl start borg-backup.timer
```

_Проверяем работу таймера_

```ruby
systemctl list-timers --all
```

![image](https://github.com/user-attachments/assets/67a25210-b19e-45ed-bc35-6006d99f7ce2)


<br/>

### [:diamond_shape_with_a_dot_inside:](#toc) <a name='9'>Ручное резервное копирование</a>

```ruby
rsync -avz -e ssh --delete ./1111111  vagrant@192.168.56.30:~
```
![image](https://github.com/user-attachments/assets/79e2285a-a9cd-4dbb-878f-5f4f98b7712d)


<br/>

### [:diamond_shape_with_a_dot_inside:](#toc) <a name='recommended_sources'>Дополнительные источники</a>

- [Установка и настройка BORG backup](https://www.bytelink.ru/byte-wiki/rezervirovanie-i-backups/ustanovka-i-nastrojka-sistemy-rezervnykh-kopij-borg)
- [Теория и практика бэкапов с Borg](https://habr.com/ru/companies/flant/articles/420055/)
- [Backup с помощью systemd вместо cron](https://ekhlakov.blogspot.com/2014/04/backup-systemd-cron.html)
