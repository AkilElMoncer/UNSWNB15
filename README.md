# **Configuration de la Topologie Réseau sous GNS3 avec MikroTik et Entraînement d'un IDS**

## **1. Introduction**
Ce document décrit la mise en place d'une **topologie réseau sous GNS3** en utilisant des **routeurs MikroTik**, un **pare-feu pfSense**, ainsi que plusieurs serveurs et postes clients Linux.

L'objectif principal de cette topologie est de **simuler une infrastructure réseau vulnérable** pour tester un **IDS (Intrusion Detection System)** en envoyant du trafic normal et malveillant. Un script Python est utilisé pour générer du trafic ICMP, TCP et UDP, et ce trafic est capturé par **tcpdump** et **Wireshark**, puis envoyé à un IDS (**Zeek**).

Initialement, la topologie était construite avec **des routeurs Cisco**, mais a été remplacée par des **routeurs MikroTik** pour une meilleure flexibilité et une gestion simplifiée du routage et des fonctionnalités avancées.

## **2. Création de la Topologie Réseau**

La topologie comprend :
- **MikroTik1, MikroTik2, MikroTik3, MikroTik4, MikroTik5** : Routeurs interconnectés.
- **pfSense** : Pare-feu assurant la sécurité et la gestion du trafic.
- **Serveurs et PC Linux** : Situés dans différentes zones de réseau (violette, bleue, orange, blanche).
- **NAT** : Accès à Internet pour certains segments.
- **Relais SSH** : Accès à distance grâce a vscode server en ssh via mon pc Windows 

Voici une vue d'ensemble de la **topologie GNS3 mise en place** :

