Daily Log - M1PPAW Projet 2 - Jour 1

Date : 13/04/2026
Durée : 7h
Objectif : Setup environnement + déploiement + première reconnaissance

1. Standards de Documentation

Trois types de documentation :
- Notes techniques (daily log) : tout documenter, même erreurs
- Rapport technique : pour profils techniques
- Rapport exécutif : pour décideurs (risques métier)

2. Setup Environnement

VM :
- OS : Kali Linux (VMware)
- RAM : 8GB
- CPU : 2 cores
- Network : NAT
- Snapshot : Clean_Install

Commande test réseau :
ping -c 1 8.8.8.8

3. Outils installés

sudo apt update && sudo apt upgrade -y

sudo apt install -y burpsuite sqlmap gobuster kibana elasticsearch

sudo apt install -y docker.io docker-compose
sudo systemctl enable docker --now

docker --version
docker ps

4. Déploiement


Juice Shop :
git clone https://github.com/juice-shop/juice-shop.git
docker-compose up -d
→ http://localhost:3000

crAPI :
git clone https://github.com/OWASP/crAPI.git
docker-compose up -d
→ problème RAM

VAmPI :
git clone https://github.com/erev0s/VAmPI.git
docker-compose up -d
→ http://localhost:5000

5. Test Burp

burpsuite
Proxy : 127.0.0.1:8080
→ interception OK

6. Reconnaissance

nikto -h http://localhost:3000
gobuster dir -u http://localhost:3000 -w common.txt
whatweb http://localhost:3000
curl http://localhost:5000

Découvertes :
- endpoint login
- /admin
- API accessible sans auth

7. Problèmes

- crAPI : manque RAM
- Burp : proxy non configuré

8. Next Steps

- SQLi
- XSS
- IDOR
- Analyse API

9. Checklist fin de journée

- Environnement OK
- Screenshots sauvegardés


