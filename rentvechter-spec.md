# RentVechter — PRD (v1)

**Status:** Draft
**Owner:** Ty
**Last updated:** 28 July 2026

---

## 1. Overview

RentVechter is a web tool that lets Dutch renters check whether their rent is legal under the WWS (Woningwaarderingsstelsel) points system. Users enter details about their home; the tool returns a points total, the estimated legal maximum rent, and — if they're overpaying — plain-language next steps for getting it corrected. V1 is a single, stateless calculator: no accounts, no backend, no data storage.

## 2. Problem statement

Since the Wet betaalbare huur took effect on 1 July 2024, the WWS is legally binding for the social and mid-rent segments — landlords must keep to the maximum rent that matches a home's points total, not just treat it as a guideline [1]. Since 1 January 2025, landlords must also hand new tenants a printed points calculation with the lease, covering every segment including the free sector, with municipal fines up to €25,750 for non-compliance [2]. Despite this, the system — twelve scoring categories, an energy-label table, a WOZ-value formula with a cap, monument surcharges — is complex enough that almost no renter checks it themselves. The gap between what's charged and what's legally allowed often runs into hundreds of euros a month, and most renters have no way to know it's happening.

## 3. Goals

- Give a renter a trustworthy points total and max-rent estimate in under 3 minutes, without creating an account.
- Make clear, correctly, whether they're likely overpaying and by how much.
- Point them to a concrete, correct next action (Huurcommissie route) rather than leaving them with just a number.
- Establish a top-of-funnel entry point for later RentVechter features (letter generation, case tracking, landlord reviews).

### Non-goals (v1)

- Being a substitute for the Huurcommissie's own binding calculation.
- Covering every WWS edge case (zorgwoningen, detailed common-area scoring, garage/parking nuance) — these are flagged as "not included," not guessed at.
- Any account system, saved history, or database.
- Apartment listings/aggregation, landlord reviews, roommate matching, letter generation — all v2+ candidates.

## 4. Success metrics

- % of sessions that complete the form and reach a result (completion rate).
- % of results flagged as "overpaying" where the user clicks through to Huurcommissie / next-steps content (intent to act).
- Self-reported or measured calculator accuracy vs. official Huurcommissie Huurprijscheck outcomes, for a sample of real cases (target: within a few euros, matching the accuracy already validated against two official worked examples — see §8).
- Since v1 has no accounts, success will initially be inferred from anonymous usage analytics (e.g. Plausible/Fathom) rather than retention.

## 5. Target users

A renter in the Netherlands — often an internationally-relocated professional, a student, or a first-time renter — who has signed a lease or is about to sign one, doesn't know Dutch rental law, and wants a fast, concrete answer to "am I / will I be overpaying, and what can I do about it?"

## 6. Scope

### In scope (v1)

| # | Requirement | Description |
|---|---|---|
| R1 | Points calculator | Form covering the categories that drive most real point totals: room surface area, other spaces (storage/attic), heating, energy label (by dwelling type), kitchen, sanitary, outdoor space, WOZ value (with the WOZ-cap rule applied), and monument status [3]. |
| R2 | Max legal rent estimate | Converts the point total into a segment (social / mid-rent / free sector) and an estimated maximum kale huur (bare rent), using the official 2026 price bands [4]. |
| R3 | Overcharge check | User enters what they currently pay (or are being asked to pay); the app flags whether it's above the legal max and by how much, monthly and annualized. |
| R4 | Next-steps guidance | Explains what to do depending on situation: within 6 months of lease start → toetsing aanvangshuurprijs (retroactive); longer tenancy in a regulated segment → send a rent-reduction proposal first. Includes actual Huurcommissie costs and process [5]. |
| R5 | Accuracy disclaimer | States clearly that results are an estimate for orientation, not a legal ruling, and links to huurcommissie.nl / MijnHuurcommissie for the binding calculation and to file a real complaint. |
| R6 | Letter generator | When the overcharge check flags overpaying, auto-drafts an editable letter pre-filled with the user's numbers: a rent-reduction proposal to the landlord for established tenancies (≥6 months), or a starting-rent notice plus a Huurcommissie filing summary for new tenancies (<6 months) [8][9]. Includes copy, download (.txt), and print/save-as-PDF actions. |

