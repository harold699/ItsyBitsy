# ItsyBitsy
Mettez en commun vos connaissances en matière d’ELK et enquêtez sur un incident.
<img width="1334" height="271" alt="image" src="https://github.com/user-attachments/assets/aea09824-dc67-4ce0-ba34-57e9e4f03573" />

# Investigation SOC — Journaux de connexion (TryHackMe)

> **Scénario (à conserver tel quel dans ton repo)**

> Lors d’une surveillance normale du SOC, l’analyste John a observé une alerte sur une solution IDS indiquant une communication C2 potentielle d’un utilisateur **Browne** du service **RH**. Un fichier suspect contenant un motif malveillant **THM:{ ________ }** a été consulté. Des journaux de connexion HTTP d’une semaine ont été extraits pour enquête. En raison de ressources limitées, seuls les journaux de connexion ont pu être extraits et ingérés dans l’index `connection_logs` dans Kibana.
>
> Dans cette salle, nous allons devoir examiner les journaux de connexion réseau de cet utilisateur, trouver le lien et le contenu du fichier, et répondre aux questions.

---
## Questions et sections à remplir

### 1) Combien d’événements ont été retournés pour le mois de **mars 2022** ?

* **Format de réponse attendu :** `****` (nombre)

* **Réponse :*1482*

* **Preuve (capture d'écran, requête) :**
* Ce qu’on déduit

Même sans champ “user”, plusieurs signaux sont hautement suspects :
* <img width="1354" height="555" alt="image" src="https://github.com/user-attachments/assets/78558912-8cc7-4a36-99ba-07d93469df7a" />
<img width="1273" height="166" alt="image" src="https://github.com/user-attachments/assets/65601249-b84c-4ade-9f40-09c1a9638ce3" />


---

### 2) Quelle est l’adresse IP associée à l’utilisateur suspect dans les journaux ?

* **Format de réponse :** `***.***.**.**`

* **Réponse :*192.166.65.54*

* **Preuve (capture d'écran, requête) :**
<img width="1358" height="546" alt="image" src="https://github.com/user-attachments/assets/d9982815-332a-409e-b199-9c19d419e9de" />
<img width="1258" height="86" alt="image" src="https://github.com/user-attachments/assets/f13bc3db-9f84-4f62-ad21-d09bdcbf72b9" />
Élément	Explication
🕓 Date : mars 10, 2022	Cohérente avec la période d’alerte signalée (mars 2022).
🔢 source_ip : 192.166.65.54	C’est la machine qui initie la connexion suspecte — donc potentiellement celle de Browne.
---

### 3) Quel est le nom du binaire Windows légitime utilisé pour télécharger un fichier depuis le serveur C2 ?

* **Format de réponse :** `*********` (nom du binaire)

* **Réponse :*bitsadmin*

* **Preuve:**
Élément	Explication
🧠 User-Agent : **bitsadmin **	bitsadmin est un outil Windows légitime, mais très souvent détourné par des malwares pour télécharger des payloads (C2, scripts malveillants, etc.). Rare en usage normal.
<img width="1353" height="480" alt="image" src="https://github.com/user-attachments/assets/5309e80c-661e-4087-a222-610b8b22836a" />
<img width="1276" height="108" alt="image" src="https://github.com/user-attachments/assets/b55692e0-1f86-4bef-8643-e892003d4a15" />

---

### 4) Quel est le site de partage de fichiers (nom de domaine) utilisé comme serveur C2 ?

* **Format de réponse :** `********.***`

* **Réponse :*pastebin.com*

* **Preuve (capture d'écran, requête) :**
Élément	Explication
🌐 Domaine : 	Pastebin est souvent utilisé pour héberger des payloads ou scripts encodés par des attaquants (C2 par “paste dropper”).
<img width="1268" height="326" alt="image" src="https://github.com/user-attachments/assets/3a7cda45-e634-4486-b30c-fa08ed37b7ba" />
<img width="1280" height="146" alt="image" src="https://github.com/user-attachments/assets/84fc1c12-5066-4c10-b372-00f968136884" />

---

### 5) Quelle est l’URL complète du C2 auquel l’hôte infecté est connecté ?

* **Format de réponse :** `********.***/********`

* **Réponse :*pastebin.com/yTg0Ah6a*

* **Preuve (capture d'écran, requête) :**
* <img width="1109" height="233" alt="image" src="https://github.com/user-attachments/assets/ad60a94b-2a04-4f85-9885-d7534a610323" />
<img width="1354" height="498" alt="image" src="https://github.com/user-attachments/assets/e2a3886a-ec98-435b-9134-35801540c9a7" />
<img width="1265" height="115" alt="image" src="https://github.com/user-attachments/assets/2d105ebe-2ce7-4481-8c82-5b448149a8f9" />



---

### 6) Quel est le nom du fichier consulté sur le site de partage ?

* **Format de réponse :** `******.***`

* **Réponse :*secret.txt*

* **Preuve (capture d'écran, requête) :**
<img width="1352" height="639" alt="image" src="https://github.com/user-attachments/assets/f4fe88ad-d1bb-4713-8ff7-4f9c362a26f0" />
<img width="1255" height="100" alt="image" src="https://github.com/user-attachments/assets/15c0acb7-81be-4918-8898-60558e690332" />

---

### 7) Le fichier contient un code secret au format `THM{_____}`. Quel est ce code ?

* **Format de réponse :** `THM{******__****}`

* **Réponse :*THM{SECRET__CODE}*

* **Preuve (capture d'écran, requête) :**
<img width="1352" height="639" alt="image" src="https://github.com/user-attachments/assets/8338a150-619d-4d8d-9f5c-827df72bee64" />
<img width="1270" height="98" alt="image" src="https://github.com/user-attachments/assets/58d6f322-0ecd-4765-accd-c3d86f0838a3" />

### analyse final sur la methode 

Lors de l’enquête, nous avons repéré deux adresses IP candidates dans les journaux — mais en situation réelle il est rarement aussi simple de désigner immédiatement la machine compromise. Pour raffiner notre recherche, nous avons exploité le champ user_agent. Le user-agent bitsadmin a retenu notre attention : bien qu’il s’agisse d’un utilitaire Windows légitime, il est fréquemment détourné par des malwares pour récupérer des payloads (C2, scripts malveillants, etc.) et reste peu courant en usage quotidien. En lançant une requête KQL   sur ce user_agent,   user_agent:*bitsadmin*
nous avons retrouvé le log incriminant qui contenait l’adresse IP suspecte —ce qui nous a permis de relier l’activité réseau malveillante à une machine précise et de poursuivre l’investigation.

---

