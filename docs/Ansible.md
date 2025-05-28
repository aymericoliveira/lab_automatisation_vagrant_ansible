# 🤖 Documentation Ansible

Ce fichier documente l’utilisation d’**Ansible** pour provisionner automatiquement les machines virtuelles déployées via **Vagrant**.

L’objectif est d’installer et configurer automatiquement :

- Deux serveurs web (Apache2)
- Un serveur HAProxy (load balancer avec pages d’erreur personnalisées, page statistiques de l'infrastructure)

---

## 🗺️ Architecture ciblée

| Nom     | Rôle          | IP              | OS        |
|----------|---------------|------------------|-----------|
| haproxy  | Load balancer | 192.168.10.100   | Debian 12 |
| www1     | Web serveur   | 192.168.10.111   | Debian 12 |
| www2     | Web serveur   | 192.168.10.112   | Debian 12 |

---

## 🧩 Où installer Ansible ?

Trois options :

1. Sur l’ordinateur hôte
2. Sur une VM Linux dédiée
3. Sur la VM HAProxy

> 💡 Bonne pratique : l’installer sur l’hôte pour que Vagrant communique facilement avec Ansible.

Dans ce projet de lab, Ansible est installé sur **une VM dédiée**. Pour permettre la communication avec Vagrant, un **dossier partagé** a été configuré :

```ruby
# Dossier partagé pour que la VM Ansible ait accès aux fichiers
config.vm.synced_folder ".", "/vagrant"
```
<br>

# 🏗️ Étapes préliminaires

## 1. Création de la VM Ansible
La VM dédiée à Ansible est générée automatiquement via le Vagrantfile.

- [`docs/vagrant.md`](./docs/vagrant.md) : Configuration réseau, Vagrantfile, IPs statiques

## 2. Configuration du fichier **inventory.ini**
Le fichier inventory.ini référence les hôtes et les organise par groupe afin qu'Ansible exécute les tâches ciblées.

## 📁 Il est placé dans le dossier partagé accessible à Ansible.

```bash 
[webservers]
www1 ansible_host=192.168.10.111 ansible_user=vagrant ansible_ssh_private_key_file=/vagrant/.vagrant/machines/www1/virtualbox/private_key
www2 ansible_host=192.168.10.112 ansible_user=vagrant ansible_ssh_private_key_file=/vagrant/.vagrant/machines/www2/virtualbox/private_key

[loadbalancer]
haproxy ansible_host=192.168.10.100 ansible_user=vagrant ansible_ssh_private_key_file=/vagrant/.vagrant/machines/haproxy/virtualbox/private_key
```
## 3. Création du fichier provision.yml

> 💡 Un playbook ou provision.yml est un fichier écrit en YAML qui contient une série d'instructions pour automatiser des tâches de gestion de configuration, de déploiement d'applications, et d'orchestration de systèmes.

> ⚠️ Attention : l'indentation est essentielle dans YAML. Une mauvaise indentation entraîne des erreurs.



```bash 
- hosts: webservers
  become: yes
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
        update_cache: yes

    - name: Créer la page d'accueil
      copy:
        content: "Bonjour le monde {{ inventory_hostname }}"
        dest: /var/www/html/index.html

- hosts: loadbalancer
  become: yes
  tasks:
    - name: Installer HAProxy
      apt:
        name: haproxy
        state: present
        update_cache: yes

    - name: Sauvegarder le fichier haproxy.cfg
      copy:
        src: /etc/haproxy/haproxy.cfg
        dest: /etc/haproxy/haproxy.cfg.bak
        remote_src: yes

    - name: Configurer haproxy.cfg
      copy:
        content: |
          frontend front_httpservers
              bind *:80
              default_backend backend_httpservers
              option forwardfor

          backend backend_httpservers
              balance roundrobin
              server www1 192.168.10.111:80 check
              server www2 192.168.10.112:80 check
        dest: /etc/haproxy/haproxy.cfg

    - name: Redémarrer HAProxy
      service:
        name: haproxy
        state: restarted

```

## 4. Installation d’Ansible
Dans la VM dédiée :

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y ansible
```

# 🚀 Lancement du playbook
Place-toi dans le dossier partagé pour exécuter le playbook :

```bash
cd /vagrant
ansible-playbook -i inventory.ini provision.yml
```

![Lancement du playbook](../screen_vagrant/start_playbook_ansible.png)


## 📸 Exécution du playbook :

✅ Résultat attendu
Les VMs www1 et www2 ont Apache installé + leur page personnalisée.

haproxy distribue le trafic en round-robin.

📸 Exemples de résultats observés :

![SRV_1](../screen_vagrant/screen_roundrobin/teste_roundrobin_www1.png)
![SRV_2](../screen_vagrant/screen_roundrobin/teste_roundrobin_www2.png)
