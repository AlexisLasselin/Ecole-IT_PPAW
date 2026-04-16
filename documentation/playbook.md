# Incident Response Playbook

## Sommaire

<details>

<summary>Table des matières</summary>

- [Incident Response Playbook](#incident-response-playbook)
  - [Sommaire](#sommaire)
  - [Incident Response Playbook - SQLi Attack Detection](#incident-response-playbook---sqli-attack-detection)
    - [Metadata](#metadata)
    - [Objectifs](#objectifs)
    - [Déclencheurs](#déclencheurs)
    - [⏱Timeline SLA](#timeline-sla)
    - [Phase 1: DETECTION \& TRIAGE (0-5 min)](#phase-1-detection--triage-0-5-min)
      - [Actions Automatiques (SOAR)](#actions-automatiques-soar)
      - [Actions Analyste SOC](#actions-analyste-soc)
      - [Décision Triage](#décision-triage)
    - [Phase 2: CONTAINMENT (5-20 min)](#phase-2-containment-5-20-min)
      - [Containment Court Terme (Immediate)](#containment-court-terme-immediate)
    - [Containment Moyen Terme (si attaque avancée)](#containment-moyen-terme-si-attaque-avancée)
    - [Phase 3: INVESTIGATION (20-60 min)](#phase-3-investigation-20-60-min)
      - [Forensics BDD](#forensics-bdd)
      - [Forensics Applicative](#forensics-applicative)
    - [Phase 4: ERADICATION (1-2h)](#phase-4-eradication-1-2h)
      - [Correction Urgente](#correction-urgente)
    - [Phase 5: RECOVERY (2-4h)](#phase-5-recovery-2-4h)
    - [Phase 6: POST-INCIDENT (J+1)](#phase-6-post-incident-j1)
      - [Post-Mortem Report](#post-mortem-report)
      - [KPIs à Mesurer](#kpis-à-mesurer)
    - [References](#references)
    - [📞 Contacts](#-contacts)
  - [Incident Response Playbook - XSS Attack Detection](#incident-response-playbook---xss-attack-detection)
    - [Métadonnées](#métadonnées)
    - [Objectifs](#objectifs-1)
    - [Déclencheurs](#déclencheurs-1)
    - [Timeline SLA](#timeline-sla-1)
    - [Phase 1: DETECTION \& TRIAGE (0-5 min)](#phase-1-detection--triage-0-5-min-1)
      - [Actions Automatiques (SOAR)](#actions-automatiques-soar-1)
      - [Actions Analyste SOC](#actions-analyste-soc-1)
      - [Décision Triage](#décision-triage-1)
    - [Phase 2: CONTAINMENT (5-20 min)](#phase-2-containment-5-20-min-1)
      - [Containment Court Terme (Immediate)](#containment-court-terme-immediate-1)
      - [Containment Moyen Terme (si attaque avancée)](#containment-moyen-terme-si-attaque-avancée-1)
    - [Phase 3: INVESTIGATION (20-60 min)](#phase-3-investigation-20-60-min-1)
      - [Forensics Applicative](#forensics-applicative-1)
    - [Phase 4: ERADICATION (1-2h)](#phase-4-eradication-1-2h-1)
      - [Correction Urgente](#correction-urgente-1)
    - [Phase 5: RECOVERY (2-4h)](#phase-5-recovery-2-4h-1)
    - [Phase 6: POST-INCIDENT (J+1)](#phase-6-post-incident-j1-1)
      - [Post-Mortem Report](#post-mortem-report-1)
      - [KPIs à Mesurer](#kpis-à-mesurer-1)
    - [References](#references-1)
    - [📞 Contacts](#-contacts-1)
  - [Incident Response Playbook - Compte administrateur Compromis](#incident-response-playbook---compte-administrateur-compromis)
    - [Metadata](#metadata-1)
    - [Objectifs](#objectifs-2)
    - [Déclencheurs](#déclencheurs-2)
    - [Timeline SLA](#timeline-sla-2)
    - [Phase 1: DETECTION \& TRIAGE (0-5 min)](#phase-1-detection--triage-0-5-min-2)
      - [Actions Automatiques (SOAR)](#actions-automatiques-soar-2)
      - [Actions Analyste SOC](#actions-analyste-soc-2)
      - [Décision Triage](#décision-triage-2)
    - [Phase 2: CONTAINMENT (5-20 min)](#phase-2-containment-5-20-min-2)
      - [Containment Court Terme (Immediate)](#containment-court-terme-immediate-2)
      - [Containment Moyen Terme (si attaque avancée)](#containment-moyen-terme-si-attaque-avancée-2)
    - [Phase 3: INVESTIGATION (20-60 min)](#phase-3-investigation-20-60-min-2)
      - [Forensics Applicative](#forensics-applicative-2)
    - [Phase 4: ERADICATION (1-2h)](#phase-4-eradication-1-2h-2)
      - [Correction Urgente](#correction-urgente-2)
    - [Phase 5: RECOVERY (2-4h)](#phase-5-recovery-2-4h-2)
    - [Phase 6: POST-INCIDENT (J+1)](#phase-6-post-incident-j1-2)
      - [Post-Mortem Report](#post-mortem-report-2)
      - [KPIs à Mesurer](#kpis-à-mesurer-2)
    - [References](#references-2)
    - [📞 Contacts](#-contacts-2)

</details>

## Incident Response Playbook - SQLi Attack Detection

### Metadata

- **Playbook ID:** IRP-001
- **Incident Type:** SQL Injection Attack
- **Severity:** CRITICAL
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application)
- **Owner:** Security Operations Center
- **Last Updated:** 2026-04-16

### Objectifs

Contenir et remédier rapidement à une tentative d'injection SQL détectée sur l'application SecureShop, empêcher l'exfiltration de données et restaurer la sécurité.

### Déclencheurs

Ce playbook est activé si l'un des événements suivants se produit :

- Alerte SIEM : "SQL Injection Detected" (Rule ID: 1001-1005)
- Alerte WAF ModSecurity : Blocage SQLi (ID 920xxx, 942xxx)
- Alertes multiples (>5) en provenance de la même IP
- Anomalie détectée par UEBA : requêtes BDD inhabituelles

### ⏱Timeline SLA

- **Détection → Triage :** < 5 minutes
- **Triage → Containment :** < 15 minutes
- **Containment → Remediation :** < 2 heures
- **Remediation → Recovery :** < 4 heures
- **Recovery → Post-Mortem :** < 24 heures

### Phase 1: DETECTION & TRIAGE (0-5 min)

#### Actions Automatiques (SOAR)

- [ ] Enrichir l'IP source (GeoIP, Reputation, Shodan)
- [ ] Extraire payload complet de la requête
- [ ] Vérifier si attaque réussie (status 200) ou bloquée (status 403)
- [ ] Corréler avec autres alertes même IP/user

#### Actions Analyste SOC

1. **Ouvrir le ticket incident** (Jira/ServiceNow) - Titre: "SQLi Attack - [IP] on [endpoint]" - Severity: CRITICAL si données exfiltrées, HIGH si bloquée
2. **Analyser le payload** :`sql -- Exemple de payload à analyser ' UNION SELECT username, password, email FROM users--` - Type SQLi : Union-based / Boolean-based / Time-based ? - Cible : Tables sensibles (users, payments, orders) ?
3. **Déterminer impact potentiel** :
   - [ ] Données exfiltrées ? (vérifier logs BDD + taille réponse HTTP)
   - [ ] Modification BDD ? (INSERT/UPDATE/DELETE détecté ?)
   - [ ] RCE possible ? (xp_cmdshell, LOAD_FILE détecté ?)

#### Décision Triage

- **FAUX POSITIF** → Clore ticket, documenter, whitelister si nécessaire
- **ATTAQUE BLOQUÉE** → Continuer Phase 2 (containment préventif)
- **ATTAQUE RÉUSSIE** → ESCALADE IMMEDIATE, passer Phase 2 URGENT

### Phase 2: CONTAINMENT (5-20 min)

#### Containment Court Terme (Immediate)

1. **Bloquer l'IP source** :

   ```bash
   # Firewall iptables -I INPUT 1 -s [IP_ATTAQUANT] -j DROP
   # WAF (si pas déjà bloqué)
   echo "[IP_ATTAQUANT]" >> /etc/modsecurity/blocklist.txt
   modsec_reload
   ```

2. **Isoler la session utilisateur** (si attaquant authentifié) :

   ```sql
   -- Révoquer tous les tokens de cet utilisateur
   DELETE FROM sessions WHERE user_id = [ATTACKER_USER_ID];
   ```

3. **Activer rate limiting agressif** :

   ```nginx
   # nginx.conf
   limit_req_zone $binary_remote_addr zone=sqli_protection:10m rate=1r/s; limit_req zone=sqli_protection burst=5 nodelay;`
   ```

### Containment Moyen Terme (si attaque avancée)

1. **Mettre l'application en mode maintenance** (si exfiltration massive détectée) :

   ```bash
   # Rediriger vers page maintenance
   touch /var/www/maintenance.flag systemctl reload nginx
   ```

2. **Snapshot BDD actuelle** (avant toute modification) :

   ```bash
   mysqldump -u root -p secureshop > /backup/secureshop_incident_$(date +%Y%m%d_%H%M%S).sql
   ```

### Phase 3: INVESTIGATION (20-60 min)

#### Forensics BDD

1. **Audit logs BDD** :

   ```sql
   -- MySQL : activer et consulter general log
   SET GLOBAL general_log = 'ON'; SELECT * FROM mysql.general_log WHERE argument LIKE '%UNION%' OR argument LIKE '%SELECT%FROM%' AND event_time > NOW() - INTERVAL 1 HOUR;
   ```

2. **Identifier données compromises** :

   ```sql
   -- Vérifier accès tables sensibles
   SELECT * FROM information_schema.table_privileges WHERE grantee LIKE '%webapp_user%';
   -- Vérifier modifications récentes
   SELECT * FROM audit_log WHERE timestamp > '[HEURE_ATTAQUE]' AND (action = 'SELECT' AND table_name IN ('users', 'payments'));
   ```

#### Forensics Applicative

1. **Analyser logs applicatifs complets** :

   ```bash
   # Extraire toutes les requêtes de l'IP attaquante
   grep "[IP_ATTAQUANT]" /var/log/secureshop/access.log > /tmp/attacker_requests.log

   # Identifier toutes les tentatives SQLi (pas que celle détectée)
   grep -E "(UNION|SELECT.*FROM|sleep\(|waitfor)" /tmp/attacker_requests.log
   ```

2. **Vérifier exfiltration** :

   ```bash
   # Analyser les réponses HTTP volumineuses vers l'attaquant
   grep "[IP_ATTAQUANT]" /var/log/secureshop/access.log | awk '$10 > 10000 {print}' # Colonne $10 = bytes envoyés
   ```

### Phase 4: ERADICATION (1-2h)

#### Correction Urgente

1. **Patcher la vulnérabilité SQLi** :

   ```python
   # AVANT (vulnérable)
   query = f"SELECT * FROM products WHERE id = {request.args.get('id')}"
   # APRÈS (sécurisé)
   query = "SELECT * FROM products WHERE id = ?"
   cursor.execute(query, (request.args.get('id')))
   ```

2. **Déployer le patch** :

   ```bash
   # Test en staging
   git checkout -b hotfix/sqli-search-endpoint

   # ... appliquer patch ...
   pytest tests/security/test_sqli.py

   # Déploiement production
   git merge hotfix/sqli-search-endpoint ansible-playbook deploy_secureshop.yml --tags=hotfix
   ```

3. **Renforcer WAF** (si attaque bypass détecté) :

   ```apache
   # ModSecurity : règle plus stricte
   SecRule ARGS "@rx (?i)(union.*select|sleep\(|waitfor|xp_cmdshell)" \ "id:9001,phase:2,deny,status:403,log,msg:'SQLi Detected - Advanced Pattern'"
   ```

### Phase 5: RECOVERY (2-4h)

1. **Validation post-patch** :
   - [ ] Rejouer l'attaque initiale → doit être bloquée
   - [ ] Tests fonctionnels complets (non-régression)
   - [ ] Scan sécurité automatisé (OWASP ZAP)
2. **Restaurer services** (si mode maintenance activé) :

   ```bash
   rm /var/www/maintenance.flag
   systemctl reload nginx
   ```

3. **Notification utilisateurs** (si données compromises - RGPD) :

   ```txt
   template email :
   "Une faille de sécurité a été détectée le [DATE]. Par précaution, nous vous recommandons de changer votre mot de passe."
   ```

### Phase 6: POST-INCIDENT (J+1)

#### Post-Mortem Report

1. **Réunion post-incident** (participants : SOC, DevSecOps, RSSI)
   - Timeline détaillée de l'incident
   - Root cause analysis
   - Lessons learned
2. **Mesures préventives additionnelles** :
   - [ ] Scan sécurité hebdomadaire automatisé (ajout au CI/CD)
   - [ ] Formation dev sur requêtes préparées
   - [ ] Pentest externe sur endpoints critiques
   - [ ] SAST intégré dans pipeline (SonarQube)
3. **Mise à jour documentation** :
   - [ ] Ajouter ce cas dans la base de connaissance
   - [ ] Mettre à jour l'inventaire des actifs critiques
   - [ ] Réviser les SLA si non respectés

#### KPIs à Mesurer

- **Time to Detect (TTD) :** [ ] minutes
- **Time to Respond (TTR) :** [ ] minutes
- **Time to Remediate (TTRem) :** [ ] heures
- **Data Compromised :** [ ] records
- **Cost of Incident :** [ ]€ (downtime + remediation + notification RGPD)

### References

- OWASP SQL Injection Prevention Cheat Sheet
- MITRE ATT&CK T1190
- NIST SP 800-61 (Incident Response Guide)
- RGPD Article 33 (Notification breach < 72h)

### 📞 Contacts

- **SOC Lead :** John Doe ([john.doe@company.com](mailto:john.doe@company.com), +33 6 XX XX XX XX)
- **RSSI :** Jane Smith ([jane.smith@company.com](mailto:jane.smith@company.com), +33 6 XX XX XX XX)
- **DPO :** (si données personnelles compromises)
- **Communication/PR :** (si incident public)

**Version :** 1.0

**Prochain Review :** 2025-10-16 (tous les 6 mois)

## Incident Response Playbook - XSS Attack Detection

### Métadonnées

- **Playbook ID:** IRP-002
- **Incident Type:** Cross-Site Scripting (XSS) Attack
- **Sévérité:** ÉLEVÉE
- **MITRE ATT&CK:** T1059.007 (Command and Scripting Interpreter: JavaScript)
- **Propriétaire:** Security Operations Center
- **Dernière mise à jour:** 2026-04-16

### Objectifs

Contenir et remédier rapidement à une tentative d'attaque XSS détectée sur l'application SecureShop, empêcher l'exécution de scripts malveillants et restaurer la sécurité.

### Déclencheurs

Ce playbook est activé si l'un des événements suivants se produit :

- Alerte SIEM : "XSS Attack Detected" (Rule ID: 2001-2005)
- Alerte WAF ModSecurity : Blocage XSS (ID 920xxx, 941xxx)
- Alertes multiples (>5) en provenance / en direction de la même IP
- Anomalie détectée par UEBA : requêtes avec payloads suspects (ex: `<script>`, onerror=, etc.)
- Signalement utilisateur : "Je vois une alerte de sécurité sur votre site" (via formulaire de contact ou support)

### Timeline SLA

- **Détection → Triage :** < 5 minutes
- **Triage → Containment :** < 15 minutes
- **Containment → Remediation :** < 2 heures
- **Remediation → Recovery :** < 4 heures
- **Recovery → Post-Mortem :** < 24 heures
- **Notification RGPD (si données personnelles compromises) :** < 72 heures
- **Communication publique (si nécessaire) :** < 24 heures après décision

### Phase 1: DETECTION & TRIAGE (0-5 min)

#### Actions Automatiques (SOAR)

- [ ] Enrichir l'IP source (GeoIP, Reputation, Shodan)
- [ ] Extraire payload complet de la requête
- [ ] Vérifier si attaque réussie (status 200) ou bloquée (status 403)
- [ ] Corréler avec autres alertes même IP/user
- [ ] Vérifier si payload contient des patterns XSS connus (ex: `<script>`, onerror=, etc.)

#### Actions Analyste SOC

1. **Ouvrir le ticket incident** (Jira/ServiceNow) - Titre: "XSS Attack - [IP] on [endpoint]" - Severity: HIGH
2. **Analyser le payload** :`<script>alert('XSS')</script>` - Type XSS : Reflected / Stored / DOM-based ? - Cible : Champs de saisie (commentaires, formulaires) ?
3. **Déterminer impact potentiel** :
   - [ ] Script exécuté ? (vérifier logs applicatifs + retours utilisateurs)
   - [ ] Données compromises ? (cookies, tokens, etc.)
   - [ ] RCE possible ? (payloads avancés détectés ?)

#### Décision Triage

- **FAUX POSITIF** → Clore ticket, documenter, whitelister si nécessaire
- **ATTAQUE BLOQUÉE** → Continuer Phase 2 (containment préventif)
- **ATTAQUE RÉUSSIE** → ESCALADE IMMEDIATE, passer Phase 2 URGENT

### Phase 2: CONTAINMENT (5-20 min)

#### Containment Court Terme (Immediate)

1. **Bloquer l'IP source** :

   ```bash
   # Firewall iptables -I INPUT 1 -s [IP_ATTAQUANT] -j DROP
   # WAF (si pas déjà bloqué)
   echo "[IP_ATTAQUANT]" >> /etc/modsecurity/blocklist.txt
   modsec_reload
   ```

2. **Isoler la session utilisateur** (si attaquant authentifié) :

   ```sql
   -- Révoquer tous les tokens de cet utilisateur
   DELETE FROM sessions WHERE user_id = [ATTACKER_USER_ID];
   ```

3. **Activer rate limiting agressif** :

   ```nginx
   # nginx.conf
   limit_req_zone $binary_remote_addr zone=xss_protection:10m rate=1r/s; limit_req zone=xss_protection burst=5 nodelay;`
   ```

#### Containment Moyen Terme (si attaque avancée)

1. **Mettre l'application en mode maintenance** (si exfiltration massive détectée) :

   ```bash
   # Rediriger vers page maintenance
   touch /var/www/maintenance.flag systemctl reload nginx
   ```

2. **Snapshot BDD actuelle** (avant toute modification) :

   ```bash
   mysqldump -u root -p secureshop > /backup/secureshop_incident_$(date +%Y%m%d_%H%M%S).sql
   ```

### Phase 3: INVESTIGATION (20-60 min)

#### Forensics Applicative

1. **Analyser logs applicatifs complets** :

   ```bash
   # Extraire toutes les requêtes de l'IP attaquante
   grep "[IP_ATTAQUANT]" /var/log/secureshop/access.log > /tmp/attacker_requests.log

   # Identifier toutes les tentatives XSS (pas que celle détectée)
   grep -E "(<script>|onerror=|javascript:)" /tmp/attacker_requests.log
   ```

2. **Vérifier exfiltration** :

   ```bash
   # Analyser les réponses HTTP volumineuses vers l'attaquant
   grep "[IP_ATTAQUANT]" /var/log/secureshop/access.log | awk '$10 > 10000 {print}' # Colonne $10 = bytes envoyés
   ```

### Phase 4: ERADICATION (1-2h)

#### Correction Urgente

1. **Patcher la vulnérabilité XSS** :

   ```python
   # AVANT (vulnérable)
   comment = request.args.get('comment')
   # APRÈS (sécurisé)
   from markupsafe import escape
   comment = escape(request.args.get('comment'))
   ```

2. **Déployer le patch** :

   ```bash
   # Test en staging
   git checkout -b hotfix/xss-comment-endpoint
   # ... appliquer patch ...
   pytest tests/security/test_xss.py
   # Déploiement production
   git merge hotfix/xss-comment-endpoint ansible-playbook deploy_secureshop.yml --tags=hotfix
   ```

3. **Renforcer WAF** (si attaque bypass détecté) :

   ```apache
   # ModSecurity : règle plus stricte
   SecRule ARGS "@rx (?i)(<script>|onerror=|javascript:)" \ "id:9002,phase:2,deny,status:403,log,msg:'XSS Detected - Advanced Pattern'"
   ```

### Phase 5: RECOVERY (2-4h)

1. **Validation post-patch** :
   - [ ] Rejouer l'attaque initiale → doit être bloquée
   - [ ] Tests fonctionnels complets (non-régression)
   - [ ] Scan sécurité automatisé (OWASP ZAP)
   - [ ] Vérifier retours utilisateurs (pas d'alertes XSS visibles)
2. **Restaurer services** (si mode maintenance activé) :

   ```bash
   rm /var/www/maintenance.flag
   systemctl reload nginx
   ```

3. **Notification utilisateurs** (si données compromises - RGPD) :

   ```txt
   template email :
   "Une faille de sécurité a été détectée le [DATE]. Par précaution, nous vous recommandons de changer votre mot de passe."
   ```

### Phase 6: POST-INCIDENT (J+1)

#### Post-Mortem Report

1. **Réunion post-incident** (participants : SOC, DevSecOps, RSSI)
   - Timeline détaillée de l'incident
   - Root cause analysis
   - Lessons learned
   - Mesures préventives additionnelles
   - Mise à jour documentation

#### KPIs à Mesurer

- **Time to Detect (TTD) :** [ ] minutes
- **Time to Respond (TTR) :** [ ] minutes
- **Time to Remediate (TTRem) :** [ ] heures
- **Data compromises :** [ ] records
- **Coût de l'Incident :** [ ]€ (downtime + remediation + notification RGPD)

### References

- OWASP XSS Prevention Cheat Sheet
- MITRE ATT&CK T1059.007
- NIST SP 800-61 (Incident Response Guide)
- RGPD Article 33 (Notification breach < 72h)
- OWASP ZAP (outil de scan de vulnérabilités)
- Markupsafe (bibliothèque de sécurisation des entrées)

### 📞 Contacts

- **SOC Lead :** John Doe ([john.doe@example.com](mailto:john.doe@example.com))
- **RSSI :** Jane Smith ([jane.smith@example.com](mailto:jane.smith@example.com))
- **DPO :** (si données personnelles compromises)
- **Communication/PR :** (si incident public)

**Version :** 1.0

**Prochain Review :** 2025-10-16 (tous les 6 mois)

## Incident Response Playbook - Compte administrateur Compromis

### Metadata

- **Playbook ID:** IRP-003
- **Incident Type:** Compte Administrateur Compromis
- **Sévérité:** CRITIQUE
- **MITRE ATT&CK:** T1078 (Valid Accounts)
- **Owner:** Security Operations Center
- **Last Updated:** 2026-04-16

### Objectifs

Détecter, contenir et remédier rapidement à une compromission de compte administrateur sur l'application SecureShop, empêcher les actions malveillantes et restaurer la sécurité.

### Déclencheurs

Ce playbook est activé si l'un des événements suivants se produit :

- Alerte SIEM : "Admin Account Compromise Detected" (Rule ID: 3001-3005)
- Alertes multiples (>5) en provenance de la même IP sur des endpoints d'administration
- Anomalie détectée par UEBA : activités inhabituelles sur compte admin (ex: login depuis IP inconnue, actions non habituelles, etc.)
- Signalement utilisateur : "Je ne reconnais pas ces activités sur mon compte" (via formulaire de contact ou support)
- Détection d'une session active sur un compte admin depuis une IP suspecte
- Alertes de sécurité sur endpoints d'administration (ex: WAF, IDS)
- Tentatives de brute-force sur compte admin détectées

### Timeline SLA

- **Détection → Triage :** < 5 minutes
- **Triage → Containment :** < 15 minutes
- **Containment → Remediation :** < 2 heures
- **Remediation → Recovery :** < 4 heures
- **Recovery → Post-Mortem :** < 24 heures
- **Notification RGPD (si données personnelles compromises) :** < 72 heures
- **Communication publique (si nécessaire) :** < 24 heures après décision
- **Escalade vers direction / communication :** < 30 minutes après confirmation compromission

### Phase 1: DETECTION & TRIAGE (0-5 min)

#### Actions Automatiques (SOAR)

- [ ] Enrichir l'IP source (GeoIP, Reputation, Shodan)
- [ ] Vérifier les logs de connexion du compte admin (IP, heure, user-agent)
- [ ] Corréler avec autres alertes même IP/user
- [ ] Vérifier si des actions sensibles ont été effectuées (ex: modification de données, accès à des endpoints critiques, etc.)
- [ ] Vérifier si des sessions actives existent pour ce compte admin depuis des IP suspectes
- [ ] Vérifier si des tentatives de brute-force ont été détectées sur ce compte admin
- [ ] Vérifier si des données sensibles ont été accédées ou exfiltrées par ce compte admin
- [ ] Vérifier si des actions non habituelles ont été effectuées par ce compte admin (ex: accès à des ressources non habituelles, modifications de configurations, etc.)
- [ ] Vérifier si des alertes de sécurité ont été déclenchées sur des endpoints d'administration (ex: WAF, IDS) en lien avec ce compte admin
- [ ] Vérifier si des activités inhabituelles ont été détectées par UEBA sur ce compte admin (ex: login depuis IP inconnue, actions non habituelles, etc.)

#### Actions Analyste SOC

1. **Ouvrir le ticket incident** (Jira/ServiceNow) - Titre: "Admin Account Compromise - [Admin Username]" - Severity: CRITICAL
2. **Analyser les logs de connexion** : - IP d'origine : [IP_ATTAQUANT] - Heure de connexion : [HEURE_CONNEXION] - User-agent : [USER_AGENT]
3. **Déterminer impact potentiel** :
   - [ ] Actions sensibles effectuées ? (vérifier logs applicatifs + retours utilisateurs)
   - [ ] Données compromises ? (vérifier accès à des données sensibles)
   - [ ] Actions non habituelles ? (vérifier accès à des ressources non habituelles, modifications de configurations, etc.)

#### Décision Triage

- **FAUX POSITIF** → Clore ticket, documenter, whitelister si nécessaire
- **COMPROMISSION CONFIRMÉE** → ESCALADE IMMEDIATE, passer Phase 2 URGENT

### Phase 2: CONTAINMENT (5-20 min)

#### Containment Court Terme (Immediate)

1. **Bloquer l'IP source** :

   ```bash
   # Firewall iptables -I INPUT 1 -s [IP_ATTAQUANT] -j DROP
   # WAF (si pas déjà bloqué)
   echo "[IP_ATTAQUANT]" >> /etc/modsecurity/blocklist.txt
   modsec_reload
   ```

2. **Isoler la session utilisateur** :

   ```sql
   -- Révoquer tous les tokens de cet utilisateur
   DELETE FROM sessions WHERE user_id = [ADMIN_USER_ID];
   ```

3. **Activer rate limiting agressif** :

   ```nginx
   # nginx.conf
   limit_req_zone $binary_remote_addr zone=admin_protection:10m rate=1r/s; limit_req zone=admin_protection burst=5 nodelay;`
   ```

#### Containment Moyen Terme (si attaque avancée)

1. **Mettre l'application en mode maintenance** (si exfiltration massive détectée) :

   ```bash
   # Rediriger vers page maintenance
   touch /var/www/maintenance.flag systemctl reload nginx
   ```

2. **Snapshot BDD actuelle** (avant toute modification) :

   ```bash
   mysqldump -u root -p secureshop > /backup/secureshop_incident_$(date +%Y%m%d_%H%M%S).sql
   ```

### Phase 3: INVESTIGATION (20-60 min)

#### Forensics Applicative

1. **Analyser logs applicatifs complets** :

   ```bash
   # Extraire toutes les requêtes du compte admin compromis
   grep "user_id=[ADMIN_USER_ID]" /var/log/secureshop/access.log > /tmp/admin_activity.log

   # Identifier toutes les actions sensibles effectuées par ce compte admin
   grep -E "(DELETE|UPDATE|INSERT|CONFIG_CHANGE|ACCESS_SENSITIVE_DATA)" /tmp/admin_activity.log
   ```

2. **Vérifier exfiltration** :

   ```bash
   # Analyser les réponses HTTP volumineuses vers l'attaquant
   grep "user_id=[ADMIN_USER_ID]" /var/log/secureshop/access.log | awk '$10 > 10000 {print}' # Colonne $10 = bytes envoyés
   ```

3. **Vérifier actions non habituelles** :

   ```bash
   # Identifier accès à des ressources non habituelles, modifications de configurations, etc.
   grep -E "(ACCESS_UNUSUAL_RESOURCE|CONFIG_CHANGE)" /tmp/admin_activity.log
   ```

### Phase 4: ERADICATION (1-2h)

#### Correction Urgente

1. **Réinitialiser le mot de passe du compte admin compromis** :

   ```sql
   -- Réinitialiser le mot de passe
   UPDATE users SET password = '[NOUVEAU_MOT_DE_PASSE_HASH]' WHERE user_id = [ADMIN_USER_ID];
   ```

2. **Déployer le patch** (si vulnérabilité exploitée pour la compromission) :

   ```bash
   # Test en staging
   git checkout -b hotfix/admin-compromise
   # ... appliquer patch ...
   pytest tests/security/test_admin_compromise.py
   # Déploiement production
   git merge hotfix/admin-compromise ansible-playbook deploy_secureshop.yml --tags=hotfix
   ```

3. **Renforcer WAF** (si attaque bypass détecté) :

   ```apache
   # ModSecurity : règle plus stricte pour endpoints d'administration
   SecRule REQUEST_URI "@rx (?i)(/admin|/config|/settings)" \ "id:9003,phase:2,deny,status:403,log,msg:'Admin Endpoint Access Detected - Additional Protection'"
   ```

### Phase 5: RECOVERY (2-4h)

1. **Validation post-remediation** :
   - [ ] Rejouer les actions effectuées par le compte admin compromis → doivent être bloquées
   - [ ] Tests fonctionnels complets (non-régression)
   - [ ] Scan sécurité automatisé (OWASP ZAP)
   - [ ] Vérifier retours utilisateurs (pas d'activités suspectes visibles)
   - [ ] Vérifier que le compte admin compromis ne peut plus accéder à des ressources sensibles le temps que le patch est appliqué

2. **Restaurer services** (si mode maintenance activé) :

   ```bash
   rm /var/www/maintenance.flag
   systemctl reload nginx
   ```

3. **Notification utilisateurs** (si données compromises - RGPD) :

   ```txt
   template email :
   "Une faille de sécurité a été détectée le [DATE]. Par précaution, nous vous recommandons de changer votre mot de passe."
   ```

### Phase 6: POST-INCIDENT (J+1)

#### Post-Mortem Report

1. **Réunion post-incident** (participants : SOC, DevSecOps, RSSI)
   - Timeline détaillée de l'incident
   - Root cause analysis
   - Lessons learned
   - Mesures préventives additionnelles
   - Mise à jour documentation

#### KPIs à Mesurer

- **Time to Detect (TTD) :** [ ] minutes
- **Time to Respond (TTR) :** [ ] minutes
- **Time to Remediate (TTRem) :** [ ] heures
- **Data compromises :** [ ] records
- **Coût de l'Incident :** [ ]€ (downtime + remediation + notification RGPD)
- **Nombre d'actions sensibles effectuées par le compte admin compromis avant containment :** [ ] actions
- **Nombre d'actions non habituelles effectuées par le compte admin compromis avant containment :** [ ] actions
- **Nombre de tentatives de brute-force détectées sur le compte admin compromis :** [ ] tentatives
- **Nombre de sessions actives détectées pour le compte admin compromis depuis des IP suspectes :** [ ] sessions

### References

- OWASP Authentication Cheat Sheet
- MITRE ATT&CK T1078
- NIST SP 800-61 (Incident Response Guide)
- RGPD Article 33 (Notification breach < 72h)
- OWASP ZAP (outil de scan de vulnérabilités)
- Best practices for securing admin accounts (ex: MFA, least privilege, etc.)
- Guides de réponse à la compromission de comptes administrateurs
- Outils de détection de compromission de comptes (ex: UEBA, SIEM, etc.)
- Techniques d'attaque courantes pour compromettre des comptes administrateurs (ex: phishing, credential stuffing, etc.)
- Mesures de prévention pour éviter la compromission de comptes administrateurs (ex: MFA, surveillance renforcée, etc.)

### 📞 Contacts

- **SOC Lead :** John Doe ([john.doe@company.com](mailto:john.doe@company.com))
- **RSSI :** Jane Smith ([jane.smith@company.com](mailto:jane.smith@company.com))
- **DPO :** (si données personnelles compromises)
- **Communication/PR :** (si incident public)
- **Direction :** (si incident critique nécessitant une communication à la direction)
- **Equipe de réponse à l'incident :** (liste des membres de l'équipe de réponse à contacter en cas d'incident de compromission de compte administrateur)
- **Support technique :** (contact pour support technique en cas de besoin lors de la remédiation)
- **Equipe de développement :** (contact pour l'équipe de développement en cas de besoin lors de la remédiation)
- **Equipe de communication :** (contact pour l'équipe de communication en cas de besoin pour la communication interne ou externe)
- **Equipe juridique :** (contact pour l'équipe juridique en cas de besoin pour la gestion des aspects légaux liés à l'incident)
- **Equipe de conformité :** (contact pour l'équipe de conformité en cas de besoin pour la gestion des aspects de conformité liés à l'incident)
- **Equipe de gestion des risques :** (contact pour l'équipe de gestion des risques en cas de besoin pour l'évaluation des risques liés à l'incident)
- **Equipe de ressources humaines :** (contact pour l'équipe de ressources humaines en cas de besoin pour la gestion des aspects liés au personnel impliqué dans l'incident)

**Version :** 1.0

**Prochain Review :** 2025-10-16 (tous les 6 mois)
