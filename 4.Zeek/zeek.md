# **Installation de Zeek sur Guest1Zone1

## 1.1 : Mise à jour du système
Avant d'installer Zeek, assurez-vous que votre système est à jour :
```bash
sudo apt update && sudo apt upgrade -y
```

---

## 1.2 : Installer les dépendances requises
Zeek nécessite certaines bibliothèques et outils :
```bash
sudo apt install -y cmake make gcc g++ flex bison libpcap-dev libssl-dev python3-dev python3-pip zlib1g-dev
```

---

## 1.3 : Télécharger et compiler Zeek

J'ai téléchargé **manuellement** la version **7.0.5** de Zeek en suivant la même procédure que sur le site Medium.

### 1.3.1 Extraction et compilation de Zeek
```bash
cd Downloads
sudo tar -xzf zeek-7.0.5.tar.gz
cd zeek-7.0.5
```

### 1.3.2 Configuration et installation
```bash
sudo ./configure
sudo make -j$(nproc)
sudo make install
```

---

## 1.3.3 Configurer les variables d'environnement
Ajoutez Zeek à votre **PATH** :
```bash
nano ~/.bashrc
```
Ajoutez cette ligne à la fin du fichier :
```bash
export PATH=/usr/local/zeek/bin:$PATH
```
Puis appliquez la modification :
```bash
source ~/.bashrc
```
Vérifiez l'installation de Zeek :
```bash
which zeek
zeek --version
```

---

## 1.3.4 Configuration et première exécution de Zeek

**Se rendre dans le dossier de configuration de Zeek** :
```bash
cd /usr/local/zeek/etc
ls
```

**Modifier l'interface réseau** :
```bash
nano node.cfg
```
Remplacez `interface=eth0` par l'nterface réseau `ens3`. Vous pouvez la trouver avec :
```bash
ip a
```

**Vérifier la configuration initiale de Zeek** :
```bash
zeekctl check
```
Si des erreurs de permissions apparaissent, corrigez-les :
```bash
sudo chown -R $USER:$USER /usr/local/zeek/spool
sudo chmod -R u+rwX /usr/local/zeek/spool
```
Puis relancez :
```bash
zeekctl check
```

 **Appliquer les permissions pour Zeek** :
```bash
sudo setcap cap_net_raw,cap_net_admin=eip /usr/local/zeek/bin/zeek
```

 **Déployer Zeek** :
```bash
zeekctl deploy
```

 **Vérifier l'état** :
```bash
zeekctl status
```

---

## Résolution des problèmes
- **"pcap_error: Operation not permitted"** → Exécutez `sudo setcap cap_net_raw,cap_net_admin=eip /usr/local/zeek/bin/zeek`
- **"zeek terminated immediately"** → Vérifiez l'interface réseau et redémarrez avec `zeekctl deploy`
- **Permissions refusées** → Essayez d'exécuter Zeek avec `sudo zeekctl deploy`
- **Erreur de symlink dans `/usr/local/zeek/spool`** → Corrigez avec :
```bash
sudo chown -R $USER:$USER /usr/local/zeek/spool
sudo chmod -R u+rwX /usr/local/zeek/spool
```

---

## 1.4 Capture de trafic avec Zeek
Testez la capture de trafic :
```bash
zeek -i ens3
```

Les logs seront générés dans `/usr/local/zeek/logs/current/`.


### Source
Ce guide suit la procédure décrite sur : [Medium - Zeek Installation on Ubuntu](https://medium.com/@cybertoolguardian/zeek-installation-in-ubuntu-60835ee3e42c)
