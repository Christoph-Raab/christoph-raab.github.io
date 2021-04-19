## System Logging in Linux

- Linux nutzt den syslog standard für Logging von Nachrichten von Services/Prozessen
- Jede Nachricht hat ein Label ``<Facility>`` mit ``<Severity>``
  - Facility: Hinweise auf den Ursprung, zB ``kern`` -> Kernel oder ``mail`` -> Mail
  - Severity: 
    - 0 Emergency - emerg / panic 
    - 1 Alert - alert
    - 2 Error - err (error)
    - 3 Critical - crit
    - 4 Warning - warn (warning)
    - 5 Notice - notice
    - 6 Info - info
    - 7 Debug - debug
- Logging Rules
  - Wurden früher von ``syslogd`` ausgeführt, heute ``rsyslog`` oder ``syslog-ng`` (folgendes gilt für ``rsyslog``)
  - Konfiguration von Regeln liegt in ``/etc/rsyslog.conf``
  - Zusätzliche Konfigurtion kann mit ``IncludeConfig`` mit einbezogen werden, zB ``IncludeConfig /etc/rsyslog.d/*.conf``
  - Regeln bestehen aus zwei Feldern, ``<Facility>.<Severity>``
    - Wildcard werden unterstützt, zB ``mail.*``
    - Severity kann auch weggelassen werden, zB ``mail.*`` = ``mail``
    - Ignorieren alles Messages über ``.none``, zB ``mail.none``
    - Können aneinandergereiht werden, zB ``*.*;mail.info;auth.none`` (= alles, mail nur info, auth nichts)
  - Konfigurieren, wohin die Nachrichten geschrieben werden 
    - ``<Facility>.<Severity>  <path-to-logfile>``, zb ``mail.*  /var/log/mail.log``
    - Es können auch mehrere Ausgabepfade für unterschiedliche Severity Level konfiguriert werden
      ```bash
      mail.info   /var/log/mail.info
      mail.warn   /var/log/mail.warn
      ```
    - ``-`` am Anfang vom Pfad aktiviert Caching, dabei können aber log Nachrichten verloren gehen
- Eigene Syslog Nachrichten schreiben
  - Über ``logger`` Befehl
  - ``-p`` Argument, um FACILITY.SEVERIFY mitzugeben
  - ``-t`` Argument, um die Nachricht zu taggen
- Log Rotation
  - Logrotate Tool hilft, die Logs zu rotieren, zu komprimieren, zu löschen oder zu mailen
  - Konfiguration in ``/etc/logrotate.conf`` hinterlegt
  - Weitere Konfigurationen mit ``Include`` hinzufügen -> ``Include /etc/logrotate.d``
  - Es gibt eine Default Konfiguration, die für alle gilt
    ```bash
    cat /etc/logrotate.d
    # rotate weekly
    weekly

    # keep 4 weeks worth of backlogs
    rotate 4

    # create new (empty) log files after rotating
    create

    # uncomment if you want your log files comressed
    #compress

    # packages drop log rotatoin information into this directory
    include /etc/logrotate.d
    ```
  - Weitere Konfigurationen können je Prozess konfiguriert werden
    ```bash
    /var/log/syslog
    {
        rotate 7
        daily
        missingok
        ...
    }
    /var/log/mail.info
    /var/log/mail.warn
    ...
    {
    ...
    }
    ```
  - Optionen
    - ratate ``<count>`` - Rotate Logs ``<count>`` mal bevor sie gelöscht werden
    - daily - tägliche rotieren
    - weekly - wöchentlich rotieren
    - missingok - Falls ein Log File fehlt, keinen Fehler verursachen
    - notifempty - Leere Files nicht rotieren
    - compress - Rotierte Files komprimieren
    - postrotate \n xyz \n endscript - xyz wird am Ende der Rotation mit ``/bin/sh`` ausgeführt
  - Rotation testen durch force
    - ``logrotate -fv /etc/logrotate.conf``
    - ``-f`` force rotate
    - ``-v`` verbose
