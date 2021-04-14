## Allgemein

- Puppet = Configuration Management Tool
- Puppet Master (old, ruby based), Puppet Server (new, jvm based)
- Agent = Server, der von Puppet Server gemanagt wird

[Übersicht](https://lucid.app/lucidchart/b7237525-63c8-4334-832c-e455c80c317d/view?page=0_0#)

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
class nginx::install ( 
  $package_name = $nginx::params::package_name,
) inherits nginx::params {
  package { 'install_nginx':
    name   => $package_name,
    ensure => $nginx::package_ensure,
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
class nginx ( 
  $package_name  = $nginx::params::package_name,
  $config_path   = $nginx::params::config_path,
  $config_source = $nginx::params::config_source,
  $service_name  = $nginx::params::service_name,
  String $package_ensure,
  String $config_ensure,
  String $service_ensure,
  Boolean $service_enable,
  Boolean $service_hasrestart,
) inherits nginx::params {
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
class nginx::config (
  $config_path  = $nginx::params::config_path,
  $config_source = $nginx::params::source_path
) inherits nginx::params {
  file { 'nginx_config':
    path   => $config_path,
    source => $config_source,
    ensure => $nginx::config_ensure,
    notify => Service['nginx_service'],
  }
}
```

``puppet:///`` -> ``/etc/puppetlabs/code/environment/productions/`` Puppet weiß dann, dass das File ``rh-nginx.conf`` im ``files`` Ordner des Modules ``nginx`` gemeint ist.

### Service Class

Sorgt dafür, dass der nginx Daemon läuft und neugestartet wird

```
class nginx::service (
  $service_name = $nginx::params::service_name,
) inherits nginx::params {
  service { 'nginx_service':
    name       => $service_name,
    ensure     => $nginx::service_ensure,
    enable     => $nginx::service_enable,
    hasrestart => $nginx::service_hasrestart,
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

### OS Spezifische Konfig

Mit ``facter`` Befehl Informationen über den Server/Agent ausgeben.
Es können auch nur Teilinformationen abgefragt werden, zB ``facter os.family``.

- Kann als Node related Data in Heira (weiter unten) oder...
- in einem modulspezifischem ``params.pp`` file gespeichert werden (hier)

Param.pp parametrisiert das Modul.

```
class nginx::params {
  $package_name = 'nginx'
  $service_name = 'nginx'
  
  case $::osfamily {
    'RedHead': {
      $config_path = '/etc/nginx/nginx.conf'
      $config_source = 'puppet:///modules/nginx/rh-nginx.conf'
    }
    'Debian': {
      $config_path = '/etc/nginx/nginx.conf'
      $config_source = 'puppet:///modules/nginx/deb-nginx.conf'
    }
  }
}
```

## Heira

- Puppet key-value store zum Speichern von Parametern als Hierarchie von Daten
- Überschreiben von Parametern mit modul-, node- oder rolespezifischem Daten
- Zentrales Config file in ``/etc/puppetlabs/puppet/hiera.yaml`` für die gesamte Infrastruktur
- Environment spezifisches Config file in ``/etc/puppetlabs/code/environments/<env>/hiera.yaml``
- Modul spezifisches Config file in  ``/etc/puppetlabs/code/environments/<env>/modules/<module>/hiera.yaml``

### Modul Spezifisch

- ``hiera.yaml`` zeigt im default auf data Verzeichnis des Modules
- Darin ist eine leere ``common.yaml`` Datei enthalten
- Darin werden Module spezifische Parameter definiert, die später noch überschrieben werden können

#### Beispiel common

```yaml
---
nginx::package_ensure: 'present'
nginx::config_ensure: 'present'
nginx::service_ensure: 'running'
nginx::service_enable: true
nginx::service_hasrestart: true
```
