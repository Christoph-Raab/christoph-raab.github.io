## Allgemein

- Puppet = Configuration Management Tool
- Puppet Master (old, ruby based), Puppet Server (new, jvm based)
- Agent = Server, der von Puppet Server gemanagt wird

## Modules

- Modul = Konfiguration für einen Teil der Infrastruktur, zB nginx, Docker, mySQL, ...
- Enthält Installations- und Konfigurationsinformation
- Liegt in ``/etc/puppetlabs/code`` -> modules = allgemeine Module, environment/modules = umgebungsspecifische Module
- Mit Puppet Dev Kit (PDK) erzeugen (muss installiert werden) -> ``pdk new module <name>``
- Inhalt nach setup
  - data: Enthält Hiera Data
  - files: Static Files, zB nginx.conf
  - manifests: Konfigurationsdateien (Classes)
  - templates: Templates, um auf den Nodes Files zu erzeugen

### Classes 
- Classes beinhalten Resource Types 
- liegen in ``/etc/puppetlabls/code/enviroments/<env>/modules/<modul_name>/manifests/``
- Resource Types beinhaltet Attribute, die die Kompenete konfigurieren
- Validieren mit ``puppet parser validate manifests/install.pp``

#### Install package
```
class nginx::install {
  package { 'install_nginx':
    name   => 'nginx',
    ensure => 'present',
  }
}
```

### Init Class

- Jedes Modul braucht eine init.pp Class
- Anlegen mit ``pdk new class <modul_name>``
- Beinhaltet alle Classes, die zu dem Modul gehören
- Reihenfolge der Ausführung der Classes wird hier definiert
  - ``->`` = 'als nächstes'
  - ``~>`` = 'als nächstes, wenn vorheriges sich geändert hat'

```
class nginx {
  contain nginx::install
  contain nginx::config
  contain nginx::service
  
  Class['nginx::install'] 
  -> Class['nginx::config']
  ~> Class['nginx::service']
}
```

### Static File Class

Definiert, wo das nginx.conf File auf dem Server und dem Agent liegt und dass eine Änderung an dem File den Daemon Neustartet (notify -> Service Class von unten)

```
class nginx::config {
  file { 'nginx_config':
    path   => '/etc/nginx/nginx.conf',
    source => 'puppet:///modules/nginx/rh-nginx.conf',
    ensure => 'present',
    notify => Service['nginx_service'],
  }
}
```

``puppet:///`` -> ``/etc/puppetlabs/code/environment/productions/`` Puppet weiß dann, dass das File ``rh-nginx.conf`` im ``files`` Ordner des Modules ``nginx`` gemeint ist.

### Service Class

Sorgt dafür, dass der nginx Daemon läuft und neugestartet wird

```
class nginx::service {
  service { 'nginx_service':
    name       => 'nginx',
    ensure     => 'running',
    enable     => true,
    hasrestart => true,
  }
}
```

### Site.pp

- Liegt in ``/etc/puppetlabls/code/enviroments/<env>/manifests``
- Mappt Module auf Agent Nodes -> Welches Modul soll auf welchem Node installiert werden

```
node <host> {
  include nginx
}
```

### Install Modules

- Catalog Converge führt alle Module für den Agent zusammen
- ``puppet agent -t`` auf dem Agent Node ausführen
