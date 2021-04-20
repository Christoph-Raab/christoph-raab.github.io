## Disk Management

- Device/Storage kann protitioniert werden, um Teile davon für unterschiedliche Zwecke zu nutzen
- Verhindert, dass ein Teil des Systems einen anderen Teil nicht beeinflusst (zB viele Userdaten -> /home ist voll, aber Applications können trotzdem laufen)

### Partitionierungsschemata

- Früher wurde MBR genutzt, aufgrund der maximalen Größe von 2 TB wurde es von GPT abgelöst
- GPT (GUID (global unique identifier) Partition Table) ist Teil von UEFI (Unified Extensible Firmware Interface) (UEFI ist neue Art BIOS)
  - Unterstützt bis zu 128 Partitionen
  - Unterstützt Größe bis 9.4 ZB
  - Auf alten System meist nicht unterstützt

### Mount Points

- Directory das verwendet wird, um auf die Daten einer Partition zuzugreifen
- Es gibt immer mindestens den Mount Point ``/``
- Wenn man eine Partition unmountet und in ein anderes Directory mountet, sind die Daten von da an dort erreichbar
- Wenn man eine Partition auf ein Directory mountet, in dem bereits Daten vorhanden waren, sind diese Daten danach nicht mehr erreichbar. Die Daten sind aber noch vorhanden. Durch unmounten werden die Daten wieder erreichbar
- Partitionen können überall gemountet werden
- Partitionen können auch andere Mount Points enthalten, zb ``/home`` -> ``/dev/abc`` und ``/home/craab`` -> ``/dev/def``. ``/home`` muss dafür aber als erstes gemountet werden

### Partitionen erzeugen

- Können mit ``fdsik`` erzeugt werden
- ``fdisk -l`` zeigt alle devices an
- ``fdsik /dev/sdb`` ermöglicht es die Partition auf ``/dev/sdb`` zu managen
  - ``m`` für Hilfe
  - Von dort weiterhangeln, je nach Aufgabe

### Dateisysteme

- Bevor die Partition genutzt werden kann, muss ein Dateisystem erzeugt werden
- Extended File System (ext) ist speziell für Linux, mittlerweile gibt es auch ext2, ext3 und ext4. Diese werden meistens benutzt
- Erzeugen mit ``mkfs -t <typ> <path-to-device>``, zB ``mkfs -t ext4 /dev/sdb``

### Partitionen mounten/unmounten

- Mit ``mount`` alle gemounteten Partitionen anzeigen, enthält aber auch virtuelle Dateisysteme. Besser ``df -h``
- Partition mounten mit ``mount <source-path> <destination-path>``, zB ``mount /dev/sdb /opt`` 
- Manuell mounts überleben einen Neustart nicht. Müssen in ``/etc/fstab`` eingetragen werden
- Partition unmounten mit ``unmount <path-to-directory>``, zB ``unmount /opt``

### Swap Space

- Falls zu wenig RAM vorhanden ist, können Dateien, die von einer Anwendung benötigt werden, hier abgelegt werden
- Anlegen mit ``mkswap <path-to-device>``, zB ``mkswap /dev/sdb``
- Alle anzeigen mit ``mkswap -s``

### File System Tables

- 