![topologie](https://github.com/user-attachments/assets/eb013feb-0eec-439c-a7e5-ce030d5f6064)


---

## **3. Configuration des MikroTik et du Routage**
Chaque MikroTik a été configuré avec des adresses IP et des routes statiques pour assurer la communication entre toutes les zones. Voici les configurations principales :

### **3.1 MikroTik1**
#### **Adresses IP**

![mikro1_address](https://github.com/user-attachments/assets/a8a99c98-2388-4a0e-b913-fb1cb5abe0d5)


#### **Routes configurées**

![mikro1](https://github.com/user-attachments/assets/69ccf6f1-619e-4acf-94f7-1923e5ef7867)

### **3.2 MikroTik2**
#### **Adresses IP**

![mikro2_address](https://github.com/user-attachments/assets/53fb5c87-4120-42b5-a1a5-da9c438b5bdf)

#### **Routes configurées**

![mikro2](https://github.com/user-attachments/assets/13ee64a4-cfaf-42b1-a796-f26317135d01)

### **3.3 MikroTik3**
#### **Adresses IP**

![mikro3_address](https://github.com/user-attachments/assets/72a94c1f-0227-4eec-90ee-9e02d7298a08)

#### **Routes configurées**
![mikro3](https://github.com/user-attachments/assets/138c45f8-0ca9-455d-beb1-262e1b36de95)

### **3.4 MikroTik4**
#### **Adresses IP**
![mikro4_address](https://github.com/user-attachments/assets/883960bc-06d4-4409-9f0c-7fbd1ed8d7c8)

#### **Routes configurées**
![mikro4](https://github.com/user-attachments/assets/3c3d721f-04fa-4896-b589-f535a6c3627f)

### **3.5 Configuration des Switchs MikroTik**

Dans cette partie, nous avons installé deux MikroTik configurés en mode switch entre **MikroTik2 et MikroTik4**, ainsi qu'entre **MikroTik1 et MikroTik3**. Ces MikroTik agissent comme des commutateurs (switchs) transparents en regroupant leurs interfaces dans un **bridge**.

**Mise en place du mode Bridge sur MikroTik**

Pour configurer ces MikroTik en mode switch, nous avons créé un **bridge** et ajouté les interfaces réseau concernées :

```bash
/interface bridge add name=bridge1
/interface bridge port add bridge=bridge1 interface=ether1
/interface bridge port add bridge=bridge1 interface=ether2
/interface bridge print
```

<img width="1179" alt="Capture d’écran 2025-03-07 à 15 47 02" src="https://github.com/user-attachments/assets/581a8334-c6d4-4419-a907-4d3163e5974e" />


`interface bridge add name=bridge1` : Crée un bridge nommé **bridge1**.

`interface bridge port add bridge=bridge1 interface=ether1` : Ajoute **ether1** au bridge.

`interface bridge port add bridge=bridge1 interface=ether2` : Ajoute **ether2** au bridge.

`interface bridge print` : Affiche la configuration des bridges pour vérifier la bonne prise en compte des interfaces.

Grâce à cette configuration, les MikroTik concernés agissent comme des switchs, permettant une communication fluide entre les équipements connectés aux interfaces concernées.


## **4. Configuration du parefeu (pfSense)**

pfSense est utilisé comme pare-feu et routeur central pour répartir le trafic entre les MikroTik.

Nous avons ajouté plusieurs interfaces à pfSense pour gérer différentes zones du réseau :

![pfsense_patte](https://github.com/user-attachments/assets/c034fad6-0afc-4900-9d18-7e6fcd4b8278)


![pfsense](https://github.com/user-attachments/assets/8db129a4-78d2-468e-9e45-1b47ca0ac9f3)


Des routes statiques ont été ajoutées pour **assurer la connectivité entre les différents segments de réseau**.

![pfsense_route](https://github.com/user-attachments/assets/ce92bd56-e95e-4a5a-9a9d-36e41082fbcd)

## ** 5. Configuration du Relais SSH pour l'accès distant

Pour faciliter l’accès aux machines de la topologie depuis une machine Windows, nous avons mis en place un relais SSH avec **Castor (192.168.1.230)** connecté via MikroTik5.

### **Configuration SSH**

1. Sur la machine `Guest1Zone3`, nous avons généré une clé SSH avec :
   ```bash
   ssh-keygen
   ```
   Cela génère une clé publique stockée dans `~/.ssh/id_rsa.pub`.

2. Sur la machine Windows, nous avons installé **VS Code** et l’extension **Remote - SSH**.

3. Dans **VS Code**, nous avons configuré un fichier `config` dans `~/.ssh/config` :

![shh_config](https://github.com/user-attachments/assets/5126c665-f936-4fa6-8282-6517a386cc97)

4. Dans VS Code, nous avons ensuite lancé **Connect to host** > **Guest1Zone3**.

![ssh_image1](https://github.com/user-attachments/assets/554c45bd-b263-4d99-8cbf-7bd145c836c3)
![ssh_image2](https://github.com/user-attachments/assets/a71ffbb5-0696-4cfb-8841-68964ca24de7)

6. Lors de la connexion, il a été demandé de saisir le mot de passe de **Castor** puis celui de **Guest1Zone3**.

![ssh_image3](https://github.com/user-attachments/assets/5b345eb1-ffcf-498f-910c-8d43d1aec8ee)
![ssh_image4](https://github.com/user-attachments/assets/1ddd9a79-8086-447c-8374-88f2c1945378)

8. Une fois authentifié, la connexion s'est établie et permet d’accéder à la machine distante via VS Code Server.

![ssh_image5](https://github.com/user-attachments/assets/c0ca7538-1b43-48ff-93e4-4215ad9a1869)


## **6 Script Python pour Générer du Trafic**
```python
import os
import time
import threading

Guest1Zone1 = "192.168.10.1"
port = 11111
NUM_PING = 10

action = input("Choisir une action (udp/icmp/tcp/udp+tcp) : ")

def envoie_tcp():
    print(f"Envoi de paquets TCP vers {Guest1Zone1}")
    while True:
        os.system(f"sudo nping --tcp -p {port} {Guest1Zone1}")

def envoie_udp():
    print(f"Envoi de paquets UDP vers {Guest1Zone1}")
    while True:
        os.system(f"sudo hping3 --udp -p {port} -c 10 -d 0 {Guest1Zone1}")

def envoie_icmp():
    print(f"Envoi de paquets ICMP vers {Guest1Zone1}")
    while True:
        os.system(f"ping -c 1 {Guest1Zone1} > /dev/null 2>&1 &")

if action == "udp":
    envoie_udp()
elif action == "icmp":
    envoie_icmp()
elif action == "tcp":
    envoie_tcp()
elif action == "udp+tcp":
    print(f"Envoi de paquets TCP et UDP vers {Guest1Zone1}")
    udp_thread = threading.Thread(target=envoie_udp)
    tcp_thread = threading.Thread(target=envoie_tcp)
    udp_thread.start()
    tcp_thread.start()
    udp_thread.join()
    tcp_thread.join()
else:
    print("Action non reconnue, veuillez réessayer")
```
---

### **6.1 Capture du Trafic avec tcpdump**
Pour capturer les paquets et les convertir en fichiers **pcap** utilisables par l'IDS, on utilise **tcpdump** :

```bash
sudo tcpdump -i ens3 port 11111 -w /tmp/netflow.pcap
```

Ou pour filtrer les paquets ICMP :
```bash
sudo tcpdump -i ens3 icmp -w /tmp/icmp_traffic.pcap
```

Pour lire les fichier pcap :
```bash
sudo tcpdump -r /tmp/icmp_traffic.pcap
```
---

### **6.2 Accès distant SSH via le terminal Windows (PC hôte)**

Pour établir une connexion à distance depuis le terminal Windows, nous avons utilisé le **relais SSH "Castor"** comme point intermédiaire pour accéder à notre serveur.

#### **6.2.1 Connexion au relais SSH**
Tout d'abord, nous nous sommes connectés au relais SSH **Castor** en utilisant la commande suivante :

```bash
ssh castoruser@cyberlab.idf.sii.fr -p 5037
```

Une fois le mot de passe saisi, la connexion avec **Castor** est établie avec succès :

![relais-ssh](https://github.com/user-attachments/assets/3924b754-7025-4bcb-9e1b-949d3fb34227)

#### **6.2.2 Connexion au serveur cible via Castor**
Après être connecté à Castor, nous avons ensuite établi une connexion SSH vers **Server1Zone1 (192.168.10.252)** :

```bash
ssh msuser@192.168.10.252
```

L’authentification se fait en entrant le mot de passe correspondant à l'utilisateur `msuser`. Une fois validée, la connexion au serveur est établie :

![ssh_castor](https://github.com/user-attachments/assets/606579b4-c140-4ea9-9656-cda62aec814c)

Nous sommes maintenant connectés à **Server1Zone1** et pouvons exécuter des commandes à distance.

---

### **6.3 Téléchargement d'un fichier via SSH (SCP)**

L'objectif est de récupérer sur l'ordinateur Windows le fichier **pcap** généré par **tcpdump** sur **Server1Zone1**.

#### **6.3.1 Vérification du fichier sur le serveur**
Sur **Server1Zone1**, nous avons identifié que le fichier **udptcp.pcap** se trouve dans le répertoire `/tmp/` :

```bash
ls /tmp/
```

Voici le résultat de la commande, confirmant la présence du fichier :

<img width="1189" alt="Capture d’écran 2025-03-06 à 11 46 16" src="https://github.com/user-attachments/assets/ebb35d0a-c729-4430-8f88-70fb1c499ea5" />

#### **6.3.2 Téléchargement du fichier avec SCP via un ProxyJump**
Étant donné que **Server1Zone1** n'est pas directement accessible depuis notre PC Windows, nous devons passer par le relais SSH **Castor** pour récupérer le fichier avec **SCP**.

La commande à exécuter sur le terminal Windows est la suivante :

```bash
scp -o "ProxyJump=castoruser@cyberlab.idf.sii.fr:5037" msuser@192.168.10.252:/tmp/udptcp.pcap C:\Users\akil.elmoncer\Documents\
```

- **`-o "ProxyJump=castoruser@cyberlab.idf.sii.fr:5037"`** : Utilisation de **Castor** comme point de relais SSH.
- **`msuser@192.168.10.252:/tmp/udptcp.pcap`** : Chemin du fichier source sur **Server1Zone1**.
- **`C:\Users\akil.elmoncer\Documents\`** : Destination du fichier sur le PC Windows.

Une fois la commande validée, le fichier est transféré et disponible sur notre bureau sous Windows.

![fichierPcap](https://github.com/user-attachments/assets/4fdb779e-6103-47b9-9338-f00c6ab3e471)

<img width="1099" alt="Capture d’écran 2025-03-06 à 11 47 03" src="https://github.com/user-attachments/assets/546ecc37-3c00-4847-a3c9-249d1dfbf421" />

Nous avons maintenant **récupéré avec succès le fichier pcap**, qui pourra être analysé avec **Wireshark** ou **Zeek** pour examiner le trafic réseau capturé. 


## **7 Installation de Zeek sur Guest1Zone1

### 7.1 : Mise à jour du système
Avant d'installer Zeek, assurez-vous que votre système est à jour :
```bash
sudo apt update && sudo apt upgrade -y
```

---

### 7.2 : Installer les dépendances requises
Zeek nécessite certaines bibliothèques et outils :
```bash
sudo apt install -y cmake make gcc g++ flex bison libpcap-dev libssl-dev python3-dev python3-pip zlib1g-dev
```

---

### 7.3 : Télécharger et compiler Zeek

J'ai téléchargé **manuellement** la version **7.0.5** de Zeek en suivant la même procédure que sur le site Medium.

#### 7.3.1 Extraction et compilation de Zeek
```bash
cd Downloads
sudo tar -xzf zeek-7.0.5.tar.gz
cd zeek-7.0.5
```

#### 7.3.2 Configuration et installation
```bash
sudo ./configure
sudo make -j$(nproc)
sudo make install
```

---

#### 7.3.3 Configurer les variables d'environnement
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

#### 7.3.4 Configuration et première exécution de Zeek

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

### Résolution des problèmes
- **"pcap_error: Operation not permitted"** → Exécutez `sudo setcap cap_net_raw,cap_net_admin=eip /usr/local/zeek/bin/zeek`
- **"zeek terminated immediately"** → Vérifiez l'interface réseau et redémarrez avec `zeekctl deploy`
- **Permissions refusées** → Essayez d'exécuter Zeek avec `sudo zeekctl deploy`
- **Erreur de symlink dans `/usr/local/zeek/spool`** → Corrigez avec :
```bash
sudo chown -R $USER:$USER /usr/local/zeek/spool
sudo chmod -R u+rwX /usr/local/zeek/spool
```

---

### 7.4 Capture de trafic avec Zeek
Testez la capture de trafic :
```bash
zeek -i ens3
```

Les logs seront générés dans `/usr/local/zeek/logs/current/`.


### Source
Ce guide suit la procédure décrite sur : [Medium - Zeek Installation on Ubuntu](https://medium.com/@cybertoolguardian/zeek-installation-in-ubuntu-60835ee3e42c)

## 8 Scénarios d'attaque sur la UNSWNB15

### 8.1 Attaque MITM avec ARP Spoofing


- **Attaquant** : `192.168.30.3` (Guest1Zone3)
- **Victime** : `192.168.30.1` ou `192.168.30.2`
- **Passerelle** : `192.168.30.254` (Mikrotik3)
- **Interface** : `ens3`

---

#### 8.1.1 Installation des outils
Avant de commencer, installe les outils nécessaires sur l'attaquant.

```bash
sudo apt update && sudo apt install dsniff -y
```

**Ce package contient :**  
✔ `arpspoof` → Pour l’attaque MITM.  
✔ `dsniff` → Pour capturer les mots de passe en clair.  
✔ `tcpkill` → Pour stopper une connexion TCP.  
✔ `filesnarf` → Pour intercepter des fichiers.  

Vérifie que `arpspoof` est bien installé :
```bash
which arpspoof
```

---

#### 8.1.2 Activation du routage IP sur l’attaquant
Si on ne fait pas ça, la victime **perdra sa connexion Internet** après l’attaque.

```bash
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
```
**Vérifie que c’est activé** :
```bash
cat /proc/sys/net/ipv4/ip_forward
```
 **Si ça renvoie `1`, c’est bon !** 

---

#### 8.1.3 Lancer l’attaque ARP Spoofing

##### 8.1.3.1 Empoisonner la table ARP de la victime (`192.168.30.1`)
```bash
sudo arpspoof -i ens3 -t 192.168.30.1 -r 192.168.30.254
```
**Effet** : La victime enverra tout son trafic vers l'attaquant.

##### 8.1.3.2 Empoisonner la table ARP de la passerelle (`192.168.30.254`)
```bash
sudo arpspoof -i ens3 -t 192.168.30.254 -r 192.168.30.1
```
 **Effet** : La passerelle enverra aussi le trafic de la victime vers l'attaquant.

 **À ce stade, l’attaquant est en plein MITM !** 

---

#### 8.1.4 Intercepter le trafic réseau

#### 8.1.4.1 Avec Wireshark
1. Ouvre **Wireshark** au pied de l'attaquant (192.168.30.3) :
```bash
sudo wireshark
```
2. Ensuite, avec un ordinateur, ping l'ordinateur de la victime (192.168.30.1)
3. Tu est cencé revecoir sur wireshark toute les donnée envoyer au 192.168.30.1

---

### **8.2 Attaque en brute force via SSH**

Dans notre topologie, nous souhaitons attaquer un ordinateur (**Guest1Zone2**) et prendre le contrôle de son terminal.

En effectuant un scan **Nmap** sur son adresse IP (**192.168.20.1**), nous avons constaté que seul le **port 22** (SSH) est ouvert :

![scan_port_zone2](https://github.com/user-attachments/assets/183d902a-b945-40e7-9f30-42c506de7064)

Ainsi, la meilleure option est d’infiltrer le terminal via **SSH**. Mais comment faire sans connaître son mot de passe ?

#### **8.2.1 : Création d’une liste de mots de passe**
Nous avons d’abord créé un fichier texte contenant plusieurs mots de passe courants, que nous avons nommé **hydra.txt** :

![hydra_txt](https://github.com/user-attachments/assets/a0e5b560-1545-4999-98b2-a1402850645f)

#### **8.2.2 : Brute force avec Hydra**
Ensuite, nous avons utilisé **Hydra** pour tester ces mots de passe et forcer l’accès au compte SSH avec la commande suivante :

![hydra_bf](https://github.com/user-attachments/assets/177386d6-143e-46df-8cb1-4fd84fe09a8e)

Nous avons ainsi découvert que le mot de passe de **Guest1Zone2** est **"root"**.

#### **8.2.3 : Connexion en SSH**
Une fois le mot de passe récupéré, nous avons pu accéder au terminal distant via la commande SSH suivante :

<img width="815" alt="Capture d’écran 2025-03-06 à 16 19 59" src="https://github.com/user-attachments/assets/fdb8cc6e-fd94-4fcf-928e-fadaa6673726" />
