---
titre: question-cv-orioris
date_creation: 2026-05-28 19:00
statut: Prod
type: Agent, RAG
tags: SYSTEME/n8n, SYSTEME/Workflows, PROJET/CV
---

# ♾️ Agent CV - Système ORIORIS

---

## 🎯 Objectif Principal (Focus)

> **Résumé**
> Agent IA conversationnel agissant comme un clone numérique pour répondre aux questions des recruteurs/clients en exploitant une base vectorielle RAG. Il est conçu pour orienter la discussion sur les performances business et l'architecture technique du candidat.

---

## 🏗️ Architecture Logique (VSL)

```mermaid
graph TD
    subgraph "Entrée (Frontend)"
        A[Webhook POST <br> /question-cv]
    end

    subgraph "Moteur Cognitif (n8n_bunker)"
        B{AI Agent}
        C([OpenAI Model <br> bunker-auto]) -.->|Cerveau| B
        D([Simple Memory]) -.->|Contexte de Session| B
    end

    subgraph "Base de Connaissances (RAG)"
        E[(Qdrant Vector Store <br> bunker-nomic-768)] <-->|Recherche Top 4| B
        F([Ollama Embeddings <br> nomic-embed-text]) -.->|Vectorisation| E
    end

    A --> B
    B -->|Réponse formatée| G[Sortie HTTP]
    
    style B fill:#3742fa,stroke:#333,stroke-width:2px,color:#fff
    style A fill:#eccc68,stroke:#333,stroke-width:2px,color:#111
    style E fill:#2ed573,stroke:#333,stroke-width:2px,color:#111
```

## 🧠 Cerveau / Instructions Système (Le Prompt)

Directives de l'Agent (Modèle Générique)
PROFIL HYBRIDE [NOM_DU_CANDIDAT] (Business Developer & Architecte Solutions) :

L'IDENTITÉ : Profil hybride rare (vente complexe + tech moderne). Il parle le langage du profit ET de l'intégration technique.

PERFORMANCE : Historique prouvé en vente complexe B2B. Capacité de production (65 licences + 82 Add-ons). 600+ comptes.

EXPERTISE : CAO/DAO/CRM, lecture de plans, agencement technique.

ARSENAL IA : Utilisation n8n, RAG, Protocoles IA pour automatiser.

DIRECTIVE TECHNIQUE ABSOLUE : NE PAS INVENTER. 
Chercher dans Qdrant et restituer les termes exacts (Docker, MCP, LiteLLM). Expliquer que l'IA sert la productivité et le ROI. 
Proposer l'analyse comportementale avec ce code exact : 
<br><br><a href="[URL_DU_PDF]" target="_blank" style="color: #e67e22; text-decoration: underline; font-weight: bold;">📄 Voir le rapport analytique complet</a>

🚨 OVERRIDE : MODE ARCHITECTE TECHNIQUE 🚨 INTERDICTION DE RÉSUMER. Extraction brute depuis la DB. Se comporter comme un CTO. Ne répondre qu'à la question sans noyer avec du contexte inutile.

## 🧩 Composants Techniques (Stack)
Déclencheur : Webhook POST (allowedOrigins: *)

LLM Principal : bunker-auto (via proxy LiteLLM local)

Modèle d'Embedding : nomic-embed-text:latest (Ollama Local)

Base Vectorielle : Qdrant (Collection : bunker-nomic-768 | Top K : 4)

Gestion Mémoire : Simple Memory (Indexée sur le sessionId du webhook)

## 📦 Sauvegarde Technique (Le Code)

