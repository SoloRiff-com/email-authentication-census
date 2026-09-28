# Email authentication by industry and company size

Who actually authenticates their email: SPF and DMARC adoption measured over public
DNS across **13,635 company domains** in **25 industries**, broken down by employee
count.

Published by [SoloRiff](https://soloriff.com). Source page, with the interactive
table and the full method: <https://soloriff.com/data/email-authentication>

Measured **2026-09-03**. Refreshed monthly.

## The headline

Adoption tracks company size far more strongly than it tracks industry.

| Company size | Publish SPF | Publish DMARC | **Enforce DMARC** |
|---|---|---|---|
| 1–10 | 88.2% | 61.7% | **21.4%** |
| 11–50 | 92.1% | 74.6% | **32.1%** |
| 51–200 | 93.8% | 86.2% | **47.3%** |
| 201–1,000 | 96.9% | 89.6% | **61.1%** |
| 1,000+ | 97.9% | 95.5% | **79.5%** |

58 points between the smallest companies and the largest. Weighted by sample size
across all 25 industries.

"Enforce" means a DMARC policy of `quarantine` or `reject`. A policy of `p=none` is
counted as publishing DMARC but not as enforcing it: it reports failures and stops
nothing. Across the whole sample, 81.7% publish DMARC and 48.4% enforce it — a third
of all companies have a DMARC record that does nothing.

## Files

| File | What it is |
|---|---|
| `email-authentication-census.csv` | 122 rows — one per industry × size band |
| `email-authentication-census.json` | The same rows plus the method and a column dictionary |

## Columns

| Column | Meaning |
|---|---|
| `industry` | Industry slug |
| `industry_label` | Industry, human readable |
| `size_band` | `1-10`, `11-50`, `51-200`, `201-1000`, `1001-plus` |
| `sample_size` | Company domains measured in this cell |
| `measured_at` | Date of the DNS measurement (ISO 8601) |
| `spf_pct` | % publishing any SPF record |
| `spf_all_open_pct` | % whose SPF record ends in `+all`, which authorises any sender |
| `dmarc_pct` | % publishing any DMARC record, including `p=none` |
| `dmarc_enforced_pct` | % with a DMARC policy of `quarantine` or `reject` |
| `no_dmarc_pct` | % publishing no DMARC record at all |
| `mx_pct` | % with an MX record (100 by construction — see method) |

## Method

One domain per company, deduplicated. Domains are sampled within employee-count
bands for each industry, then queried over public DNS:

- `TXT` at the apex for SPF
- `TXT` at `_dmarc.<domain>` for DMARC
- `MX` to establish that the domain receives mail at all

Every individual row is reproducible with `dig`.

**Domains with no MX record are excluded.** A domain that cannot receive mail is
usually parked, a redirect or a defensive brand registration rather than a company
that sends and receives email; counting those as authentication failures would drag
every percentage down and make the result an artefact of the sample.

**Cells with fewer than 60 domains are not published**, and appear as blank rather
than as a number nobody should trust.

**DKIM is not measured.** A DKIM key lives at `<selector>._domainkey.<domain>`, and
selectors cannot be enumerated from outside the domain — only guessed. A company
with flawless DKIM under a selector we did not guess would be recorded as having
none, so the figure would describe our guess list rather than the industry. Only
records published at fixed, discoverable names are measured here.

## Licence

**CC BY 4.0.** Free to reuse, including commercially, with attribution to SoloRiff
and a link to <https://soloriff.com/data/email-authentication>.

Only aggregate counts are published. The underlying company domains are never
released.

## Citation

> SoloRiff (2026). *Email authentication by industry and company size.*
> <https://soloriff.com/data/email-authentication>