### Out of scope (v1)

- Accounts, saved history, or a database (stateless calculator only).
- Landlord reviews, apartment listings/aggregation, roommate matching.
- Auto-generating/sending a legal complaint or letter to the landlord (v2 candidate).
- Full coverage of edge-case categories (zorgwoningen, detailed common-area scoring, garage/parking nuance).
- Non-Dutch-market rent regulation.

## 7. Functional requirements (detail)

1. **Input form** collects: dwelling type (single-family vs. multi-family/duplex), room and other-space surface area, heated rooms/spaces, energy label, kitchen counter length and extras, sanitary fixture counts, private/shared outdoor space, WOZ value, monument status, current rent, and tenancy length.
2. **Calculation engine** applies, in order: base surface-area points, other-space points, heating points, energy-label points (by dwelling type) [6], kitchen points, sanitary points, outdoor-space points (capped at 15), WOZ points via the two-part formula with the €85,806 floor, and the WOZ-cap rule at ≥187 points [3].
3. **Segment/price mapping** applies the 2026 price bands and, where applicable, the monument surcharge (stacked if more than one applies) [7].
4. **Output** shows: total points, segment badge, estimated max rent, difference vs. current rent, a plain-language verdict, a numbered next-steps list conditioned on tenancy length, a full points breakdown, and the accuracy disclaimer.
5. **Letter generator** (R6): collects tenant name, property address, landlord/agency name and address, and lease start date; computes a legally compliant proposed effective date for rent-reduction proposals (at least two full calendar months after the send date) [8]; computes the 6-month toetsing aanvangshuurprijs filing deadline from the lease start date and flags it in the UI, falling back automatically to the established-tenancy letter if that deadline has already passed; and renders an editable draft with copy/download/print actions. The new-tenancy draft explicitly notes that the Huurcommissie's official filing requires a copy of the lease contract and treats the letter as supporting evidence, not the filing itself [9]. The disclaimer recommends sending by registered post or email with a read receipt and keeping a copy [10].

## 8. Non-functional requirements

- **Accuracy:** calculation logic must be traceable to a cited primary source for every category; before shipping any change, validate against known official worked examples (already done for two cases: 147 points → €960.33; 169 points → €1,111.39, both reproduced within a few cents — see §10).
- **No data retention:** all inputs stay client-side; nothing is transmitted or stored.
- **Accessibility/clarity:** form must be usable by non-Dutch speakers; Dutch legal terms (kale huur, WOZ-waarde, puntentelling) are kept alongside English labels since they appear on official documents users will encounter.
- **Maintainability:** price bands and point values are indexed annually (next known change: 1 January 2027) and must be easy to update in one place.

## 9. Roadmap after v1

- **v1.1 (in progress):** ~~downloadable/copyable rent-reduction letter template~~ — moved into v1 scope as R6 (§7).
- **v1.2:** save/share a calculation via a link (still no login).
- **v2:** accounts, tracking a live Huurcommissie case, energy-label lookup by address (via public registers) to reduce manual input.
- **v3:** landlord/building reputation layer, community data on typical overcharging by city/postcode.

## 10. Key facts and data dependencies (2026, subject to annual indexation)

| Fact | Value | Source |
|---|---|---|
| WWS binding since | 1 July 2024 (Wet betaalbare huur) | [1] |
| Landlord must provide points breakdown | Since 1 Jan 2025, all segments; fines up to €25,750 | [2] |
| Social segment | up to 143 points, max €932.93/month | [4] |
| Mid-rent segment | 144–186 points, €939.73–€1,228.07/month | [4] |
| Free sector | 187+ points, no statutory maximum | [4] |
| Energy label swing | +62 pts (A++++, single-family) to −15 pts (G) | [6] |
| WOZ points formula | (WOZ ÷ €16,954) + ((WOZ ÷ m²) ÷ €268); minimum WOZ used is €85,806 | [3] |
| WOZ-cap rule | At ≥187 points, WOZ points capped at 33% of total; if capping drops total below 187, it's fixed at 186 | [3] |
| Monument surcharge | Rijksmonument +35%; municipal/provincial monument +15%; protected townscape (pre-1965) +5% — stacked if more than one applies | [7] |
| Huurcommissie cost | Tenant €25 (refunded if you win); landlord €500, rising to €700/€1,400/€1,750 on repeat losses within 4 years | [5] |
| Toetsing aanvangshuurprijs window | 6 months from lease start, retroactive if you win | [5] |

