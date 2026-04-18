---
name: russian-content-compliance
description: >
  Ensure public Russian-language content complies with RF internet and media laws.
  Covers: Meta/Instagram/Facebook (extremist org ban), YouTube (de-facto blocked),
  Telegram (restricted), VPN/proxy promotion ban, cryptocurrency advertising ban,
  blogger registration rules. Use when: (1) writing or reviewing any public-facing
  Russian content (landing pages, blog posts, guides, social media, emails),
  (2) user mentions Instagram/YouTube/Telegram/VPN/proxy/crypto in content context,
  (3) creating marketing copy for Russian audience, (4) checking content for legal
  compliance, (5) user says "проверь на законы", "РКН", "экстремизм", "запрещённые
  сети", "compliance", "российские законы". Triggers: /russian-compliance,
  /ruscompliance, "check Russian laws", "проверь контент".
---

# Russian Content Compliance

Sanitize all public Russian-language content to comply with RF internet and media laws.
Apply replacements automatically. Never output banned terms without replacement in public content.

## Replacement Dictionary

Apply these replacements in ALL public-facing content (landing pages, guides, posts, emails):

| Banned term | Replacement | Reason |
|---|---|---|
| Instagram | ЗапретГрам | Meta = extremist org (court ruling 21.03.2022) |
| Facebook | ЗапретБук | Meta = extremist org |
| YouTube | ЗапретТюб | De-facto blocked, de-jure restricted |
| Telegram | ДуровГрам | Blocked since 04.2026, status unstable |
| VPN | трёхбуквенный обходыч | 281-FZ, fines 50K-500K RUB for promotion |
| proxy / прокси | обходной путь | Same law, same risk |
| VPN-сервис | сервис обхода | Avoid direct reference |

### Replacement Rules

1. Replace the exact word, preserve surrounding context
2. On first mention add footnote-style disclaimer if the platform is Meta-owned:
   `(деятельность организации Meta Platforms Inc. запрещена на территории РФ)`
3. For YouTube and Telegram -- no legal disclaimer required (not extremist), but use replacement for consistency
4. NEVER write "подписывайтесь в Instagram/Facebook" or similar calls to action -- qualifies as "propaganda of extremist organization"
5. NEVER include hyperlinks to VPN services or instructions how to set up VPN -- qualifies as advertising (281-FZ)
6. Crypto: do not advertise crypto exchanges, trading platforms, or investment schemes. Mentioning crypto as a concept/technology is acceptable

### Context Exceptions (NO replacement needed)

- Internal files (CLAUDE.md, rules.md, TOOLS.md, SKILL.md, memory) -- not public content
- Code comments and variable names
- Git commit messages
- Technical documentation not intended for public distribution
- Direct quotes from laws or court rulings
- When user explicitly says "без замен" or "internal"

## Legal Framework (summary)

For full details with law texts and sources see [references/laws.md](references/laws.md).

| Law | What it covers | Penalty |
|---|---|---|
| Court ruling 21.03.2022 | Meta = extremist org | Art. 282.3 UK RF, up to 8 years for financing |
| 72-FZ (07.04.2025, effective 01.09.2025) | Ad ban on extremist platforms | 100K-500K RUB per post (entity) |
| 276-FZ (2017) | VPN/anonymizer operators must comply with RKN | Service blocked |
| 168-FZ (03.2024) | VPN popularization ban | Content removal by RKN |
| 281-FZ (07.2025, effective 01.09.2025) | VPN advertising penalty | 50K-80K (individual), 200K-500K (entity) |
| 259-FZ (2020) | Crypto = property, not payment method | Advertising banned |
| Blogger law (11.2024) | Channels >10K must register with RKN | Monetization ban, possible blocking |
| 152-FZ (2006, amended) | Personal data localization in RF | RKN blocking + fines up to 18M RUB |
| 255-FZ (2022, amended 2025) | Foreign agents labeling | 30K-500K RUB (admin), up to 2 years prison (criminal) |

## Workflow

1. Receive content text
2. Scan for banned terms (case-insensitive, all declensions)
3. Apply replacements from dictionary
4. Add Meta disclaimer on first mention if applicable
5. Check for VPN/proxy instructions or links -- remove or rewrite
6. Check for crypto advertising -- remove or flag
7. Check for foreign agent mentions -- verify against Minjust registry, add label if found
8. Return cleaned content

