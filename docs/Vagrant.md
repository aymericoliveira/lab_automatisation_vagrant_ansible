# 🛠️ Documentation Vagrant

Ce fichier documente en détail la configuration et le fonctionnement de la partie **Vagrant** du projet. L'objectif est de déployer rapidement une infrastructure locale composée de trois machines virtuelles :

- 1 serveur HAProxy (load balancer)
- 2 serveurs NGINX (www1 et www2)

---

## 📦 Objectifs

- Automatiser la création des VMs avec des IPs fixes
- Préparer une base propre pour les playbooks Ansible
- Simuler un environnement réseau simple pour un usage local

---

## ⚙️ Configuration réseau

Chaque machine utilise un **réseau privé** avec des adresses IP statiques. Cela permet d’éviter les conflits DHCP et de simplifier l’accès via `curl` ou un navigateur.

| Nom de la VM | Rôle         | IP privée        | OS utilisé     |
|--------------|--------------|------------------|----------------|
| `haproxy`    | Load balancer| `192.168.10.100` | Debian 12      |
| `www1`       | Web serveur  | `192.168.10.111` | Debian 12      |
| `www2`       | Web serveur  | `192.168.10.112` | Debian 12      |

> Remarque : les IPs sont définies manuellement dans le `Vagrantfile`.

---

## 📁 Structure du Vagrantfile

Voici les principaux blocs du fichier `Vagrantfile` :

```ruby

# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "debian/bookworm64"
  config.vm.box_version = "12.20250126.1"

  # Dossier partagé pour que la VM ansible ait accès aux fichiers
  config.vm.synced_folder ".", "/vagrant"

  # VM Ansible
  config.vm.define "ansible" do |ansible|
    ansible.vm.hostname = "ansible"
    ansible.vm.network "private_network", ip: "192.168.10.10"
    ansible.vm.provider "virtualbox" do |vb|
      vb.name = "ansible"
      vb.memory = 512
    end
  end

  # VM haproxy
  config.vm.define "haproxy" do |haproxy|
    haproxy.vm.hostname = "haproxy"
    haproxy.vm.network "public_network"
    haproxy.vm.network "private_network", ip: "192.168.10.100"
    haproxy.vm.provider "virtualbox" do |vb|
      vb.name = "haproxy"
    end
  end

  # VM www1 et www2
  (1..2).each do |i|
    config.vm.define "www#{i}" do |www|
      www.vm.hostname = "www#{i}"
      www.vm.network "private_network", ip: "192.168.10.11#{i}"
      www.vm.provider "virtualbox" do |v|
        v.name = "www#{i}"
      end
    end
  end
end

```

## ▶️ Lancement
Pour démarrer l'infrastructure :

```bash
vagrant up
```

Tu peux démarrer une seule VM à la fois si besoin :
```bash
vagrant up haproxy
```

Et te connecter à une VM :
```bash
vagrant ssh haproxy
```

## 🛜 Communication entre VMs
Toutes les machines peuvent se joindre via leurs IPs privées. Exemple :
```bash
ping 192.168.10.111  # depuis haproxy vers www1
```

## 📌 Notes

- Chaque VM est indépendante, mais toutes sont sur le même réseau.
- Les configurations initiales (hostname, réseau) sont définies par Vagrant.
- Aucune installation de paquets n’est faite dans Vagrant ; tout est géré via Ansible ensuite.


