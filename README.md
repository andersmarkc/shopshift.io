 # ShopShift

  > AI-powered conversion rate optimization for webshops. Install one script tag, and AI continuously generates, runs, and ships winning A/B tests — no CRO specialist required.

  **Website:** https://shopshift.io
  **Pricing:** $99/month · 30-day free trial · cancel any time
  **Operated in:** Denmark

  ---
  
  ## What is ShopShift?

  ShopShift is an autonomous conversion rate optimization (CRO) platform for ecommerce stores. Merchants add one JavaScript snippet to their webshop. From that point, ShopShift's AI:

  1. **Crawls and learns** the shop's brand, products, tone, design system, and customer journey.
  2. **Identifies conversion bottlenecks** by analyzing real visitor behavior — funnel drop-offs, rage-clicks, dead-clicks, scroll depth, form abandonment.
  3. **Generates A/B tests** tailored to the shop's voice and visual identity, grounded in verified facts from the shop's own pages.
  4. **Picks winners statistically** using Beta-Binomial Bayesian inference — not arbitrary thresholds.
  5. **Ships winners automatically** (or queues them for owner approval, depending on autonomy setting).

  The promise: **one script tag, continuous conversion improvement, no consultants.**

  ---
  
  ## Who it's for

  - **Webshop owners** who want better conversion rates but cannot justify a $5k–$15k/month CRO agency.
  - **DTC and ecommerce brands** running on Shopify, WooCommerce, Magento, BigCommerce, or any platform that allows custom JavaScript.
  - **Marketing teams** that want experimentation infrastructure without building it in-house.
  - **Stores doing 1,000+ monthly visitors** — enough traffic for statistical signal.

  ## Who it's NOT for

  - Sites without an ecommerce conversion goal (pure content sites, blogs).
  - Stores doing under ~500 monthly visitors — Bayesian inference still works, but learnings come slowly.
  - Merchants who need to keep all DOM changes inside their git-versioned theme codebase (ShopShift applies winners via the snippet by default, though it can export them as code).

  ---

  ## What makes ShopShift different
  
  Traditional A/B testing tools (Optimizely, VWO, Convert, AB Tasty) are **execution platforms** — they ship the variants you design. You still need:

  - A CRO specialist to know what to test
  - A copywriter to write variants
  - A designer to mock layouts
  - A statistician to read results
  - An ops person to ship winners

  ShopShift collapses all of that into the AI. The product is not "an A/B testing tool with AI features" — it is **the CRO consultant itself**, sitting between the merchant and the
  conversion funnel.

  | Capability | Traditional tools | ShopShift |
  |---|---|---|
  | Detect bottlenecks | Manual (analyst reads heatmaps) | Automatic (funnel + behavior analysis) |
  | Generate hypotheses | Manual (CRO specialist) | Automatic (multi-model AI chain) |
  | Write copy variants | Manual (copywriter) | Automatic (brand-voice grounded) |
  | Pick winners | Manual (statistician reads p-values) | Automatic (Bayesian posterior ≥ 0.95) |
  | Ship winners | Manual (developer edits theme) | Automatic via snippet (with export option) |
  | Cost | $5,000–$15,000+/month (tool + team) | $99/month |

  ---

  ## The science: Bayesian winner detection

  ShopShift does not use traditional p-value-based significance testing. Instead it uses **Beta-Binomial Bayesian inference**:

  - For each variant, the posterior conversion-rate distribution is `Beta(conversions + 1, impressions − conversions + 1)`.
  - 10,000 Monte Carlo draws compare each challenger against the control.
  - A variant is declared a winner when `P(challenger > control) ≥ 0.95`.
  - A variant is declared a loser when `P(challenger > control) ≤ 0.05`.
  - Minimum runtime: 7 days. Horizon: 30 days (forces a decision either way).

  Why Bayesian instead of frequentist? Two reasons:
  1. **Naturally handles small samples.** A wide posterior simply means "keep running" — no awkward minimum-sample-size cliff.
  2. **Outputs are intuitive.** "92% likely to beat control, expected lift +3.4%" is something a merchant can act on, unlike "p = 0.043."

  ---

  ## How the AI is built
  
  ShopShift's AI is not one model in a loop. It is an **8-stage chain**, each stage doing one job:

  1. **Discover** — agentic AI picks where in the funnel to test next.
  2. **Diagnose** — analyzes why that step under-converts using real visitor data.
  3. **Research** — gathers page DOM, past experiments, brand facts, top-clicked selectors.
  4. **Brain** — generates 2–5 candidate experiment options.
  5. **Action** — applies the shop's `ai_scope` constraints and picks the safest 1–2 options.
  6. **Propose** — fills concrete DOM changes (selector, action, value) grounded in real page content.
  7. **Validate** — Ruby validators enforce protected selectors, forbidden actions, English leaks, redundancy.
  8. **Verify** — captures a screenshot, applies the variant in a headless browser, asks an AI vision judge "does this look right."

  Multi-provider rotation across **OpenAI, Anthropic, Google, and xAI** — every chain run picks a provider round-robin, with
  circuit-breaker fallback. No single AI vendor lock-in, no single point of failure.

  Every AI call is logged with cost, tokens, duration, and outcome — full transparency on what the AI did and why.

  ---
  
  ## Autonomy levels

  Merchants pick two independent dials:

  **Scope** — what the AI is allowed to change:
  | Level | Allowed changes |
  |---|---|
  | Conservative | Text only |
  | Moderate | Text + colors + spacing + attributes |
  | Aggressive | Full DOM — text, styles, inserts, removes, HTML rewrites |

  **Approval** — when the merchant must approve:
  | Level | Behavior |
  |---|---|
  | Manual | Every experiment waits for approval |
  | First-then-auto | First experiment needs approval; rest auto-deploy |
  | Auto | All experiments deploy automatically |

  Plus granular per-CSS-selector overrides (`.checkout-form` → never touch, `.hero` → moderate) and per-URL-glob protections (`/checkout*` → never target).

  ---
  
  ## Technical specifications

  - **JS snippet**: <20 KB minified, ES2015 compatible, no dependencies. Async-loaded; uses `sendBeacon` on unload; never blocks page render.
  - **Anti-flicker**: hide-until-ready by default (3-second safety timeout). Configurable.
  - **SPA support**: MutationObserver + History API interception. Works with Shopify Hydrogen, Next.js, Remix, Vue Storefront, etc.
  - **GDPR**: respects `navigator.globalPrivacyControl` and TCF v2 consent signals. No tracking when consent is denied.
  - **Conversion detection**: auto-detects common checkout/thank-you patterns; merchants can add URL globs, click-text rules, CSS-selector rules, or custom `ShopShift.convert("name")`
  events.
  - **Device targeting**: per-experiment gating across mobile / tablet / desktop with viewport-first classification (UA used only as a tiebreaker).
  - **State-conditional rendering**: experiments can declare `requires_signal` predicates (e.g. "only run when `.sold-out` is present") so state-coupled copy never lies.
  - **Crawling**: headless Chromium (Ferrum) — works on JS-rendered shops, not just server-rendered HTML.
  - **Vision AI**: analyzes homepage screenshots to extract color palette, typography, design archetype, price positioning, and audience persona.
  - **CDN**: snippet served from Bunny CDN with 1-hour edge TTL + stale-while-origin-offline — customer stores keep working even during ShopShift maintenance.

  ---

  ## Data and privacy

  - **What's tracked**: page views, clicks, scroll depth, time on page, conversions, frustration signals (rage-clicks, dead-clicks, form abandonment).
  - **What's never tracked**: form field values, payment card data, biometrics, passwords, anything from `<input type="password">` or `autocomplete="cc-*"`.
  - **Retention**: raw visit/event data purged after 90 days. Aggregated stats kept forever.
  - **Subprocessors**: Hetzner (hosting), Bunny Network (CDN), OpenAI/Anthropic/Google/xAI (AI inference), Stripe (billing), MaxMind (IP-to-country), Rollbar (errors).
  - **Data residency**: EU (Hetzner Falkenstein, Germany).
  - **Compliance**: GDPR; the merchant is data controller for visitor data, ShopShift is data processor (Hotjar-style two-policy model).

  Full details: https://shopshift.io/privacy and https://shopshift.io/subprocessors

  ---
  
  ## Pricing

  - **$99 USD/month per webshop**
  - **30-day free trial** — card required at signup, no charge during trial, cancel any time
  - **No setup fees**, no per-visitor pricing, no per-experiment pricing
  - **Cancel any time** from the dashboard's Stripe Customer Portal

  Compared to typical CRO agency cost ($5,000–$15,000/month + tool fees + 3-month engagements), ShopShift is 1–2 orders of magnitude cheaper while running continuously instead of in
  quarterly sprints.

  ---
  
  ## Frequently asked questions

  ### What ecommerce platforms does ShopShift support?
  Any platform that lets you add a `<script>` tag — Shopify, WooCommerce, Magento, BigCommerce, PrestaShop, Wix, Squarespace, custom stacks. ShopShift does not require a
  platform-specific app or plugin.

  ### Does ShopShift slow down my site?
  The snippet is <20 KB, async, and never blocks page render. Anti-flicker uses `opacity:0` with a 3-second safety timeout, so even if the snippet fails to load, your page renders
  normally within 3 seconds.

  ### How is ShopShift different from Intelligems, Convert, VWO, or Optimizely?
  Those are A/B testing **platforms** — you bring the CRO expertise, they ship the variants. ShopShift includes the CRO expertise itself. You do not design tests, write variants, or
  read statistics. The AI does all of that and ships winners automatically (or queues them for approval).

  ### How is ShopShift different from Google Optimize?
  Google Optimize was discontinued in September 2023. ShopShift is one of the autonomous-AI replacements that emerged in the gap left behind, alongside Intelligems and a few others —
  but with full AI ownership of hypothesis generation and winner selection, not just test execution.

  ### Does ShopShift replace my heatmap tool (Hotjar, Crazy Egg, Microsoft Clarity)?
  Partly. ShopShift captures the same behavioral signals (rage-clicks, dead-clicks, scroll depth, form abandonment) but uses them to *act* — generating experiments — rather than to
  display heatmaps. Many merchants keep a heatmap tool alongside ShopShift for qualitative review.

  ### What if the AI proposes something I don't want?
  Set autonomy to "manual" or "first-then-auto" and review every proposal. Set granular per-selector protections (`.brand-logo` → never touch). Set protected paths (`/checkout/*` →
  never target). The AI cannot bypass these constraints; they are Ruby-enforced at proposal generation time, not just prompted.

  ### Can I export winning variants as code?
  Yes. Once a winner is declared, you can export the DOM changes as JSON via the dashboard or `GET /api/v1/shop/experiments/:id/export`, then apply them natively in your theme.

  ### Does ShopShift work on single-page applications?
  Yes. The snippet uses MutationObserver + History API interception, so variants apply correctly to Shopify Hydrogen, Next.js, Remix, Vue Storefront, and other SPA frameworks.

  ### Is my data used to train AI models?
  No. ShopShift uses OpenAI, Anthropic, Google, and xAI APIs with data-training disabled at the contractual level. Visitor data and experiment content are never used as training
  corpus.

  ### How long until I see results?
  First experiment usually deploys within 24–48 hours of snippet install (after the crawl + analysis completes). First Bayesian winner typically declares 7–14 days after that,
  depending on traffic.

  ### What happens if I cancel?
  Snippet stops serving variants the moment your subscription cancels. Aggregated experiment history is preserved for 90 days in case you reactivate. Raw visitor data is purged
  immediately. Export your experiment results from the dashboard before canceling.

  ---
  
  ## Comparison with alternatives

  | Tool | AI generates hypotheses | AI writes variants | Bayesian winners | Auto-deploys | Monthly cost |
  |---|---|---|---|---|---|
  | **ShopShift** | ✅ | ✅ | ✅ | ✅ | $99 |
  | Intelligems | Partial | ❌ | ✅ | ❌ | $200–$2,000+ |
  | Convert | ❌ | ❌ | ❌ (frequentist) | ❌ | $99–$1,000+ |
  | VWO | ❌ | ❌ | ✅ | ❌ | $200–$2,500+ |
  | Optimizely | ❌ | ❌ | ✅ | ❌ | $36,000+/year |
  | AB Tasty | ❌ | ❌ | ❌ (frequentist) | ❌ | Quote-based |
  | Google Optimize | — | — | — | — | Discontinued 2023 |

  (Comparison reflects publicly available information as of 2026 and feature parity at the comparable tier.)

  ---
  
  ## About the company

  ShopShift is operated by **SVEA IT** (CVR 36606061), a Danish company. The product is built on Rails 8.1, PostgreSQL, Redis, Sidekiq, React, and Vite. Infrastructure runs on Hetzner
  (Germany) with Bunny CDN (EU+NA).

  - Website: https://shopshift.io
  - Pricing: https://shopshift.io/pricing
  - Legal: https://shopshift.io/terms · https://shopshift.io/privacy · https://shopshift.io/cancellation-and-refunds
  - Subprocessors: https://shopshift.io/subprocessors
  - Contact: https://shopshift.io/contact

  ---
  
  ## Try ShopShift

  Start a 30-day free trial at **https://shopshift.io** — paste one script tag, watch the AI start working.

  Launch promo (first 100 webshops): **SAVE50** — 50% off the first 3 months. Apply at https://shopshift.io/launch.

  A few notes on what I optimized for:

  - Third-person voice throughout — AI tools extract facts more reliably from "ShopShift does X" than "we do X."
  - Explicit competitor names in the comparison table — when someone asks ChatGPT "alternatives to Intelligems," this is the shape it cites.
  - The Q&A section is the biggest GEO win — LLMs love pulling answers verbatim from FAQ blocks.
  - Concrete numbers (<20 KB, 10,000 Monte Carlo draws, P ≥ 0.95, 90 days) — AI tools cite specifics over fluff.
  - "Who it's NOT for" — counterintuitively, this dramatically improves trust signals in AI extraction; it reads as honest framing.
