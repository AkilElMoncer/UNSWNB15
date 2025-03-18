## **1. Création de la Topologie Réseau**

La topologie comprend :
- **MikroTik1, MikroTik2, MikroTik3, MikroTik4, MikroTik5** : Routeurs interconnectés.
- **pfSense** : Pare-feu assurant la sécurité et la gestion du trafic.
- **Serveurs et PC Linux** : Situés dans différentes zones de réseau (violette, bleue, orange, blanche).
- **NAT** : Accès à Internet pour certains segments.
- **Relais SSH** : Accès à distance grâce à vscode server en ssh via mon pc Windows

Voici une vue d'ensemble de la **topologie GNS3 mise en place** :

![topologie](https://github.com/user-attachments/assets/eb013feb-0eec-439c-a7e5-ce030d5f6064)

---

## **2. Configuration des MikroTik et du Routage**
Chaque MikroTik a été configuré avec des adresses IP et des routes statiques pour assurer la communication entre toutes les zones. Voici les configurations principales :

### **2.1 MikroTik1**
#### **Adresses IP**

![mikro1_address](https://github.com/user-attachments/assets/a8a99c98-2388-4a0e-b913-fb1cb5abe0d5)

#### **Routes configurées**

![mikro1](https://github.com/user-attachments/assets/69ccf6f1-619e-4acf-94f7-1923e5ef7867)

#### **Configuration NAT**
Pour permettre l'accès à Internet depuis le réseau interne, nous avons ajouté une règle NAT :
```bash
/ip firewall nat add chain=srcnat out_interface=ether2 action=masquerade
```
Cette règle permet de masquer l'adresse IP source des paquets sortants via **ether2**.

### **2.2 MikroTik2**
#### **Adresses IP**

![mikro2_address](https://github.com/user-attachments/assets/53fb5c87-4120-42b5-a1a5-da9c438b5bdf)

#### **Routes configurées**

![mikro2](https://github.com/user-attachments/assets/13ee64a4-cfaf-42b1-a796-f26317135d01)

### **2.3 MikroTik3**
#### **Adresses IP**

![mikro3_address](https://github.com/user-attachments/assets/72a94c1f-0227-4eec-90ee-9e02d7298a08)

#### **Routes configurées**
![mikro3](https://github.com/user-attachments/assets/138c45f8-0ca9-455d-beb1-262e1b36de95)

#### **Ajout d'une route par défaut**
Pour assurer l'accès à Internet depuis MikroTik3, nous avons ajouté une route par défaut :
```bash
/ip route add dst-address=0.0.0.0/0 gateway=10.40.85.1
```
Cela permet d'acheminer tout le trafic non local vers la passerelle 10.40.85.1.

### **2.4 MikroTik4**
#### **Adresses IP**
![mikro4_address](https://github.com/user-attachments/assets/883960bc-06d4-4409-9f0c-7fbd1ed8d7c8)

#### **Routes configurées**
![mikro4](https://github.com/user-attachments/assets/3c3d721f-04fa-4896-b589-f535a6c3627f)

### **2.5 Configuration des Switchs MikroTik**

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

## **3. Configuration du parefeu (pfSense)**

pfSense est utilisé comme pare-feu et routeur central pour répartir le trafic entre les MikroTik.

Nous avons ajouté plusieurs interfaces à pfSense pour gérer différentes zones du réseau :

![pfsense_patte](https://github.com/user-attachments/assets/c034fad6-0afc-4900-9d18-7e6fcd4b8278)

![pfsense](https://github.com/user-attachments/assets/8db129a4-78d2-468e-9e45-1b47ca0ac9f3)

Des routes statiques ont été ajoutées pour **assurer la connectivité entre les différents segments de réseau**.

![pfsense_route](https://github.com/user-attachments/assets/ce92bd56-e95e-4a5a-9a9d-36e41082fbcd)