## Declension Handling

Russian words decline. Match and replace all forms:

| Base | Forms to catch |
|---|---|
| Инстаграм | Инстаграма, Инстаграме, Инстаграму, Инстаграмом, Instagram |
| Ютуб | Ютуба, Ютубе, Ютубу, Ютубом, YouTube |
| Телеграм | Телеграма, Телеграме, Телеграму, Телеграмом, Telegram |
| ВПН | VPN, впн, ВиПиЭн |
| Фейсбук | Фейсбука, Фейсбуке, Facebook |

Replacement declensions:
- ЗапретГрам / ЗапретГрама / ЗапретГраме / ЗапретГрамом
- ЗапретТюб / ЗапретТюба / ЗапретТюбе / ЗапретТюбом
- ЗапретБук / ЗапретБука / ЗапретБуке / ЗапретБуком
- ДуровГрам / ДуровГрама / ДуровГраме / ДуровГрамом
- трёхбуквенный обходыч / трёхбуквенного обходыча / трёхбуквенном обходыче

## Quick Checklist (before publishing)

- [ ] No raw mentions of Instagram/Facebook/YouTube/Telegram/VPN/proxy
- [ ] Meta disclaimer present on first mention of ЗапретГрам or ЗапретБук
- [ ] No calls to action directing users to banned platforms
- [ ] No VPN setup instructions or links to VPN services
- [ ] No crypto exchange ads or investment advice
- [ ] If channel >10K subscribers -- verified RKN registration?
- [ ] If storing Russian users PII -- infrastructure on RF servers (Yandex Cloud)?
- [ ] Any foreign agents mentioned? Check Minjust registry, add mandatory label if found

## 152-FZ -- Personal Data Localization

**152-FZ** requires that personal data of Russian citizens be stored and processed on servers physically located in the Russian Federation.

**Compliance stack (Yandex Cloud):**
- Serverless Containers -- compute
- Managed PostgreSQL -- database (PD storage)
- Yandex Object Storage (S3-compatible) -- file/media storage

**When it applies:**
- Collecting name, email, phone, address, payment data from Russian users
- User registration, profiles, subscriptions, CRM
- Any form that collects PII from users in Russia

**Architectural rule:** If a project stores personal data of Russian citizens, backend infrastructure MUST be on Yandex Cloud (or other RF-based provider). Foreign clouds (AWS, DO, Hetzner) are NOT compliant for PD storage.

**Exception:** Aggregated/anonymized data without PII can be stored anywhere.

## 255-FZ -- Foreign Agents (Иноагенты)

**255-FZ** (14.07.2022, effective 01.12.2022) requires mandatory labeling when mentioning foreign agents or their materials in public content.

**Registry:** https://minjust.gov.ru/ru/pages/reestr-inostryannykh-agentov/ (check before publishing)

### Mandatory Label

When mentioning a foreign agent or citing their materials, add this disclaimer:

> «Настоящий материал (информация) произведён (распространён и (или) направлен) иностранным агентом [ФИО/наименование] либо касается деятельности иностранного агента [ФИО/наименование]»

**Format requirements:**
- Text: font size **2x larger** than body text, placed above or under headline
- Video/audio: at the **beginning** of broadcast and each resumption
- Print: minimum **15% of material area**
- Social media: visible without user interaction
- Always add **18+** marking

### When to Label

- Citing materials created by a foreign agent
- Mentioning a foreign agent's activities or statements
- Republishing/reposting foreign agent content
- Collaborative work with a foreign agent

### When NOT to Label

- Personal materials of a foreign agent (family photos, travel)
- Academic mention without citing their materials
- Internal/non-public documents

### Notable Foreign Agents (examples)

- «Медуза» (Meduza), «Дождь», «Важные истории», «The Insider»
- Individual journalists, writers, artists -- list updated weekly

### Penalties

| Violation | Penalty |
|---|---|
| Agent doesn't self-label | 30K-50K (individual), 300K-500K (entity) |
| Media doesn't label agent mention | 2K-5K (individual), 40K-50K (entity) |
| Criminal (after 1 admin violation, since 10.2025) | Up to 2 years prison |

### Workflow Addition

Before publishing: check the Minjust registry for any person/org mentioned in content. If found -- add mandatory label with exact wording above.
