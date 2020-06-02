Описание ДЗ 6

1) Разница между методами получения шелла в процессе загрузки

Способ 1. init=/bin/sh

Отличительной чертой является попадание в шелл в режиме Read-Only, поскольку в настройках grub mode не удален параметр ro, который дает права только для чтения. При использовании данного метода необходимо перемонтирование корневой файловой системы для настройки привилегии на чтение.

Способ 2. rd.break 

После попадания в Emergency mode необходимо перемонтировать /sysroot с правами на чтении с целью смены пароля. При использовании данного метода также необходимо.

Способ 3. rw init=/sysroot/bin/sh

Данный метод является для меня сам предпочитаемым. Самое важное отличие в том что, мы заменяем данную команду на ro, и поэтому попадаем в корневую файловую систему с правами на чтение.

2) Установка Centos 7 на ВМ и переименование VG в LVM   (dz6-1.log)

Осуществляем смену имени группы томов centos (VG) на OtusRoot с помощью команды vgrename.

[root@localhost ~]# vgrename centos OtusRoot

  Volume group "centos" successfully renamed to "OtusRoot"
  
После чего меняем во всех трех файлах конфигурации(/etc/fstab,/etc/default/grub,/boot/grub2/grub.cfg)grub имя centos VG на обновлённое имя OtusRoot.

Далее обновляем настройки initrd, который предназначен для загрузки корневой файловой системы

[root@localhost ~]# mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)

После успешной перезагрузки можно проверить применились ли изменения по смене имени VG.

[root@localhost ~]# vgs

  VG     #PV #LV #SN Attr   VSize   VFree
  
  OtusRoot   1   2   0 wz--n- <15.00g    0 
  
3) Добавление модуля в initrd   (dz6-2.log)
  
Скачиваем репозиторий с нужными файлами
  
Создаем скрипт который устанавливает модуль и вызывает второй скрипт, который в свою очередь рисует фигуру, которая находиться внутри файла. Копируем оба скрипта в созданию директорию /usr/lib/dracut/modules.d/01test.

Далее пересобираем образ initrd
  
[root@localhost dracut-root-lv-resize]# mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
Executing: /usr/sbin/dracut -f -v /boot/initramfs-3.10.0-1127.8.2.el7.x86_64.img 3.10.0-1127.8.2.el7.x86_64

*** Store current command line parameters ***

*** Creating image file ***

*** Creating microcode section ***

*** Created microcode section ***

*** Creating image file done ***

*** Creating initramfs image file '/boot/initramfs-3.10.0-1127.8.2.el7.x86_64.img' done ***  

При перезагрузке системы заново заходим в grub и удаляем опции rghb и quiet. После успешного выполнения всех вышеуказанных шагов во время загрузки systemd можно разглядеть пингвина который изображен в скрипте test.sh.
