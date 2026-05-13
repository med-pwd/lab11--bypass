# LAB 11 : Bypass Root Detection avec Frida
analyste: souaid med amine

 1. Objectif du Lab

Ce laboratoire a pour objectif de :

Comprendre comment les applications Android détectent le root
Utiliser Frida pour contourner cette détection
Implémenter des hooks Java et natifs

## Environnement

- OS : Windows 10

- Outil : Frida 17.9.1
  
- Émulateur : Android Emulator (Android 8+)
  
- ADB : installé et configuré
  
- Application testée : projet.fst.ma.starsgallery
  
##  Vérification de l’environnement


<img width="364" height="70" alt="image" src="https://github.com/user-attachments/assets/88fec9a1-ea22-4685-acf0-7356571011fd" />

<img width="163" height="48" alt="image" src="https://github.com/user-attachments/assets/eaabb4ab-ddfd-4f44-825a-70fded428e3b" />

##  Vérifier l’architecture

<img width="279" height="38" alt="image" src="https://github.com/user-attachments/assets/91e50b20-7fac-4892-b15f-a5c9c59f3d97" />


##  Télécharger Frida

- https://github.com/frida/frida/releases

## Push vers Android

Dans le bon dossier :

<img width="625" height="47" alt="image" src="https://github.com/user-attachments/assets/6a34a4f0-8040-4712-91c1-c8d9ce27b65a" />

## Donner les permissions

<img width="593" height="23" alt="image" src="https://github.com/user-attachments/assets/2851ca47-0fb2-4f6b-bb18-dc122c5462c6" />

## Lancer frida-server

<img width="386" height="58" alt="image" src="https://github.com/user-attachments/assets/700fd15e-743f-45ec-8237-69aba6f5693b" />

<img width="376" height="313" alt="image" src="https://github.com/user-attachments/assets/6e803b5c-d793-4ecb-ae79-d0ee467d9d03" />

## Étape 1 — Démarrage et repérage du package

<img width="421" height="23" alt="image" src="https://github.com/user-attachments/assets/78a7cc88-c83d-4b34-8673-030514ae2f46" />

## Étape 2 — Script Frida (bypass Java) prêt à l’emploi

- L’application StarsGallery est lancée directement sur l’émulateur.

  ### Identifier le processus
  
  <img width="287" height="35" alt="image" src="https://github.com/user-attachments/assets/6ca13fb2-01aa-4630-92b5-997378f02ac9" />

 ### Injection du script Frida

 <img width="520" height="203" alt="image" src="https://github.com/user-attachments/assets/c829c1a0-b315-4aec-87df-79d1add50cee" />

### - Script utilisé : bypass_root.js

Le script permet de contourner les vérifications de root en :

- Modifiant Build.TAGS
  
- Neutralisant RootBeer
  
- Bloquant File.exists() pour les fichiers suspects
  
- Interceptant Runtime.exec() pour empêcher l’exécution de commandes comme su

<img width="890" height="476" alt="image" src="https://github.com/user-attachments/assets/17396997-8507-4ebd-9b09-94fcd7fd961d" />

## Étape 3 — Ajouter des hooks natifs (si la détection passe par du code C/C++)

### - Script utilisé : bypass_native.js

Ce script permet de :

- Intercepter les fonctions natives (open, access, stat, etc.)
  
- Bloquer l’accès aux fichiers liés au root
  
- Empêcher la lecture de /proc/mounts
  

<img width="618" height="203" alt="image" src="https://github.com/user-attachments/assets/6847f703-c75b-483b-a55a-5b2822229e9d
 " />
 

 ## Étape 4 — Masquer quelques anti‑Frida basiques 

- J'ai crée script "anti_frida.js" pour masque certains indicateurs liés à Frida :

    - variables d’environnement
 
    - connexions aux ports Frida (27042 / 27043)

<img width="833" height="233" alt="image" src="https://github.com/user-attachments/assets/55990df9-8a56-42b4-80cc-d4ebb4875277" />

## Étape 5 — Méthodes de lancement et conseils pratiques

1. Lancer l’app directement avec Frida (spawn)

<img width="627" height="233" alt="image" src="https://github.com/user-attachments/assets/ce8dc60d-1605-4ccb-8f34-af4d35f97b2b" />
  
2. Attacher à une application déjà ouverte
   
   frida -U -n StarsGallery -l bypass_root.js
   
3. Analyse des fonctions natives avec frida-trace

    frida-trace -U -i open -i access -i stat -i openat -i fopen -i readlink projet.fst.ma.starsgallery 
