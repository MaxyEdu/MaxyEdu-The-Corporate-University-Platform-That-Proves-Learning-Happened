# MaxyEdu — The Corporate University Platform That Proves Learning Happened

---

## The Problem

Corporate training is broken in three ways:

1. **Certificates are worthless.** A PDF saying "João completed Compliance 101" can be forged, lost, or never verified. HR departments have no reliable way to prove an employee actually learned something.
2. **Engagement is near zero.** Traditional LMS platforms (Moodle, Cornerstone) are transactional — watch video, click next, done. There's no reason to come back, no competition, no identity.
3. **White-labeling is painful.** Every enterprise wants *their* university, not a generic platform. Spinning up a branded corporate university today means months of custom development.

---

## What MaxyEdu Is

MaxyEdu is a **white-label SaaS platform for corporate universities** that solves all three problems simultaneously.

> *"Your company's university. Your brand. Immutable proof every certificate is real."*

---

## The Three Pillars

### 1. Blockchain-Certified Learning

When an employee completes a course, MaxyEdu mints a **Soulbound Token (SBT)** — a non-transferable ERC-721 NFT — directly to their personal wallet on a private Hyperledger Besu blockchain.

What this means in practice:
- The certificate **cannot be transferred, sold, or faked**
- Anyone can verify it at `/verify` — no login, no backend call, reads straight from the chain
- A public URL like `maxyedu.yourcompany.com/verify?hash=0xabc...` is permanently valid
- Batch minting for cohort completions (e.g. "100 employees finished LGPD training")
- Prerequisite locking — the smart contract enforces course chains; you can't fake completion order

The infrastructure runs on **Hyperledger Besu in QBFT consensus** with zero gas costs in private mode. For client deployments that want a public paper trail, it bridges to Polygon Amoy testnet.

---

### 2. Gamification That Actually Works

MaxyEdu has a full gamification layer that makes learning sticky:

- **XP + Levels**: Every completed lesson awards XP. Enough XP triggers a level-up event with a visual celebration. Your level is on your public profile.
- **$MAXY Token**: An ERC-20 token awarded for milestones. Tenants can configure what $MAXY redeems for — discounts, courses, swag.
- **Leaderboard**: Real-time rankings visible to the whole company. Public, no auth required. Social pressure works.
- **Badges**: Awarded for specific achievements (first course, 10-course streak, perfect quiz score). Displayed on profiles.
- **GPS Learner Map**: A visual map of the learning journey — employees see where they are in a curriculum, what's locked, what's ahead.
- **Social Feed**: Post achievements, comment on completions. Learning becomes visible inside the organization.

None of this is cosmetic. XP, levels, and leaderboard position are all stored on the backend and reflected in the blockchain-minted certificate metadata.

---

### 3. Zero-Friction White-Labeling

Each corporate client is a **tenant**. Tenant isolation is architectural:
- Separate branding (logo, colors via CSS custom properties injected per-tenant)
- Separate course catalogs and enrollment rules
- Separate leaderboards and social feeds
- Data segregated at the database level

Provisioning a new corporate university takes minutes, not months. The platform is multi-tenant SaaS with tenant-specific CSS branding that loads dynamically — no redeployments.

---

## The Content Layer

### AI Course Generator
Instructors don't need to be curriculum designers. Type a topic ("LGPD for HR Professionals"), choose modules (3), lessons per module (4), target audience, and language (English / Portuguese). Claude/GPT-4 generates:
- Course title, description, tags
- Full module and lesson structure
- Quiz questions with correct answers and XP rewards
- All ready to publish in one click or edit before publishing

This compresses course creation from days to minutes.

### Live Sessions
WebRTC-based live class integration. Instructors can schedule and run live sessions attached to specific courses. Attendance counts toward completion.

### Prerequisite Chains
Courses can require completion of other courses — enforced both in the UI and **on-chain** in the smart contract. You can't complete "Advanced Risk Management" before "Risk Management Fundamentals."

---

## The Technical Moat

```
Next.js 16 / React 19          →  Fast, SEO-friendly, PWA-capable
NestJS REST API                →  Modular, testable, RBAC-ready
PostgreSQL 15                  →  Relational integrity, full-text search
Redis                          →  30s TTL on course listings, 60s on leaderboard
Hyperledger Besu (QBFT)        →  Private blockchain, zero gas, deterministic
MaxySBT.sol (ERC-721 SBT)      →  Non-transferable, batch mint, prereq locking
MaxyToken.sol (ERC-20 $MAXY)   →  Gamification currency, owner-only mint
```

**Security posture that enterprise procurement will sign off on:**
- JWT (15min) + refresh token (7d) in httpOnly SameSite=strict cookies
- AES-256-CBC encrypted private keys at rest
- Argon2 password hashing
- RBAC: `ADMIN` / `INSTRUCTOR` / `STUDENT` roles with guard-level enforcement
- 2FA (TOTP) built in, optional per user
- Rate limiting (throttling) on all auth endpoints
- Trivy + TruffleHog in CI/CD pipeline — no critical CVE can reach production
- SonarCloud quality gate at 80% coverage minimum

**Real-time everything:**
- WebSocket notifications (lesson completions, level-ups, badges, course enrollments)
- Web Push (VAPID) for browser notifications even when the app is closed
- Service worker PWA — works offline, installable on mobile

---

## Deployment Model

| Mode | Stack | Target |
|------|-------|--------|
| **Dev** | Docker Compose — Besu + PostgreSQL + Redis + NestJS + Next.js | Local / staging |
| **Cloud SaaS** | Azure App Service (backend) + Vercel (frontend) + Azure PostgreSQL | Multi-tenant production |
| **Self-hosted** | Oracle Cloud Always Free — single ARM VM, nginx TLS, all services in Docker | SMB / data-sovereignty clients |

CI/CD pipeline: GitHub Actions → security scan → test suite (488 tests, 80%+ coverage) → Docker image to GHCR → blue-green swap on Azure. `develop` → staging, `main` → production.

---

## Who Buys This

**Primary buyer:** L&D / HR leadership at mid-to-large enterprises, specifically in regulated industries:
- **Financial services** — compliance training with verifiable proof
- **Healthcare** — mandatory certifications that auditors can independently verify
- **Tech companies** — upskilling programs with competitive leaderboards
- **Government contractors** — training records that survive audit

**Secondary buyer:** EdTech startups that want to build a branded learning platform without building infrastructure from scratch — they white-label MaxyEdu.

---

## The Elevator Version

> MaxyEdu is the only corporate LMS where every certificate is on-chain and unforgeable, employees compete on a live leaderboard to level up, and new courses are generated by AI in 10 seconds. Your brand, your blockchain, your university — deployed in a week.

---

## Where It Stands Today

The platform is **feature-complete for an MVP launch**:
- Full auth stack (register, login, 2FA, email verification, password reset)
- Course CRUD with AI generation, video/text/quiz lessons, prerequisite chains
- Blockchain minting and public verifier live
- Gamification (XP, levels, leaderboard, badges, $MAXY token)
- Social feed, notifications (in-app + push), GPS map
- Multi-tenant CSS branding
- PWA + offline support
- 36 backend test suites, 488 passing tests
- E2E Playwright suite (register → enroll → complete → certificate)
- Oracle Cloud and Azure/Vercel deployment stacks ready

The next phase adds: Stripe payments for paid course marketplaces, analytics dashboard for L&D managers, mobile app (React Native), and a marketplace where tenants can sell courses to each other.

https://discord.gg/eWjv2Wqq
