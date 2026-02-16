# Connecteurs

## Fonctionnement des références d'outils

Les fichiers du plugin utilisent `~~catégorie` comme placeholder pour l'outil que l'utilisateur connecte dans cette catégorie. Par exemple, `~~erp` peut désigner Sage, Cegid, Pennylane ou tout autre logiciel comptable disposant d'un serveur MCP.

Les plugins sont **agnostiques des outils** — ils décrivent les workflows en termes de catégories (erp, data warehouse, paie, etc.) plutôt que de produits spécifiques. Le `.mcp.json` pré-configure certains serveurs MCP, mais tout serveur MCP de la même catégorie fonctionne.

## Connecteurs pour ce plugin

| Catégorie | Placeholder | Serveurs inclus | Autres options |
|-----------|-------------|-----------------|----------------|
| Data warehouse | `~~data warehouse` | Snowflake\*, BigQuery | Databricks, PostgreSQL |
| Email | `~~email` | Microsoft 365 | Gmail |
| Suite bureautique | `~~suite bureautique` | Microsoft 365 | Google Workspace |
| Chat | `~~chat` | Slack | Microsoft Teams |
| ERP / Comptabilité | `~~erp` | — (pas de serveur MCP supporté) | Sage, Cegid, Pennylane, QuickBooks, EBP, Divalto |
| Paie | `~~paie` | — (pas de serveur MCP supporté) | Silae, PayFit, Sage Paie, ADP |
| Banque | `~~banque` | — (pas de serveur MCP supporté) | Open Banking (DSP2), Qonto, Shine |
| Analytics / BI | `~~analytics` | — (pas de serveur MCP supporté) | Power BI, Tableau, Looker |

\* Placeholder — URL MCP pas encore configurée
