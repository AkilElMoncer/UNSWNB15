# **Configuration SSH**

## **1 Configuration du Relais SSH pour l'Accès Distant**

Pour faciliter l'accès aux machines de la topologie depuis une machine Windows, nous avons mis en place un relais SSH avec **Castor (192.168.1.230)** connecté via MikroTik5.

### **1.1 Génération de la Clé SSH**

Sur la machine **Guest1Zone3**, nous avons généré une clé SSH avec la commande suivante :

```bash
ssh-keygen
```

Cela génère une clé publique stockée dans `~/.ssh/id_rsa.pub`.

### **1.2. Installation et Configuration de VS Code**

1. Sur la machine Windows, nous avons installé **VS Code** ainsi que l'extension **Remote - SSH**.
2. Dans VS Code, nous avons configuré un fichier `config` dans `~/.ssh/config` pour définir les paramètres de connexion.

![ssh_config](https://github.com/user-attachments/assets/5126c665-f936-4fa6-8282-6517a386cc97)

### **1.3. Connexion à la Machine Distante**

1. Dans VS Code, nous avons utilisé l'option **Connect to Host** > **Guest1Zone3**.

   ![ssh_image1](https://github.com/user-attachments/assets/554c45bd-b263-4d99-8cbf-7bd145c836c3)
   ![ssh_image2](https://github.com/user-attachments/assets/a71ffbb5-0696-4cfb-8841-68964ca24de7)

2. Lors de la connexion, il a été demandé de saisir le mot de passe de **Castor**, puis celui de **Guest1Zone3**.

   ![ssh_image3](https://github.com/user-attachments/assets/5b345eb1-ffcf-498f-910c-8d43d1aec8ee)
   ![ssh_image4](https://github.com/user-attachments/assets/1ddd9a79-8086-447c-8374-88f2c1945378)

3. Une fois l'authentification réussie, la connexion a été établie, permettant un accès distant via **VS Code Server**.

   ![ssh_image5](https://github.com/user-attachments/assets/c0ca7538-1b43-48ff-93e4-4215ad9a1869)



## **2 Accès Distant SSH depuis un PC Windows**

Nous utilisons un relais SSH (**Castor**) pour accéder au serveur cible.

### **2.1 Connexion au relais SSH**

```bash
ssh castoruser@cyberlab.idf.sii.fr -p 5037
```

![relais-ssh](https://github.com/user-attachments/assets/3924b754-7025-4bcb-9e1b-949d3fb34227)

### **2.2 Connexion au serveur cible via Castor**

```bash
ssh msuser@192.168.10.252
```

Après authentification, nous pouvons exécuter des commandes à distance.

![ssh_castor](https://github.com/user-attachments/assets/606579b4-c140-4ea9-9656-cda62aec814c)

---


