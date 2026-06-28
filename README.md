<div align="center">
  <img src="banniere_orioris.jpg" alt="ORIORIS Cyber Security" width="100%" />
  
  <h1>ORIORIS | CV Interactif & Clone Numérique</h1>
  
  <p><b>Démonstrateur Technique : Agent IA conversationnel couplé à une base vectorielle locale (RAG).</b></p>

  <p>
    <img src="https://img.shields.io/badge/Frontend-Vanilla_JS_%2B_Tailwind-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white" alt="Frontend"/>
    <img src="https://img.shields.io/badge/Orchestration-n8n-FF5722?style=for-the-badge&logo=n8n&logoColor=white" alt="n8n"/>
    <img src="https://img.shields.io/badge/Vector_DB-Qdrant-CD5C5C?style=for-the-badge&logo=database&logoColor=white" alt="Qdrant"/>
    <img src="https://img.shields.io/badge/LLM-Local_Proxy-483699?style=for-the-badge&logo=openai&logoColor=white" alt="LLM"/>
  </p>
</div>

---

## 🚀 Le Projet : "Answer Engine" B2B & Cyber

Ce dépôt héberge mon CV numérique, mais sa véritable valeur réside dans son **infrastructure**. 
Après 20 ans d'expérience dans le déploiement logiciel B2B et la gestion de comptes stratégiques, ma transition vers la Cybersécurité (AIS) s'accompagne d'une exigence : la souveraineté des données.

Ce projet démontre ma capacité à concevoir un **système de Retrieval-Augmented Generation (RAG) 100% local et sécurisé**, intégré à une interface web fluide pour interagir avec les recruteurs.

---

## 🏗️ Architecture Logique (VSL)

Le système est divisé en deux pipelines distincts orchestrés par n8n :
1. **L'Ingestion (Asynchrone) :** Traitement des fichiers locaux, découpage sémantique et vectorisation.
2. **L'Inférence (Temps Réel) :** L'interface web interroge l'agent qui croise la mémoire (Vector Store) avec le LLM.

```mermaid
graph TD
    %% --- Interface Web (Frontend) ---
    subgraph Frontend [cv.orioris.com]
        UI[Interface HTML/Tailwind<br/>Moteur Markdown]
        BTN(Bouton Chat IA)
        UI --- BTN
    end

    %% --- Le Bunker IA (Orchestration n8n) ---
    subgraph Bunker [Bunker IA : Infrastructure Docker Locale]
        direction TB
        
        %% Pipeline Inférence
        subgraph Inference [Pipeline d'Inférence Temps Réel]
            WH[Webhook POST<br/>/question-cv-patrice]
            AGENT{AI Agent<br/>Modèle: bunker-auto}
            MEM[Simple Memory<br/>Contexte Session]
        end
        
        %% Pipeline Ingestion
        subgraph Ingestion [Pipeline d'Ingestion RAG_TEST]
            TRIG[Local File Trigger]
            CHUNK[Split in Chunks<br/>Taille: 1500 / Overlap: 300]
            EMBED[Ollama Embeddings]
        end
        
        %% Persistance Vectorielle
        VDB[(Qdrant Vector Store<br/>Mémoire des Expériences)]
    end

    %% --- Les Flux ---
    BTN -->|Requête Utilisateur| WH
    WH --> AGENT
    MEM -.->|Contexte| AGENT
    
    TRIG -->|Lecture fichiers .md| CHUNK
    CHUNK -->|Vectorisation| EMBED
    EMBED -->|Stockage Indexé| VDB
    
    VDB -.->|Retrieval RAG| AGENT
    AGENT -->|Réponse formatée| UI

    %% --- Styles ---
    style Frontend fill:#1e1e1e,stroke:#38B2AC,stroke-width:2px,color:#fff
    style Bunker fill:#0d1117,stroke:#FF5722,stroke-width:2px,color:#fff
    style Inference fill:#2d3436,stroke:#fff,color:#fff
    style Ingestion fill:#2d3436,stroke:#fff,color:#fff
    style AGENT fill:#8e44ad,stroke:#fff,color:#fff
    style VDB fill:#c0392b,stroke:#fff,color:#fff
```

---
## 📂 Contenu du Dépôt 

index.html : Interface principale avec design monolithique et intégration de Tailwind CSS. Contient le script asynchrone de connexion au webhook n8n, la gestion du statut de l'IA et le parsing Markdown des réponses.

Les notes documentaires (format Obsidian) décrivant l'ingénierie des flux n8n :

♾️Agent CV - Patrice Vayne.md : Documentation du workflow question-cv-patrice. Définit l'agent conversationnel, sa mémoire de session et sa connexion au LLM bunker-auto.

♾️Pipeline d'Ingestion RAG (RAG_TEST).md : Documentation de l'automate d'alimentation. Surveille les fichiers, les découpe (chunks) et les stocke dans Qdrant.

## ⚙️ Mécanique Interne & Sécurité

Souveraineté : Le système de chat n'appelle pas directement des API publiques (OpenAI/Anthropic). Il passe par un routeur interne (litellm_bunker) qui permet de basculer instantanément sur des modèles open-source locaux (Mistral, Llama 3) via Ollama en cas de besoin.

Vectorisation Contrôlée : Le chunking est calibré manuellement (1500 tokens / overlap 300) pour garantir que le contexte renvoyé à l'agent lors des requêtes métier (ex: "Quelles sont ses compétences en CAD/CRM ?") soit pertinent et sans hallucination.

Design VSL (Visual Spatial Learning) : Le code frontend est optimisé pour une charge cognitive minimale, avec un parsing Markdown immédiat des réponses de l'IA.
