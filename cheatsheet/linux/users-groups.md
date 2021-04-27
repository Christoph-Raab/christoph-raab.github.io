## Users and Groups

### User

```bash
$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
username:password:UID:GID:comments:home_dir_shell
```
- x bei password -> Passwort in ``/etc/shadow`` hinterlegt
- Username sollte < 8 chars sein, bis zu 32 wird unterstütz, aber dann wird evtl. nur UID angezeigt
- Zur UID 
  - Kann in ``/etc/login.defs`` angepasst werden
  - UID root ist immer 0
  - UID < 1000 für System Accounts
- Zur GID
  - Ist die Group ID der Gruppe
  - Dateien, die vom Nutzer angelegt werden, haben diese GID
  - Mit ``newgrp`` kann der Nutzer in eine andere Standard Gruppe wechseln, von der er Mitglied ist
- Bei login landet der Nutzer im angegebenen ``home_dir``
- Bei login startet die angegebende Shell, bzw das angegebene Programm wird ausgeführt
  - Mit ``/usr/sbin/nologin`` oder ``/bin/false`` sicherstellen, dass der Nutzer von niemandem interaktiv genutzt werden kann
  - Kann genutzt werden, um den Anwender bei Login zB in eine bestimmte Anwendung zu zwingen

### User anlegen

``useradd [option] <username>``

- ``-c "Kommentar"`` Kommentar hinzufügen
- ``-m`` Home Directory anlegen mit Default ``/home/<username>`` und ``/etc/skel`` reinkopieren
- ``-d /path/to/home`` Anderes Home Directory festlegen
- ``-s /path/to/shell`` Shell festlegen
- ``-g GROUP`` Default Group festlegen
- ``-G GROUP1,GROUP2`` Nutzer zu zusätzlichen Gruppen hinzufügen
- ``-r`` System Account anlegen: UID aus Range in ``/etc/login-defs`` hinterlegt
- ``-u`` UID festlegen

### Passwort vergeben/ändern

``passwd <username>``

### User editieren

``usermod [option] <username>``

- ``-c`` Kommentar updaten
- ``-g GROUP`` Primäre Gruppe festlegen
- ``-G GROUP1,GROUP2`` Zusätzliche Gruppen ändern
- ``-s /path/to/shell`` Shell ändern

-> User zu Gruppe hinzufügen

``usermod -aG <group> <username>``

### User löschen

``userdel [option] <username>``

- ``-r`` Home Directory löschen

## Gruppen

```bash
$ cat /etc/group
root:x:0:
group_name:password:GID:account1,account2
```

- x bei password -> Passwort wird genutzt, liegt in ``/etc/gshadow``
- root Gruppe hat keine Mitglieder. root ist Mitglied, da die Default Group ``0`` ist
- Wenn man im ``/etc/passwd`` eine Primar Group ID hat, ist man auch dann Mitglied der Gruppe, wenn man in ``/etc/group`` nicht als Mitglied aufgeführt ist

### Gruppen von Nutzer anzeigen

``groups [options] [username]``

- ohne username -> eigene Gruppen werden angezeigt

### Gruppe anlegen

``groupadd [option] group``

- ``-g`` Numerische Group ID festlegen

### Gruppe editieren

``groupmod [option] group``

- ``-g GID`` Group ID ändern
- ``-n GROUP`` Name der Gruppe ändern

### Gruppe löschen

``groupdel group``
