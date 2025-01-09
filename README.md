Резервное копирование

#### <a name='toc'>Содержание</a>

1. [Система инициализации](#initialization_system)
2. [Управление сервисами при использовании systemd](#managing_services)
3. [Создание unit-файла для мониторинга логов](#create_unit_file)
4. [Установить spawn-fcgi и создать unit-файл](#create_init_spawn)
5. [Дополнительные источники](#recommended_sources)

Lorem ipsum dolor sit amet consetetur sadipscing elitr sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat sed diam voluptua at vero eos et accusam et justo duo dolores et ea rebum stet clita kasd gubergren no sea takimata sanctus est lorem ipsum dolor sit amet.

Minim diam at ipsum tempor. Augue feugait luptatum in lorem tincidunt sed eros elitr aliquip suscipit. Diam molestie sanctus ut. Sadipscing iriure dolor rebum et justo diam nonumy dolore ut sit ipsum ut lorem. No nihil eu lorem sea.

Erat no euismod diam elitr diam erat velit. Diam kasd ipsum sit est laoreet diam commodo laoreet exerci congue hendrerit in vel et takimata accusam est. Elitr sanctus volutpat id at clita kasd et ut sit te et gubergren sadipscing eos takimata sadipscing.

### Тестовый стенд:

![image](https://github.com/user-attachments/assets/ecd995b2-da3f-4f61-b28d-fc495362a613)


Устанавливаем borgbackup на сервер 

```
apt install borgbackup
```

![image](https://github.com/user-attachments/assets/8e4b67df-7ecf-43d9-a74b-7194cb55dcb6)



Устанавливаем borgbackup на сервер 

```
apt install borgbackup
```

![image](https://github.com/user-attachments/assets/1ec2760a-1729-4db1-a221-f01155998567)



### Ручное копирование

```
rsync -avz -e ssh --delete ./1111111  vagrant@192.168.56.30:~
```

![image](https://github.com/user-attachments/assets/79e2285a-a9cd-4dbb-878f-5f4f98b7712d)




#### [[⬆]](#toc) <a name='recommended_sources'>Дополнительные источники</a>

- [Systemd - Википедия](https://ru.wikipedia.org/wiki/Systemd)
- [Система инициализации - systemd](https://basis.gnulinux)
- [Systemd. Библия сисадмина](https://habr.com/ru/companies/)
- [Что такое система инициализации](https://pikabu.ru/story)
- Весь Linux Для тех, кто хочет стать профессионалом, стр.324

https://dzen.ru/a/X_1FafmIRRpCKDj1
