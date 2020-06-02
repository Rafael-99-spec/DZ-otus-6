Описание ДЗ 6

1) Разница между методами получения шелла в процессе загрузки

Способ 1. init=/bin/sh

Отличительной чертой является попадание в шелл в режиме Read-Only, поскольу в настройках grub mode не удален параметр ro, который дает права только для чтения. При использрвании данного метода необходимо перемонтирование корневой файловой системы для настройки привилегии на чтение.

Способ 2. rd.break 

После попадания в Emergency mode необходимо перемонтирвать /sysroot с правами на чтении с целью смены парол. При использрвании данного метода также необходимо.

Способ 3. rw init=/sysroot/bin/sh

Данный метод является для меня сам предпочитаемым. Самое важное отличие в том что, мы заменяем данную команду на ro, и поэтому попадаем в корневую файловую систему с правами на чтение.

2) Установка Centos 7 на ВМ и переименование VG в LVM   (dz6-1.log)

Осуществляем смену имени группы томов centos (VG) на OtusRoot с помощью команды vgrename.

[root@localhost ~]# vgrename centos OtusRoot

  Volume group "centos" successfully renamed to "OtusRoot"
  
После чего меняем во всех трех файлах конфигурации(/etc/fstab,/etc/default/grub,/boot/grub2/grub.cfg)grub имя centos VG на обнавленное имя OtusRoot.

Далее обновляем настройки initrd, который предназначен для загрузки корневой файловой системы

[root@localhost ~]# mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)

После успешной перезагрузки можно проверить применились ли изменения по смене имени VG.

[root@localhost ~]# vgs

  VG     #PV #LV #SN Attr   VSize   VFree
  
  OtusRoot   1   2   0 wz--n- <15.00g    0 
  
3) Добавление модуля в initrd   (dz6-2.log)
  
Скачиваем репозиторий с нужными файлами
  
Создаем скрипт который установливает модуль и и вызывает второй скрипт, который в свю очередь рисует фигуру, которая находиться внутри файла. Копируем оба скрипта в созданию директорию /usr/lib/dracut/modules.d/01test.

Далее пересобираем образ initrd
  
[root@localhost dracut-root-lv-resize]# mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
Executing: /usr/sbin/dracut -f -v /boot/initramfs-3.10.0-1127.8.2.el7.x86_64.img 3.10.0-1127.8.2.el7.x86_64
dracut module 'modsign' will not be installed, because command 'keyctl' could not be found!
dracut module 'busybox' will not be installed, because command 'busybox' could not be found!
dracut module 'crypt' will not be installed, because command 'cryptsetup' could not be found!
dracut module 'dmraid' will not be installed, because command 'dmraid' could not be found!
dracut module 'dmsquash-live-ntfs' will not be installed, because command 'ntfs-3g' could not be found!
dracut module 'mdraid' will not be installed, because command 'mdadm' could not be found!
dracut module 'multipath' will not be installed, because command 'multipath' could not be found!
dracut module 'cifs' will not be installed, because command 'mount.cifs' could not be found!
dracut module 'iscsi' will not be installed, because command 'iscsistart' could not be found!
dracut module 'iscsi' will not be installed, because command 'iscsi-iname' could not be found!
95nfs: Could not find any command of 'rpcbind portmap'!
dracut module 'modsign' will not be installed, because command 'keyctl' could not be found!
dracut module 'busybox' will not be installed, because command 'busybox' could not be found!
dracut module 'crypt' will not be installed, because command 'cryptsetup' could not be found!
dracut module 'dmraid' will not be installed, because command 'dmraid' could not be found!
dracut module 'dmsquash-live-ntfs' will not be installed, because command 'ntfs-3g' could not be found!
dracut module 'mdraid' will not be installed, because command 'mdadm' could not be found!
dracut module 'multipath' will not be installed, because command 'multipath' could not be found!
dracut module 'cifs' will not be installed, because command 'mount.cifs' could not be found!
dracut module 'iscsi' will not be installed, because command 'iscsistart' could not be found!
dracut module 'iscsi' will not be installed, because command 'iscsi-iname' could not be found!
95nfs: Could not find any command of 'rpcbind portmap'!
*** Including module: bash ***
*** Including module: test ***
*** Including module: nss-softokn ***
*** Including module: i18n ***
*** Including module: network ***
*** Including module: ifcfg ***
*** Including module: drm ***
*** Including module: plymouth ***
*** Including module: dm ***
Skipping udev rule: 64-device-mapper.rules
Skipping udev rule: 60-persistent-storage-dm.rules
Skipping udev rule: 55-dm.rules
*** Including module: kernel-modules ***
*** Including module: lvm ***
Skipping udev rule: 64-device-mapper.rules
Skipping udev rule: 56-lvm.rules
Skipping udev rule: 60-persistent-storage-lvm.rules
*** Including module: qemu ***
*** Including module: resume ***
*** Including module: rootfs-block ***
*** Including module: terminfo ***
*** Including module: udev-rules ***
Skipping udev rule: 40-redhat-cpu-hotplug.rules
Skipping udev rule: 91-permissions.rules
*** Including module: biosdevname ***
*** Including module: systemd ***
*** Including module: usrmount ***
*** Including module: base ***
*** Including module: fs-lib ***
*** Including module: microcode_ctl-fw_dir_override ***
  microcode_ctl module: mangling fw_dir
    microcode_ctl: reset fw_dir to "/lib/firmware/updates /lib/firmware"
    microcode_ctl: processing data directory  "/usr/share/microcode_ctl/ucode_with_caveats/intel"...