Note: exact energy-label point values and worked point-to-price examples used to validate the MVP calculator were cross-checked against a secondary source (rekenmachinepro.nl, 14 July 2026) that cites the same primary Huurcommissie beleidsboek and price table; two of its worked examples (147 points → €960.33; 169 points → €1,111.39) were independently reproduced by the calculator logic to within a few cents.

## 11. Open questions / risks

- Annual re-indexation (points-to-price bands, WOZ floor) needs a maintenance owner and a yearly check-in; if missed, the tool will silently give wrong figures.
- The linear price-per-point formula is an approximation of the official table, not the table itself — worth eventually replacing with the exact Bijlage 3 values per point if precision becomes a differentiator.
- No legal review has been done; the disclaimer mitigates but doesn't eliminate liability exposure if a user acts on a wrong estimate.

## 12. Sources

1. Volkshuisvesting Nederland (Ministerie van BZK) — "Dwingend maken WWS": https://www.volkshuisvestingnederland.nl/onderwerpen/huren-en-wonen/wet-betaalbare-huur/hoe-werkt-de-wet-betaalbare-huur/dwingend-maken-wws
2. Huurcommissie — "Wettelijke wijzigingen per 1 januari 2025 om rekening mee te houden" (19 Dec 2024): https://www.huurcommissie.nl/actueel/nieuws/2024/12/19/wettelijke-wijzigingen-per-1-januari-2025-om-rekening-mee-te-houden
3. Huurcommissie — Beleidsboek, Hoofdstuk 2, "Het woningwaarderingsstelsel voor een zelfstandige woning" (categories, WOZ formula and cap): https://www.huurcommissie.nl/support/beleidsboeken/waarderingsstelsel-zelfstandige-woonruimte/algemene-toelichting
4. Volkshuisvesting Nederland — "Maximale huurprijsgrenzen" (2026 price bands, indexed 1 Jan 2026): https://www.volkshuisvestingnederland.nl/onderwerpen/huren-en-wonen/inkomensgrenzen-huurprijsgrenzen-en-huurtoeslagparameters/maximale-huurprijsgrenzen; Huurcommissie Bijlage 3 (full price table): https://www.huurcommissie.nl/support/beleidsboeken/waarderingsstelsel-zelfstandige-woonruimte/bijlage-3-maximale-huurprijsgrenzen-voor-zelfstandige-woningen
5. Huurcommissie — "Wat kost een procedure?": https://www.huurcommissie.nl/onderwerpen/procedure/procedure-starten/kosten
6. Rijksoverheid — "Welke invloed heeft het energielabel op de huurpunten van mijn woning?": https://www.rijksoverheid.nl/onderwerpen/woning-huren/vraag-en-antwoord/welke-invloed-heeft-het-energielabel-op-de-huurpunten-van-mijn-woning
7. Volkshuisvesting Nederland — "Aanpassingen van het WWS" (monument surcharge rules): https://www.volkshuisvestingnederland.nl/onderwerpen/huren-en-wonen/wet-betaalbare-huur/hoe-werkt-de-wet-betaalbare-huur/wws-aanpassingen
8. Huurcommissie — Modelbrief "Voorstel tot huurverlaging op grond van puntenaantal" (required letter content: current rent, proposed rent, points count, proposed effective date at least two months out): https://www.huurcommissie.nl/documenten/modelbrief/12/14/voorstel-tot-huurverlaging-op-grond-van-puntenaantal
9. Huurcommissie — "Verlaging van de aanvangshuurprijs" (toetsing aanvangshuurprijs process; lease contract required, written landlord proposal optional): https://www.huurcommissie.nl/onderwerpen/zaken-door-huurder-huurprijs-en-huurverhoging/verlaging-aanvangshuurprijs-h
10. Het Juridisch Loket — "Voorbeeldbrief verzoek om huurverlaging" (send by post/email, use registered mail if no response, keep a copy and proof of delivery): https://www.juridischloket.nl/voorbeeldbrieven/voorbeeldbrief-verzoek-huurverlaging/
