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
  - files: Static Files
  - manifests: Konfigurationsdateien
  - templates: Templates, um auf den Nodes Files zu erzeugen

### Classes 
- Classes beinhalten Resource Types 
- liegen in ``/etc/puppetlabls/code/enviroments/<env>/modules/<modul_name>/manifests/install.pp``
- Resource Types beinhaltet Attribute, die die Kompenete konfigurieren
- Validieren mit ``puppet parser validate manifests/install.pp``

### Bsp class
```
class nginx::install {
  package { 'install_nginx':
    name   => 'nginx',
    ensure => 'present',
  }
}
```
