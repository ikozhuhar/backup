Резервное копирование

#### <a name='toc'>Содержание</a>

1. [Тестовый стенд](#1)
2. [Установка и настройка borgbackup](#2)
3. [Создание unit-файла для мониторинга логов](#create_unit_file)
4. [Установить spawn-fcgi и создать unit-файл](#create_init_spawn)
5. [Дополнительные источники](#recommended_sources)

Lorem ipsum dolor sit amet consetetur sadipscing elitr sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat sed diam voluptua at vero eos et accusam et justo duo dolores et ea rebum stet clita kasd gubergren no sea takimata sanctus est lorem ipsum dolor sit amet.

Minim diam at ipsum tempor. Augue feugait luptatum in lorem tincidunt sed eros elitr aliquip suscipit. Diam molestie sanctus ut. Sadipscing iriure dolor rebum et justo diam nonumy dolore ut sit ipsum ut lorem. No nihil eu lorem sea.

Erat no euismod diam elitr diam erat velit. Diam kasd ipsum sit est laoreet diam commodo laoreet exerci congue hendrerit in vel et takimata accusam est. Elitr sanctus volutpat id at clita kasd et ut sit te et gubergren sadipscing eos takimata sadipscing.

### [:diamond_shape_with_a_dot_inside:](#toc) <a name='1'>Тестовый стенд</a>

![image](https://github.com/user-attachments/assets/ecd995b2-da3f-4f61-b28d-fc495362a613)


### Установка и настройка borgbackup

Установка как на сервере, так и на клиенте выполняется одинаково:
```ruby
sudo -i
apt update
apt install borgbackup
```


### Устанавливаем borgbackup на сервер 

```
apt install borgbackup
```

![image](https://github.com/user-attachments/assets/8e4b67df-7ecf-43d9-a74b-7194cb55dcb6)

```
sudo mkdir /var/backup
sudo useradd -s /bin/bash -d /home/borg -m borg
sudo chown borg:borg /var/backup/
sudo passwd borg
su - borg
mkdir .ssh
touch .ssh/authorized_keys
chmod 700 .ssh
chmod 600 .ssh/authorized_keys

```



### Устанавливаем borgbackup на клиенте

```
apt install borgbackup
```

![image](https://github.com/user-attachments/assets/1ec2760a-1729-4db1-a221-f01155998567)


![image](https://github.com/user-attachments/assets/92e1dbed-3304-4e87-800c-be8055eae65d)


Запускаем Бэкап

```ruby
borg create --stats --list borg@192.168.56.160:/var/backup/::"etc-{now:%Y-%m-%d_%H:%M:%S}" /etc
```

![image](https://github.com/user-attachments/assets/797d273a-0910-4b2f-8976-5c93fe0e8afd)
![image](https://github.com/user-attachments/assets/64fc6cd2-0815-45b0-910f-248c91a8aee5)

Смотрим, что у нас получилось

```ruby
borg list borg@192.168.56.160:/var/backup/
```

![image](https://github.com/user-attachments/assets/5f309f55-7156-472b-8605-6bd984daf266)

Смотрим список файлов

```ruby
borg list borg@192.168.56.160:/var/backup/::etc-2025-01-11_11:12:46
```

![image](https://github.com/user-attachments/assets/79e2285a-a9cd-4dbb-878f-5f4f98b7712d)
![image](https://github.com/user-attachments/assets/3ef67d64-35ae-43a2-84d0-9d4d02283aac)


**Восстановление данных из резервной копии**

Перед восстановлением, рекомендуется создать отдельную директорию и перейти в нее:

```ruby
mkdir /mnt/borgrestore
cd /mnt/borgrestore
```

Для восстановления всех файлов из доступного архива ввести команду:

```ruby
borg extract borg@192.168.56.160:/var/backup/::etc-2025-01-11_11:12:46
```

**Для восстановления отдельных файлов или директорий из архива ввести команду:**

```ruby
borg extract borg@192.168.56.160:/var/backup/::etc-2025-01-11_11:12:46 etc/passwd
```

**Монтирование резервной копии в локальную папку (альтернативный вариант восстановления данных)**

borg позволяет смонтировать репозиторий в локальную файловую систему и работать с архивом как с обычной папкой.

Для этого требуется создать точку (директорию) монтирования и подмонтировать в нее требуемую резервную копию с сервера:

```ruby
mkdir /mnt/borgmount
borg extract borg@192.168.56.160:/var/backup/::etc-2025-01-11_11:12:46 /mnt/borgmount
```















### Ручное копирование

```ruby
rsync -avz -e ssh --delete ./1111111  vagrant@192.168.56.30:~
```




#### [[⬆]](#toc) <a name='recommended_sources'>Дополнительные источники</a>

- https://www.bytelink.ru/byte-wiki/rezervirovanie-i-backups/ustanovka-i-nastrojka-sistemy-rezervnykh-kopij-borg
- [Система инициализации - systemd](https://basis.gnulinux)
- [Systemd. Библия сисадмина](https://habr.com/ru/companies/)
- [Что такое система инициализации](https://pikabu.ru/story)
- Весь Linux Для тех, кто хочет стать профессионалом, стр.324

https://dzen.ru/a/X_1FafmIRRpCKDj1
