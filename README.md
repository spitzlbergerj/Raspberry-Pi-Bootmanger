# Raspberry-Pi-Bootmanger
Eine Kommandosammlung um eine Art Bootmanager auf dem Raspberry Pi zu verwirklichen.

Die Idee ist jeweils, auf dem Speichersystem des Raspberry Pi (SD Karte oder SSD Platte) neben den ohnehin vorhandenen Partitionen bootfs und rootfs weitere Partitionen anzulegen.
Die nächste Root Partition wird in der Datei /boot/cmdline.txt bzw. /boot/firmware/cmdline.txt festlegt.

Die notwendigen UUIDs liefert das Kommando

`sudo blkid`

mit z.B. folgender Ausgabe

`
/dev/sda2: LABEL="rootfs" UUID="c56e36bb-eae5-4005-83dd-d9ee706a98f8" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="c0374a6c-02"
/dev/sda7: LABEL="rootfs-5" UUID="84c7eb52-3153-46ff-91a0-9ec8e21142b4" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="c0374a6c-07"
/dev/sda5: LABEL="rootfs-3" UUID="e0c6d5d6-47d5-4be3-aec8-692a71f8f9ed" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="c0374a6c-05"
/dev/sda3: LABEL="rootfs-2" UUID="7add68ed-2356-4b23-9584-1c0a9c0d2a9c" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="c0374a6c-03"
/dev/sda1: LABEL_FATBOOT="bootfs" LABEL="bootfs" UUID="EF6E-C078" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="c0374a6c-01"
/dev/sda6: LABEL="rootfs-4" UUID="6397791e-7fca-4971-bc2c-b612cacef43f" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="c0374a6c-06"
`

Die `cmdline.txt` enthält in der Regel folgendes:

`console=serial0,115200 console=tty1 root=PARTUUID=c0374a6c-06 rootfstype=ext4 fsck.repair=yes rootwait quiet splash plymouth.ignore-serial-consoles cfg80211.ieee80211_regdom=DE`

Dabei gibt `root=PARTUUID=c0374a6c-06` die notwenigige Information an. Das nächste Rootfilessystem wird von /dev/sda6 gezogen werden.

## Variante 1 - pi-boot-switch

Eine Umgebung, die das vollständig zu mamagen versucht ist dieses Repository

https://github.com/bablokb/pi-boot-switch

Erklärung unter
https://www.raspberry-pi-geek.de/ausgaben/rpg/2020/02/multiboot-fuer-raspbian-2/


## Variante 2 - manuelles Umschalten und rpi-clone

pi-boot-switch hat bei mir allerdings auf aktuellem Rasperry Pi OS nicht fehlerfrei funktioniert. Daher habe ich die Umschaltungen manuell vorgenommen, indem ich die cmdline.txt editiert habe. 

Zum Kopieren der Partitionsinhalte habe ich pi-boot-switch Kommandos benutzt:

`
umount /dev/sda2
sudo pi-boot-switch -C /dev/sda7 -t /dev/sda2 -L "rootfs" -D "Original" -F
`
- Hänge /dev/sda2 aus
- Kopiere von /dev/sda7 auf /dev/sda2
- vergebe eine Label "rootfs" und eine Description" Original" an /dev/sda2
- Formatiere /dev/sda2 vor der Kopie

Dies müsste aller dings auch mit rpi-cline einfach möglich sein.