intel: model '', path ' intel-ucode/*', kvers ''
intel: blacklist ''
    microcode_ctl: intel: Host-Only mode is enabled and "intel-ucode/06-9e-09" matches "intel-ucode/*"
      microcode_ctl: intel: caveats check for kernel version "3.10.0-1127.8.2.el7.x86_64" passed, adding "/usr/share/microcode_ctl/ucode_with_caveats/intel" to fw_dir variable
    microcode_ctl: processing data directory  "/usr/share/microcode_ctl/ucode_with_caveats/intel-06-2d-07"...
intel-06-2d-07: model 'GenuineIntel 06-2d-07', path ' intel-ucode/06-2d-07', kvers ''
intel-06-2d-07: blacklist ''
intel-06-2d-07: caveat is disabled in configuration
    microcode_ctl: kernel version "3.10.0-1127.8.2.el7.x86_64" failed early load check for "intel-06-2d-07", skipping
    microcode_ctl: processing data directory  "/usr/share/microcode_ctl/ucode_with_caveats/intel-06-4f-01"...
intel-06-4f-01: model 'GenuineIntel 06-4f-01', path ' intel-ucode/06-4f-01', kvers ' 4.17.0 3.10.0-894 3.10.0-862.6.1 3.10.0-693.35.1 3.10.0-514.52.1 3.10.0-327.70.1 2.6.32-754.1.1 2.6.32-573.58.1 2.6.32-504.71.1 2.6.32-431.90.1 2.6.32-358.90.1'
intel-06-4f-01: blacklist ''
intel-06-4f-01: caveat is disabled in configuration
    microcode_ctl: kernel version "3.10.0-1127.8.2.el7.x86_64" failed early load check for "intel-06-4f-01", skipping
    microcode_ctl: processing data directory  "/usr/share/microcode_ctl/ucode_with_caveats/intel-06-55-04"...
intel-06-55-04: model 'GenuineIntel 06-55-04', path ' intel-ucode/06-55-04', kvers ''
intel-06-55-04: blacklist ''
intel-06-55-04: caveat is disabled in configuration
    microcode_ctl: kernel version "3.10.0-1127.8.2.el7.x86_64" failed early load check for "intel-06-55-04", skipping
    microcode_ctl: final fw_dir: "/usr/share/microcode_ctl/ucode_with_caveats/intel /lib/firmware/updates /lib/firmware"
*** Including module: shutdown ***
*** Including modules done ***
*** Installing kernel module dependencies and firmware ***
*** Installing kernel module dependencies and firmware done ***
*** Resolving executable dependencies ***
*** Resolving executable dependencies done***
*** Hardlinking files ***
*** Hardlinking files done ***
*** Stripping files ***
*** Stripping files done ***
*** Generating early-microcode cpio image contents ***
*** Constructing GenuineIntel.bin ****
*** Store current command line parameters ***
*** Creating image file ***
*** Creating microcode section ***
*** Created microcode section ***
*** Creating image file done ***
*** Creating initramfs image file '/boot/initramfs-3.10.0-1127.8.2.el7.x86_64.img' done ***  

При перезагрузке системы заново заходми в grub и удаляем опции rghb и quiet. После успешного выполнения всех вышеуказанных шагов во время загрузки systemd можно разглядеть пингвина который изображен в скрипте test.sh.
