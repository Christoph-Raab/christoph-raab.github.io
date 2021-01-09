---
layout: topic
title: dns
parent: networking
---

## DNS Basics

In ``/etc/hosts`` können DNS Einträge angegeben werden, um Hostnamen auf IPs aufzulösen.

Ein DNS Server dient als Speicher für Einträge, auf den alle Hosts zugreifen können (``nameserver`` Eintrag in ``/etc/resolv.conf``). Dadurch entfällt updaten aller Hosts. Lokale Einträge überschreiben aber die auf dem DNS Server.

Es können mehrere Nameserver (DNS Server) aufgelistet werden, um interne und externe DNS Resolution zu ermöglichen.

### Domain Names

- Top Level Domain
  Domain Endung, zB .de oder .com
  
- Subdomain 
  ermöglichen Untergruppen

Mit einem ``search`` Eintrag in ``/etc/resolv.conf`` kann eine Domain angegeben werden, damit lokal Subdomains auf Domains gemappt werden kann.

### Record Types

- A Record: IPv4 -> Hostname
- AAAA Record: IPv6 -> Hostname
- CNAME Record: Hostname -> Hostname

### Tools

- ping
- nslookup (Beachtet aber lokale Einträge in ``/etc/hosts`` nicht)
- dig