```JSON
{
  "nodes": [
    {
      "parameters": {
        "httpMethod": "POST",
        "path": "question-cv-template",
        "responseMode": "lastNode",
        "options": {}
      },
      "type": "n8n-nodes-base.webhook",
      "typeVersion": 2.1,
      "position": [-272, -16],
      "name": "Webhook"
    },
    {
      "parameters": {
        "promptType": "define",
        "text": "={{ $json.body.question }}",
        "options": {
          "systemMessage": "PROFIL HYBRIDE [NOM_DU_CANDIDAT] (Business Developer & Architecte Solutions) :\n\n1. L'IDENTITÉ (Le Profil Hybride) :\nLe candidat n'est ni un développeur informatique classique, ni un vendeur à l'ancienne. C'est un profil hybride rare : un expert de la vente complexe et de la gestion de projet qui utilise les technologies modernes (IA, automatisation) pour écraser la concurrence. Il parle le langage du profit, de la stratégie d'entreprise ET de l'intégration technique.\n\n2. PERFORMANCE COMMERCIALE & BUSINESS :\n- Historique prouvé en vente complexe B2B et cycle court.\n- Capacité de production certifiée.\n- Gestion de portefeuilles de plus de 600 comptes stratégiques.\n\n3. EXPERTISE TECHNIQUE & GESTION DE PROJET :\n- Maîtrise avancée des logiciels de conception (CAO/DAO/CRM).\n- Lecture de plans industriels, agencement technique haut de gamme.\n\n4. L'ARSENAL IA & AUTOMATISATION (L'Avantage Déloyal) :\nLe candidat utilise des technologies de pointe (Workflows, RAG, Protocoles IA) pour automatiser l'administratif et maximiser son temps de closing.\nDIRECTIVE TECHNIQUE ABSOLUE : Pour lister les outils précis, l'architecture réseau ou les modèles utilisés, tu NE DOIS PAS inventer. Tu DOIS IMPÉRATIVEMENT chercher ces informations dans tes documents de contexte (base vectorielle) et restituer les termes exacts (Docker, MCP, LiteLLM, Qdrant, etc.) qui s'y trouvent.\n- Stratégie : Utiliser ces outils pour automatiser l'administratif, structurer les données et maximiser le temps de présence terrain et de closing.\n\nINSTRUCTION STRICTE POUR L'IA (TON RÔLE) :\nTon rôle est de prouver la polyvalence du candidat. \n- Si on te parle de COMMERCE : mets en avant ses chiffres de vente et sa gestion de comptes. \n- Si on te parle d'INFORMATIQUE/IA : explique qu'il déploie ces solutions (n8n, RAG, MCP) pour optimiser les process d'une entreprise et augmenter la productivité, pas juste pour faire du code.\n\n🚨 OVERRIDE : MODE ARCHITECTE TECHNIQUE (ANTI-VULGARISATION) 🚨\nSi le visiteur pose une question pointue sur l'infrastructure, le réseau, les modèles IA ou le routage :\n\nINTERDICTION DE RÉSUMER : Tu ne dois jamais vulgariser ou faire des synthèses génériques de l'infrastructure.\n\nEXTRACTION BRUTE : Tu dois te comporter comme un CTO. Tu dois aller lire ta base vectorielle et recracher les termes techniques EXACTS.\nRÉPONDRE À LA QUESTION : Ne parle pas de gestion de projet si on te pose une question uniquement sur le routage LLM. Sois un sniper de la donnée."
        }
      },
      "type": "@n8n/n8n-nodes-langchain.agent",
      "typeVersion": 3.1,
      "position": [64, 64],
      "name": "AI Agent"
    },
    {
      "parameters": {
        "model": {
          "__rl": true,
          "value": "bunker-auto",
          "mode": "list"
        }
      },
      "type": "@n8n/n8n-nodes-langchain.lmChatOpenAi",
      "typeVersion": 1.3,
      "position": [-208, 240],
      "name": "OpenAI Chat Model"
    },
    {
      "parameters": {
        "mode": "retrieve-as-tool",
        "toolDescription": "Utilise cet outil pour rechercher des informations précises sur le parcours professionnel et les compétences techniques du candidat.",
        "qdrantCollection": {
          "__rl": true,
          "value": "=bunker-nomic-768",
          "mode": "id"
        },
        "topK": "={{ 4 }}"
      },
      "type": "@n8n/n8n-nodes-langchain.vectorStoreQdrant",
      "typeVersion": 1.3,
      "position": [304, 256],
      "name": "Qdrant Vector Store"
    },
    {
      "parameters": {
        "model": "nomic-embed-text:latest"
      },
      "type": "@n8n/n8n-nodes-langchain.embeddingsOllama",
      "typeVersion": 1,
      "position": [112, 448],
      "name": "Embeddings Ollama"
    },
    {
      "parameters": {
        "sessionIdType": "customKey",
        "sessionKey": "={{ $('Webhook').item.json.body.sessionId }}"
      },
      "type": "@n8n/n8n-nodes-langchain.memoryBufferWindow",
      "typeVersion": 1.4,
      "position": [-32, 272],
      "name": "Simple Memory"
    }
  ],
  "connections": {
    "Webhook": {
      "main": [
        [
          {
            "node": "AI Agent",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "OpenAI Chat Model": {
      "ai_languageModel": [
        [
          {
            "node": "AI Agent",
            "type": "ai_languageModel",
            "index": 0
          }
        ]
      ]
    },
    "Qdrant Vector Store": {
      "ai_tool": [
        [
          {
            "node": "AI Agent",
            "type": "ai_tool",
            "index": 0
          }
        ]
      ]
    },
    "Embeddings Ollama": {
      "ai_embedding": [
        [
          {
            "node": "Qdrant Vector Store",
            "type": "ai_embedding",
            "index": 0
          }
        ]
      ]
    },
    "Simple Memory": {
      "ai_memory": [
        [
          {
            "node": "AI Agent",
            "type": "ai_memory",
            "index": 0
          }
        ]
      ]
    }
  },
  "pinData": {}
}
```
