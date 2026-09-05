# App Store Optimization (ASO) Prompt

A copy-pasteable prompt for optimizing store listing text — title, subtitle, keywords, and description — for discoverability and conversion, not just policy compliance. This is the business/growth side of store metadata: given a target keyword list, competitor context, and your current listing, it proposes optimized copy and explains the reasoning.

This guide is **not**:

- a policy compliance checker — always run the output through [metadata.md](metadata.md) before submitting, since a keyword-optimized title can still violate Apple's or Google's rules
- a keyword research tool — you still need real search-volume or competitor data (App Store Connect, Google Play Console, or a third-party ASO tool) to feed it good input
- a guarantee of ranking or conversion improvement — ASO claims should be tested with real A/B experiments (Apple's Product Page Optimization, Google's Store Listing Experiments), not assumed from a single AI pass

---

## 1. When to use this

- You're writing a new store listing and want more than a plain, unoptimized description.
- You're refreshing an existing listing and want to know which words are pulling their weight.
- You want a few conversion-rate-optimized variants to test in Product Page Optimization or Store Listing Experiments, rather than a single option you're guessing at.

## 2. The prompt

Populate `<current_listing>` and `<optimization_target>` with your actual data, then run the whole block.

```markdown
<system_instructions>
You are an App Store Optimization (ASO) specialist. Your job is to optimize store listing
copy for discoverability (organic search ranking) and conversion (browse-to-install rate),
while staying inside each platform's character limits and content policies.

For each platform in scope, produce:
1. An optimized title/name, subtitle/short description, and (for iOS) keyword field.
2. A rewritten full description, front-loading the highest-value keywords in the first
   1-2 lines (both stores truncate the description in search results and on the listing page).
3. A short rationale for each change — which keyword it targets, and why the previous
   version was weaker.
4. Two additional variants for the title/subtitle suitable for A/B testing via Apple's
   Product Page Optimization or Google's Store Listing Experiments, each with a one-line
   hypothesis for what it tests (e.g. "leads with the core benefit instead of the category").

Do not invent claims the app doesn't support (ratings, awards, "#1" rankings, feature
claims). Do not repeat the same keyword more than natural language allows — keyword
stuffing gets flagged by both platforms' review processes as well as by users.

Character limits to respect:
- iOS: Title 30, Subtitle 30, Keywords field 100 (comma-separated, no spaces after commas
  to save characters), Description 4000.
- Android: Title 30, Short description 80, Full description 4000.
</system_instructions>

<optimization_target>
- Platform(s): <iOS | Android | Both>
- App category: <e.g. Finance, Health & Fitness>
- Primary keywords (ranked by importance): <keyword1, keyword2, keyword3, ...>
- Known competitor apps (for differentiation, not for using their names as keywords): <list>
- Anything you can legitimately claim (real ratings, real awards, real press): <list, or "none">
</optimization_target>

<current_listing>
- Title / App name: <CURRENT_TITLE>
- Subtitle / Short description: <CURRENT_SUBTITLE>
- Keywords (iOS only): <CURRENT_KEYWORDS>
- Full description: <CURRENT_DESCRIPTION>
</current_listing>
```

## 3. Example

**Input:**

```text
Platform: iOS
Category: Finance
Primary keywords: budgeting, expense tracker, savings goals, spending insights
Competitors: Mint, YNAB
Legitimate claims: 4.8 rating, 50,000 downloads

Current title: MoneyWise
Current subtitle: Track your money
Current keywords: budget,money,finance,tracker,save,spend,expenses,wallet
Current description: MoneyWise helps you manage your money. Track spending. Set goals. Simple and easy to use.
```

**Output (abridged):**

```markdown
## Optimized listing

- **Title**: `MoneyWise: Budget & Expense Tracker` (36 chars — over limit, see alternate below)
- **Title (fits 30)**: `MoneyWise Budget Tracker` (24 chars)
- **Subtitle**: `Savings goals & spend insights` (30 chars)
- **Keywords**: `budgeting,expense,tracker,savings,goals,spending,insights,finance,wallet` (74 chars)
- **Description opening line**: "Track every expense, set real savings goals, and see exactly where your money goes — rated 4.8 by 50,000+ users."

### Rationale
- Original title had no keywords at all — added "Budget Tracker" since that's the highest-value
  search term in your list and wasn't present anywhere in the listing.
- "expense tracker" and "savings goals" moved from buried in the description to the
  keywords field and subtitle, where they actually influence search ranking.
- Removed "wallet" from primary keyword placement — lower relevance to your stated category
  than the other four terms; kept it once in the keyword field for coverage, not repeated.
- Did not use "Mint" or "YNAB" anywhere — referencing competitor names in metadata risks
  an Apple Guideline 2.3.7 rejection.

### A/B test variants (for Product Page Optimization)
1. **Variant A** — leads with category term: `Budget & Expense Tracker` — tests whether a
   generic category-first title outperforms a branded one for cold search traffic.
2. **Variant B** — leads with outcome: `MoneyWise: Hit Your Savings Goals` — tests whether an
   outcome-focused title converts better than a feature-focused one.
```

## 4. Before you trust this output

- [ ] Run the result through [metadata.md](metadata.md) — an ASO-optimized title can still violate character limits or content policy.
- [ ] Every claim in the output (ratings, download counts, awards) is something you can actually verify — the model has no way to check this itself.
- [ ] No competitor brand names ended up in keywords or description text.
- [ ] You have a real plan to test the A/B variants — Apple's Product Page Optimization or Google's Store Listing Experiments — rather than just shipping the "optimized" version untested.
- [ ] The keywords chosen are backed by actual search/competitor data, not just the model's guess at what's popular.

---

## Official sources

- Apple Product Page Optimization: https://developer.apple.com/app-store/product-page-optimization/
- App Store Connect Analytics — Product Page Optimization: https://developer.apple.com/help/app-store-connect-analytics/acquisition/product-page-optimization/
- Google Play Store listing experiments: https://play.google.com/console/about/store-listing-experiments/
- Apple App Store Review Guidelines (Guideline 2.3, accurate metadata): https://developer.apple.com/app-store/review/guidelines/#accurate-metadata

**Last verified:** September 5, 2026

---

## Related documentation

### Prompts

- `ai/prompts/README.md`
- `ai/prompts/metadata.md`

### Agents and workflows

- `ai/agents/metadata-agent.md`
- `ai/workflows/metadata-generation.md`

### Store operations and publishing

- `store-operations/metadata.md`
- `publishing/cross-platform/metadata.md`
- `templates/app-store-description.md`
- `templates/play-store-description.md`
