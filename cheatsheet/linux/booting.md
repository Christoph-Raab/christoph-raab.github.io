## Booting von Linux Systemen

### BIOS 

- Basic Input/Output System
- Teil der Firmware, startet als aller erstes
- Ist OS unabhängig
- Startet erst POST, Power-On Self test, um Hardware Komponenten zu testen
- Danach wird der Bootloader gestartet
- BIOS enthält konfigurierbare Liste von Devices (Festplatte, USB, DVD Laufwerk, ...), von denen das System gebootet werden kann
- Diese Devices werden in einer konfiguierbaren Reihenfolge nach bootbaren Devices durchsucht
- Es gibt Bootloader:
  - LILO, Linux Loader (auf älteren Systemen)
  - GRUB, Grand Unified Bootloader (meist Standard)
- Der Bootloader startet dann das Betriebsssytem


### Initial RAM Disk und der Kernel

- Initial RAM Disk, initrd, ist ein temporäres FileSystem, das in Memory geladen wird, wenn das System bootet
- Es enthält Helfer, die die Hardware erkennen und die notwendigen Treiber laden
- Ab einem gewissen Punkt mountet initrd das tatsächliche FileSystem. Ab dann wird das OS von dort geladen, statt aus initrd
- Kernel, initrd und weitere Dateien, die für das Booten notwendig sind, liegen in ``/boot``
- Der Linux Kernel heißt normalerweise ``vmlinux``, wenn er komprimiert ist, heißt er ``vmlinuz``


### Kernel Ring Buffer

- Enthält Nachrichten, die mit dem Linux Kernel in Verbindung stehen
- Befehl ``dmesg`` zeigt Inhalt des Buffers an
- Die Nachrichten liegen meistens auf in ``/var/log/dmesg``
- Rotierendes Log, dh hat immer die gleiche Größe, alte Nachrichten fallen raus, wenn der Buffer voll ist


### Runlevels and Targets

- Runlevels werden genutzt um festzustellen, welcher Prozess oder Service gestartet werden soll
- Folgende Runlevels sind standardmäßig konfiguriert:
  - 0: Power Off
  - 1: Single-User Mode
  - 2: Multi-User Mode mit Graphical Interface (Debian/Ubuntu)
  - 3: Multi-User Mode mit Text Mode (RetHat/CentOS)
  - 4: undefined
  - 5: Multi-User Mode mit Graphical Interface (RetHat/CentOS)
  - 6: Reboot
- Früher wurden Programme vom init Programm kontrolliert
  - in ``/etc/inittab`` konfiguriert
  - Default ändern: 
    - zB runlevel 3 als default setzen: ``id:3:initdefault:``
  - Runlevel ändern:
    - ``telinit 5``
  - Reboot:
    - ``telinit 6``
  - Power Off
    - ``telinit 0``
- Heute wird eher systemd genutzt
  - Systemd nutzt targets statt runlevels, ist aber ungefär das gleiche Konzept
  - Alle verfügbaren Targets einsehen in ``/lib/systemd/system``
  - Targets sind als symlinks angelegt
  - Default ändern: 
    - ``systemctl set-default multi-user.target``
    - Oder in ``/etc/systemd/system/default.target`` anpassen
  - Runlevel ändern:
    - ``systemctl isolate graphical.target`` (= ``telinit 5``)
  - Reboot:
    - ``systemctl isolate reboot.target`` (= ``telinit 6``)
    - ``reboot``
    - ``shutdown -r now``
  - Power Off
    - systemctl isolate poweroff.target`` (= ``telinit 0``)
    - ``poweroff``
- Alternative zu systemd: upstart
