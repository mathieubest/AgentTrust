# AgentTrust

**Don't just trust an AI service. Verify its reputation.**  
**Ne faites pas seulement confiance à un service IA : vérifiez sa réputation.**

> **Status / Statut:** Hackathon MVP — concept and implementation plan. Features, deployments, payments and results described below are **not yet claimed as completed**.

**Languages:** [Français](#français) · [English](#english)

---

# 🇫🇷 Français

## En une phrase

**AgentTrust est une couche de découverte et de réputation vérifiable pour les services utilisés par les agents IA et les humains : découvrir, payer, évaluer et vérifier, grâce à Stellar et Soroban.**

## Le problème

Les agents IA peuvent appeler des API et effectuer des paiements, mais ils ne disposent pas toujours d'informations fiables pour décider **quel service choisir**. Un annuaire présente des prix et des descriptions ; une note classique ne prouve pas que l'auteur a réellement utilisé le service. Un service peut devenir lent, indisponible ou décevant après avoir obtenu une bonne réputation.

**Question centrale :** comment permettre à un agent de choisir un fournisseur sur la base d'expériences vérifiables, plutôt que de simples promesses marketing ?

## Notre solution

AgentTrust combine quatre fonctions :

1. **Découvrir :** parcourir un catalogue de services API, leurs capacités, prix et performances observées.
2. **Payer :** consommer un service payant avec un parcours compatible x402 et un règlement sur Stellar en USDC (objectif MVP).
3. **Évaluer :** publier une note après une utilisation, avec des métriques observables lorsque c'est possible.
4. **Vérifier :** relier l'évaluation à une attestation d'utilisation, consultable via un smart contract Soroban.

Les humains disposent d'une interface lisible ; les agents peuvent interroger les mêmes informations par API.

## Notre innovation : Proof of Experience

Nous séparons **trois niveaux de confiance** :

| Niveau | Signification | Limite |
| --- | --- | --- |
| Avis déclaré | Un utilisateur ou agent publie une appréciation | L'utilisation n'est pas prouvée |
| Utilisation attestée | Une autorité désignée atteste qu'une interaction ou un paiement a eu lieu | Cela ne prouve pas la qualité |
| Qualité testée | Des contrôles reproductibles mesurent disponibilité, latence ou résultat | Les tests ne couvrent pas tous les cas d'usage |

**Une transaction prouve un paiement, pas la qualité d'un service.** AgentTrust rend cette distinction visible au lieu de la masquer derrière une seule note.

### Anti-abus prévu pour le MVP

- Chaque utilisation reçoit un identifiant unique ; **un seul avis par utilisation**.
- Une attestation doit être émise par un émetteur autorisé, et l'avis doit être soumis par l'identité associée à l'utilisation.
- Le contrat refuse les identifiants déjà consommés et les notes hors plage.
- Le score affiché distingue les observations attestées des avis non vérifiés.
- Les limites sont explicites : un émetteur compromis, des identités multiples et des tests manipulés restent des risques. La résistance complète aux attaques Sybil n'est **pas** revendiquée.

## Pourquoi Stellar ?

- **Paiements :** règlement de faibles montants en USDC pour les appels de services.
- **Soroban :** registre public d'attestations et d'évaluations, vérifiable indépendamment de notre interface.
- **Testnet :** démonstration reproductible sans argent réel.

**Test de pertinence :** avec une base de données seule, le catalogue fonctionnerait, mais les attestations et l'historique de réputation dépendraient exclusivement de notre opérateur. Soroban permet à des tiers de vérifier les enregistrements publiés, sans nous croire sur parole. Nous ne mettons pas les données personnelles ni les réponses complètes des API on-chain.

## Pour qui ? Qui paie ?

- **Utilisateurs :** développeurs d'agents, opérateurs d'automatisations et personnes recherchant des services API fiables.
- **Payeur immédiat :** l'agent ou son propriétaire règle les appels API consommés.
- **Modèle économique potentiel :** commission de mise en relation / paiement, outils premium d'observabilité et d'analyse pour fournisseurs. **Hypothèses à valider**, pas des revenus existants.
- **Pourquoi maintenant :** les agents automatisent de plus en plus la sélection et l'appel de services ; ils ont besoin de signaux de confiance exploitables par machine.

## La démo qui fait « wow »

**Mission :** « Trouve une API de traduction fiable, dans un budget de 1 USDC. »

1. L'agent consulte trois services et leurs scores, coûts et preuves.
2. Il sélectionne un fournisseur, réalise un appel payant et reçoit un résultat.
3. Nous activons volontairement un **mode défaillant** dans ce service de démonstration.
4. Un test constate l'échec ; l'agent soumet une évaluation liée à l'utilisation attestée.
5. Le smart contract enregistre l'attestation / l'évaluation ; l'interface montre la transaction et l'évolution du score.
6. L'agent relance sa recherche et **bascule vers un autre fournisseur**, sans choix manuel.

> **Moment clé :** « Our agent didn't just pay for an API. It learned which service to trust — and made that experience verifiable for the next agent. »

**Important :** les trois fournisseurs et le scénario de panne peuvent être contrôlés pour rendre la démo reproductible ; le paiement et les écritures on-chain doivent, eux, être réels sur testnet pour être présentés comme tels.

## MVP : périmètre réaliste

| À livrer | Hors périmètre du premier MVP |
| --- | --- |
| 3 services API de démonstration | Marketplace mondiale ouverte |
| Catalogue + comparaison dans un dashboard | Indexation automatique de milliers de services |
| Agent sélectionnant un fournisseur selon prix et réputation | Économie multi-agents générale |
| 1 paiement testnet compatible x402 si intégration finalisée | Paiements mainnet et garde de fonds réels |
| Contrat Soroban : service, utilisation attestée, avis, lecture | Système anti-Sybil décentralisé complet |
| Historique des preuves et lien vers l'explorateur | Certification universelle de qualité |

**Priorité absolue :** un parcours complet et reproductible. Si x402 n'est pas finalisé à temps, nous documenterons honnêtement la différence entre le paiement Stellar démontré et l'intégration x402 restant à faire.

## Architecture proposée

```text
Humain (dashboard)                  Agent IA (client)
         \                              /
          \                            /
             API AgentTrust / Backend
                  |            |
        Catalogue & tests     Passerelle de paiement x402
                  |            |
                  |         Stellar Testnet / USDC
                  |
         Service d'attestation autorisé
                  |
         Smart contract Soroban
         - registre de services
         - utilisations attestées
         - évaluations uniques
         - consultation de réputation
                  |
         Historique vérifiable on-chain
```

Les données volumineuses (réponses API, logs, commentaires détaillés) restent hors chaîne. Le contrat conserve uniquement les champs nécessaires à la vérification, avec des identifiants non sensibles et éventuellement des empreintes cryptographiques.

## Contrat Soroban prévu : `AgentTrustRegistry`

**Interface conceptuelle — noms et paramètres susceptibles de changer à l'implémentation :**

```rust
register_service(service_id, metadata_hash)
record_usage(usage_id, service_id, reviewer, evidence_hash)
submit_review(usage_id, rating, result_hash)
get_reputation(service_id)
```

**Règles :** `register_service` exige l'authentification du fournisseur ; `record_usage` exige celle d'un émetteur d'attestations autorisé ; `submit_review` exige celle du destinataire enregistré et interdit le double avis. Une note doit respecter une plage définie (par exemple 1–5). Les scores agrégés et leur méthode de calcul devront être documentés ; aucune « preuve de qualité » automatique n'est déduite du seul paiement.

## Installation et reproduction — à compléter après implémentation

**Prérequis envisagés :** Rust, cible `wasm32v1-none`, Stellar CLI, Node.js et un compte Stellar Testnet financé.

```bash
# Dans le répertoire du projet de contrat (une fois créé)
stellar network use testnet
stellar keys generate agenttrust-demo --fund
stellar keys use agenttrust-demo
stellar contract build
stellar contract deploy --alias agenttrust

# Consulter l'interface exacte du contrat déployé
stellar contract invoke --id agenttrust -- --help

# Appel réel : remplacer <fonction> et ses arguments par ceux de l'interface
stellar contract invoke --id agenttrust --send=yes -- <fonction> <arguments>
```

Ces commandes constituent **un guide de préparation**, pas une preuve de déploiement. Le README sera mis à jour avec les véritables commandes, le chemin du projet, les paramètres exacts et les transactions observées.

## Feuille de route

**Jour 1 :** figer l'interface du contrat, coder les autorisations et tests, déployer sur testnet, réaliser un appel réel, créer trois API de démonstration.  
**Jour 2 :** brancher le dashboard, l'agent de sélection, les preuves et le paiement ; enregistrer la démo ; vérifier le README de bout en bout.

**Après le hackathon :** réputation portable entre plateformes, attestations multiples, résistance aux abus, découverte ouverte, analyses fournisseurs et intégrations aux frameworks d'agents.

## Vision HackMeridian (brouillon)

> AgentTrust crée une couche de confiance vérifiable pour l'économie des agents IA. Aujourd'hui, un agent peut découvrir et payer une API, mais ne sait pas toujours si elle est fiable : les notes sont faciles à manipuler et rarement liées à une utilisation réelle. Notre MVP permet à un agent de comparer trois services, d'en choisir un, de l'appeler, puis de publier une évaluation liée à une attestation d'utilisation. Un contrat Soroban enregistre ces preuves et rend l'historique consultable indépendamment de notre interface. Dans notre démonstration, un service devient volontairement défaillant : l'agent détecte le problème, actualise son évaluation et choisit un meilleur fournisseur. Stellar sert aux paiements testnet en USDC et à la vérifiabilité des attestations. À terme, AgentTrust pourra devenir une couche de réputation interopérable entre marketplaces, agents et fournisseurs, également accessible aux humains. Notre priorité est de démontrer un parcours réel, reproductible et honnête sur ses limites.

---

# 🇬🇧 English

## One-line pitch

**AgentTrust is a verifiable discovery and reputation layer for services used by AI agents and humans: discover, pay, review, and verify with Stellar and Soroban.**

## The problem

AI agents can call APIs and increasingly initiate payments, but choosing **which service to trust** is difficult. Directories show prices and marketing descriptions. Ordinary star ratings rarely prove that a reviewer actually used a service. A previously reliable API can become slow, unavailable, or inaccurate.

**Core question:** how can an agent choose providers using verifiable experience rather than unsubstantiated claims?

## The solution

AgentTrust brings together four capabilities:

1. **Discover:** browse API services, capabilities, prices, and observed performance.
2. **Pay:** access paid endpoints using an x402-compatible flow and USDC settlement on Stellar (MVP target).
3. **Review:** rate a service after using it, alongside measurable results where applicable.
4. **Verify:** link the review to a usage attestation recorded through a Soroban smart contract.

Humans use a readable dashboard; agents can consume the same data programmatically.

## What makes it different: Proof of Experience

AgentTrust explicitly distinguishes **three trust levels**:

| Level | What it means | What it does not prove |
| --- | --- | --- |
| Self-reported review | A user or agent shares feedback | That the service was actually used |
| Attested usage | An authorized attester confirms an interaction or payment | That the service delivered quality |
| Tested quality | Reproducible checks measure uptime, latency, or output | That every possible use case will work |

**A payment proves payment, not service quality.** AgentTrust makes that distinction transparent instead of hiding it in one star rating.

### MVP anti-abuse safeguards

- One unique usage identifier can support **only one review**.
- An authorized attester records usage; only the designated reviewer can submit feedback.
- The contract rejects reused usage identifiers and invalid ratings.
- The UI distinguishes attested experiences from unverified claims.
- Limitations remain: compromised attesters, multiple identities, and manipulated tests are possible. We do **not** claim full Sybil resistance.

## Why Stellar?

- **Payments:** USDC settlement for small service purchases.
- **Soroban:** a publicly verifiable record of attestations and reviews, independent of our UI.
- **Testnet:** a reproducible demonstration without real funds.

**The database test:** a centralized database can store a catalog, but then reputation depends entirely on our operator. Soroban enables third parties to verify published records independently. Sensitive data and full API responses stay off-chain.

## Users, buyers, and business model

- **Users:** agent developers, automation operators, and humans searching for dependable APIs.
- **Who pays for services:** an agent or its owner pays the provider per API call.
- **Potential AgentTrust revenue:** transaction/referral fees and premium analytics or observability for providers. These are **business hypotheses**, not current revenue.
- **Why now:** autonomous software increasingly needs machine-readable trust signals before selecting and paying external services.

## The two-minute wow demo

**Mission:** “Find a reliable translation API with a maximum budget of 1 USDC.”

1. The agent compares three providers using cost, score, and evidence.
2. It selects one, makes a paid API call, and receives a result.
3. We deliberately enable a **failure mode** in that demo provider.
4. A test detects the failure and the agent submits feedback linked to attested usage.
5. Soroban records the attestation/review; the dashboard displays the transaction and updated score.
6. The agent searches again and **switches providers autonomously**.

> **Closing line:** “Our agent didn't just pay for an API. It learned which service to trust — and made that experience verifiable for the next agent.”

Demo providers and failure modes may be controlled for reproducibility. Any payment or on-chain write described as real must actually be executed on testnet.

## Realistic MVP scope

| Build for the hackathon | Later |
| --- | --- |
| Three demo API services | Open global marketplace |
| Search and comparison dashboard | Large-scale automated indexing |
| Agent selecting by price and reputation | General multi-agent economy |
| One x402-compatible testnet payment if integration is completed | Mainnet payment operations |
| Soroban contract for services, attested usage, reviews, and reads | Fully decentralized Sybil-resistant reputation |
| Evidence history and explorer links | Universal service quality certification |

**First priority:** one end-to-end reproducible flow. If x402 integration is incomplete, we will clearly distinguish the demonstrated Stellar payment from the remaining x402 work.

## Proposed architecture

```text
Human dashboard                     AI agent client
        \                              /
         \                            /
              AgentTrust API / Backend
                  |            |
           Catalog & tests     x402 payment gateway
                  |            |
                  |         Stellar Testnet / USDC
                  |
             Authorized usage attester
                  |
             Soroban smart contract
             - service registry
             - attested usage
             - unique reviews
             - reputation queries
                  |
             Verifiable on-chain history
```

Large payloads, logs, and detailed comments remain off-chain. Only essential verification data, non-sensitive identifiers, and optionally hashes are written to Soroban.

## Planned Soroban contract: `AgentTrustRegistry`

**Conceptual interface — exact signatures will be finalized during implementation:**

```rust
register_service(service_id, metadata_hash)
record_usage(usage_id, service_id, reviewer, evidence_hash)
submit_review(usage_id, rating, result_hash)
get_reputation(service_id)
```

**Security model:** service registration requires provider authentication; usage recording requires an authorized attester; review submission requires the recorded reviewer and is limited to one review per usage. Ratings must fall within a documented range (e.g. 1–5). Score aggregation rules will be published. Payment alone never automatically certifies quality.

## Build, deploy, and reproduce — complete after implementation

**Planned prerequisites:** Rust, `wasm32v1-none` target, Stellar CLI, Node.js, and a funded Stellar Testnet identity.

```bash
# Run inside the contract project after it has been created
stellar network use testnet
stellar keys generate agenttrust-demo --fund
stellar keys use agenttrust-demo
stellar contract build
stellar contract deploy --alias agenttrust

# Inspect the actual deployed interface
stellar contract invoke --id agenttrust -- --help

# Real invocation: replace placeholders with actual contract function and args
stellar contract invoke --id agenttrust --send=yes -- <function> <arguments>
```

These are **preparation examples, not proof of deployment**. We will replace them with exact commands, project paths, transaction hashes, and reproducible steps once implemented.

## Roadmap

**Day 1:** finalize contract interface, implement authentication and tests, deploy to testnet, perform a real invocation, create three demo APIs.  
**Day 2:** connect dashboard, agent selection, evidence and payments; record the demo; verify the README end to end.

**Beyond the hackathon:** portable cross-platform reputation, multiple attesters, stronger abuse resistance, open discovery, provider analytics, and agent framework integrations.

## HackMeridian vision (draft)

> AgentTrust is building a verifiable trust layer for the AI agent economy. Agents can discover and pay APIs, but they still struggle to know which providers are reliable: ordinary ratings are easy to manipulate and rarely tied to actual usage. Our MVP lets an agent compare three services, choose one, invoke it, and publish feedback tied to a usage attestation. A Soroban contract records the evidence, making the history independently verifiable. In our demo, a provider deliberately fails: the agent detects the problem, updates its assessment, and autonomously selects a better service. Stellar supports testnet USDC payments and verifiable attestations. Our long-term vision is portable reputation across marketplaces, agents, and service providers, with a human-friendly interface too. Our immediate goal is a real, reproducible end-to-end demonstration that is transparent about its limitations.

---

## License / Licence

**To be decided before public release / À définir avant publication publique.**
