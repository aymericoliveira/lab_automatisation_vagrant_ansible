# 🗺️ Plan du repository

Lab_Vagrant_Ansible/  
├── README.md               # Vue d’ensemble du projet    
├── docs/                   # Dossier de documentation  
│   ├── Ansible.md          # Détails des rôles Ansible, commandes associées  
│   ├── commandes_vagrant.md     # Toutes les commandes utiles utilisées  
│   ├── HAProxy.md          # Rôle et configuration logique du load balancer  
│   └── Vagrant.md          # Explication détaillée de la partie Vagrant  
├── screen_vagrant/         # Captures d’écran  
│   ├── page_statistique/   # Affichage des statistiques via HAProxy  
│   └── screen_roundrobin/  # Exemple de load balancing Round Robin  
├── .gitignore              # Fichiers/dossiers à exclure du versionnement  


<br> 

# Infrastructure Web avec Vagrant + Ansible + HAProxy

Ce projet vise à déployer une infrastructure web locale automatisée avec **Vagrant** et **Ansible**, incluant un **load balancer HAProxy**, deux serveurs web Apache2, et une interface de statistiques sécurisée.

## 🧱 Architecture

- `haproxy` : Load balancer avec ACLs, redirections, page 403 personnalisée
- `www1` et `www2` : Serveurs web NGINX affichant un message simple
- `ansible` : Automatisation complète de la configuration via playbooks
- `vagrant` : Provisionnement de l'infrastructure en local avec VirtualBox

## 🔁 Fonctionnement

1. `Vagrant` crée 3 VMs (HAProxy, www1, www2)
2. `Ansible` configure automatiquement chaque VM selon son rôle :
   - Installation des paquets
   - Configuration réseau
   - Déploiement de HAProxy, Apache2, page d’erreur 403
   - Sécurisation avec ACL
   - Interface de stats HAProxy sur le port 8888

## 🛡️ Sécurité

- ACL HAProxy sur `haproxy.lab.local`
- Page personnalisée 403 si le domaine ne correspond pas
- Authentification HTTP sur `/stats`
- Clés SSH en cours d’intégration pour une gestion sécurisée

## 📁 Documentation

- [`docs/vagrant.md`](./docs/vagrant.md) : Configuration réseau, Vagrantfile, IPs statiques
- [`docs/ansible.md`](./docs/ansible.md) : Inventaire, rôles, playbooks
- [`docs/commandes_vagrant.md`](./docs/commandes_vagrant.md) : Commandes utiles (Vagrant, Ansible)
- [`docs/images/`](./docs/images/) : Captures d’écran HAProxy, tests `curl`, etc.

## 🚀 Lancer le projet

```bash
vagrant up
```
<br>
<br>

## Autre évolution possible Sécurité du LAB : 

### **Sécurité**
### 1. **Utilisation de clés SSH**
### 2. **Désactivation de root + mot de passe**

- Crée un utilisateur `ansible` ou `devops` avec droits sudo
    
- Redémarre `sshd` après modification : `sudo systemctl restart sshd`

### 3. **Firewall (UFW ou iptables)**
### 4. **HTTPS avec certificat (Let's Encrypt ou self-signed)**

- Générer un certificat auto-signé avec SSL :
    - HAProxy supporte `ssl crt` dans `bind`
