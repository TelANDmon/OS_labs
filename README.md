##Практика 1

## Освоить практический workflow разработчика системного ПО при работе с исходным кодом ядра
Linux — от установки toolchain до наблюдения загрузки собственноручно собранного ядра, — как
базовый навык для всех последующих лабораторных работ курса, требующих модификации и
пересборки ядра.

##Версии

gcc 16.2.1
GNU Make 4.4.1
QEMU 10.2.2
BusyBox 1.36.1

##Конфигурация ядра

CONFIG_64BIT=y
CONFIG_BLK_DEV_INITRD=y
CONFIG_SERIAL_8250_CONSOLE=y

##Сборка ядра

time make KCFLAGS="-Wno-error=unused-but-set-variable" -j$(nproc)
arch/x86/boot/bzImage
Размер: 15М
Время: около 40 минут

##Сборка BusyBox
CONFIG_STATIC=y
собрано командой - make -j$(nproc)
после успешной сборки - make -j$(nproc)
в результате появилась директория _install

##Создание initframs

содержимое busybox было скопировано в нее - cp -a ~/lab1_OS/busybox-1.36.1/_install/* .
созданы системные каталоги - mkdir -p proc sys dev
содержимое init:
#!/bin/sh
mount -t proc none /proc
mount -t sysfs none /sys
echo "Custom initramfs loaded successfully"
exec /bin/sh

создание архива initframs - find . -print0 | cpio --null -ov --format=newc | gzip -9 > ~/initramfs.img

##Запуск ядра в QEMU

запуск:
qemu-system-x86_64 \
  -kernel linux-6.12.48/arch/x86/boot/bzImage \
  -initrd ~/initramfs.img \
  -append "console=ttyS0" \
  -nographic


