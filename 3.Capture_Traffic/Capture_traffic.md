# **Génération et Capture de Trafic Réseau**

## **1. Génération de Trafic Réseau avec Python**

Ce script génère différents types de trafic réseau (**TCP, UDP, ICMP**) pour analyse.

### **1.1 Code Python**

```python
import os
import threading

Guest1Zone1 = "192.168.10.1"
port = 11111

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

## **2. Capture du Trafic avec tcpdump**

La capture de trafic est réalisée avec `tcpdump` et exportée en format **pcap**.

### **2.1 Capture des paquets**

#### **2.1.1 Capture des paquets UDP et TCP**

```bash
sudo tcpdump -i ens3 port 11111 -w /tmp/netflow.pcap
```

#### **2.1.2 Capture des paquets ICMP**

```bash
sudo tcpdump -i ens3 icmp -w /tmp/icmp_traffic.pcap
```

### **2.2 Lecture des fichiers pcap**

```bash
sudo tcpdump -r /tmp/icmp_traffic.pcap
```

---

## **3. Transfert d'un Fichier via SCP (SSH)**

Le fichier **pcap** généré sur **Server1Zone1** doit être récupéré sur l'ordinateur Windows.

### **3.1 Vérification du fichier sur le serveur**

```bash
ls /tmp/
```

<img width="1189" alt="Capture d’écran 2025-03-06 à 11 46 16" src="https://github.com/user-attachments/assets/ebb35d0a-c729-4430-8f88-70fb1c499ea5" />

### **3.2 Transfert du fichier avec SCP via ProxyJump**

```bash
scp -o "ProxyJump=castoruser@cyberlab.idf.sii.fr:5037" msuser@192.168.10.252:/tmp/udptcp.pcap C:\Users\akil.elmoncer\Documents\
```

- **ProxyJump** : Utilisation de Castor comme relais SSH.
- **Chemin source** : `/tmp/udptcp.pcap` sur Server1Zone1.
- **Destination** : `C:\Users\akil.elmoncer\Documents\` sur Windows.

![fichierPcap](https://github.com/user-attachments/assets/4fdb779e-6103-47b9-9338-f00c6ab3e471)

<img width="1099" alt="Capture d’écran 2025-03-06 à 11 47 03" src="https://github.com/user-attachments/assets/546ecc37-3c00-4847-a3c9-249d1dfbf421" />
