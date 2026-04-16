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
3. **Déterminer impact potentiel** : - [ ] Données exfiltrées ? (vérifier logs BDD + taille réponse HTTP) - [ ] Modification BDD ? (INSERT/UPDATE/DELETE détecté ?) - [ ] RCE possible ? (xp_cmdshell, LOAD_FILE détecté ?)

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
3. **Déterminer impact potentiel** : - [ ] Script exécuté ? (vérifier logs applicatifs + retours utilisateurs) - [ ] Données compromises ? (cookies, tokens, etc.) - [ ] RCE possible ? (payloads avancés détectés ?)

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
