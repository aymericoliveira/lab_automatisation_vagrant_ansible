# 📘 Commandes Vagrant les plus utilisées

Ce fichier recense les **commandes Vagrant les plus utilisées** pour gérer les machines virtuelles rapidement et efficacement dans un environnement de développement.


## 🔧 Commandes principales


| **Commande** | **Description** |
| --- | --- |
| `vagrant init` | 📁 Initialise un nouveau projet Vagrant en créant un fichier `Vagrantfile`. |
| `vagrant up` | 🚀 Démarre et provisionne toutes les VMs définies dans le `Vagrantfile`. |
| `vagrant up <nom>` | ⚙️ Démarre uniquement la VM spécifiée. |
| `vagrant halt` | 🛑 Éteint proprement toutes les VMs en cours d'exécution. |
| `vagrant halt <nom>` | 🛑 Éteint une seule VM. |
| `vagrant reload` | 🔁 Redémarre les VMs et applique les changements du `Vagrantfile`. |
| `vagrant reload <nom>` | 🔁 Redémarre une seule VM et recharge sa config. |
| `vagrant destroy` | 💥 Supprime toutes les VMs et libère l’espace disque. |
| `vagrant destroy <nom>` | 💥 Supprime uniquement la VM indiquée. |
| `vagrant ssh` | 🔐 Se connecte en SSH à la VM principale (ou à la première définie). |
| `vagrant ssh <nom>` | 🔐 Se connecte à une VM spécifique. |
| `vagrant status` | 📊 Affiche l’état actuel des VMs définies dans le projet. |
| `vagrant box list` | 📦 Liste toutes les box Vagrant installées localement. |
| `vagrant box add <box>` | ⬇️ Télécharge une nouvelle box depuis Vagrant Cloud. |
| `vagrant global-status` | 🌍 Affiche toutes les VMs Vagrant actives sur ta machine. |
| `vagrant provision` | 📜 Relance uniquement les scripts de provisioning (Ansible, Shell, etc.). |
provisioning (ex: Ansible).            |


## 💡 Astuces utiles

- Tu peux combiner certaines commandes :
  ```bash
  vagrant reload --provision
🔄 Redémarre la VM et relance le provisioning (utile après modif d’un playbook).

Pour forcer la re-synchronisation des dossiers partagés :

```bash
vagrant rsync
```
Pour afficher les logs en cas de bug :
```bash
vagrant up --debug
```
Pour nettoyer complètement l’environnement :
```bash
vagrant destroy -f && vagrant up
```