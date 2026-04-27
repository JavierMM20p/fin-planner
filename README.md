# fin-planner

A browser-based net worth simulator and financial planner. No backend, no installation, no data leaves your machine — everything runs in a single HTML file.

Live at: https://javierMM20p.github.io/fin-planner

---

## Overview

fin-planner lets you model long-term wealth accumulation and withdrawal strategies with a high degree of precision. It combines compound-growth projections, inflation adjustment, Monte Carlo simulation, and Spanish tax law into a single interactive tool.

It is aimed at individual investors who want to go beyond simple savings calculators and understand the real after-tax, inflation-adjusted trajectory of their portfolio under realistic market uncertainty.

---

## Features

### Portfolio modelling

- **Initial capital and monthly contributions** — set a starting balance and a regular contribution amount.
- **Contribution growth** — optionally increase contributions at a fixed annual rate (e.g. to track salary growth).
- **Multi-bucket asset allocation** — split the portfolio across any number of named buckets, each with its own expected return, volatility, and asset type (equity, bond, cash, pension).
- **Automatic allocation normalisation** — bucket weights are normalised to 100%, so you can enter raw percentages without having to ensure they sum exactly.
- **Annual rebalancing** — optionally rebalance allocations at year-end, which triggers taxable realisation events in the model.

### Inflation

- Toggle inflation adjustment on or off.
- Configurable annual inflation rate.
- Display modes: nominal only, real only, or both overlaid on the same chart.

### Monte Carlo simulation

- Runs N independent paths (default 400, configurable up to 5,000) using log-normal return draws.
- Per-bucket volatility drives the stochastic returns; a Mulberry32 seeded PRNG ensures reproducible results.
- Displays P10, P50, and P90 percentile bands on the chart.
- Optional overlay of the five worst paths, useful for visualising sequence-of-returns risk.
- Summary statistics: success rate, median outcome, worst drawdown, and median depletion year among failed paths.

### FIRE solver

- Accumulation mode: manually configure a withdrawal amount and start year.
- FIRE mode: specify a target monthly withdrawal (real terms) and a target duration, then let the solver find the earliest year the portfolio can sustain that withdrawal without depletion.

### Expenses

- **One-time expenses** — a lump-sum cost applied at a specific month.
- **Recurring expenses** — a regular cost applied over a range of months, with an optional inflation-indexing toggle.

### Spanish tax engine

**Capital-gains tax (IRPF — Ahorro)**

The simulator applies Spain's tiered savings-income tax brackets to realised gains on each withdrawal:

| Gain band | Rate |
|---|---|
| 0 – 6,000 € | 19% |
| 6,001 – 50,000 € | 21% |
| 50,001 – 200,000 € | 23% |
| 200,001 – 300,000 € | 27% |
| > 300,000 € | 30% |

Gains are tracked cumulatively within each tax year to apply the correct marginal rate incrementally.

**Pension contributions**

Pension-bucket contributions qualify for income tax deduction, capped at €1,500/year (2026 individual-plan limit). The annual refund is computed at the user's marginal income tax rate and credited back to the portfolio. Pension withdrawals are taxed as ordinary income rather than capital gains.

**FIFO lot tracking**

When enabled, each buy creates a discrete lot with its own cost basis. Sells consume the oldest lots first (FIFO), so the gain reported for each sale is the difference between the sale proceeds and the oldest lot's purchase price — consistent with how Spanish brokers report gains for tax purposes. FIFO can be disabled in favour of proportional cost-basis averaging.

**Wealth tax (Impuesto sobre el Patrimonio)**

Optional annual wealth tax using Spain's state-default progressive brackets, with a configurable personal exemption (default €700,000). Tax can be paid from the portfolio itself or modelled as an external cost.

### Scenarios

- Save, load, and delete named scenarios stored in `localStorage`.
- Overlay multiple saved scenarios on the chart simultaneously for comparison.
- Export any scenario to a JSON file and import it back later.
- Generate a shareable URL that encodes the current configuration in the query string — recipients open the same state without needing an account.

### Output

- **Interactive Chart.js chart** — nominal and real portfolio value over time, with Monte Carlo bands when enabled.
- **Key statistics panel** — final balance, total contributed, total withdrawn (net), and total taxes paid.
- **Year-by-year breakdown table** — expandable table showing annual snapshots; exportable as CSV.

### Internationalisation

The full interface is available in English and Spanish. Language is toggled with a single button; the selection persists across sessions via `localStorage`.

### Appearance

Light and dark themes, toggled with a button in the top-right corner. The selected theme persists across sessions.

---

## Usage

Open `index.html` in any modern browser. No build step, no server, no dependencies to install — Chart.js and the Google Fonts are loaded from CDN.

To use offline, download the file and substitute local copies of Chart.js and the fonts, or remove the font import to fall back to system fonts.

---

## Configuration reference

| Field | Description |
|---|---|
| Initial capital | Starting portfolio value in euros |
| Monthly contribution | Regular monthly addition to the portfolio |
| Contribution growth | Annual percentage increase in monthly contribution |
| Years | Simulation horizon |
| Asset buckets | Name, allocation weight, expected annual return, annual volatility, type |
| Rebalance annually | Whether to rebalance to target weights at year-end (triggers tax events) |
| Adjust for inflation | Whether to deflate portfolio values to real terms |
| Annual inflation | Expected annual CPI rate |
| Monte Carlo runs | Number of stochastic paths |
| Seed | Integer seed for the PRNG; same seed gives identical Monte Carlo output |
| Show worst 5 paths | Overlay the five paths with the worst final outcome |
| Mode | Accumulation or FIRE solver |
| Monthly withdrawal | Amount to withdraw per month (accumulation mode) |
| Withdrawal start year | Year at which withdrawals begin |
| Keep withdrawal real | Inflate the nominal withdrawal amount to preserve real purchasing power |
| FIRE target withdrawal | Target real monthly withdrawal for the FIRE solver |
| FIRE duration | Minimum number of years the portfolio must survive after FIRE |
| Capital-gains tax | Toggle Spanish IRPF savings-income tax on withdrawals |
| FIFO lot tracking | Use FIFO cost basis; disable for proportional average cost basis |
| Marginal income tax rate | Used for pension deduction refunds and pension withdrawal tax |
| Wealth tax | Toggle annual Spanish Impuesto sobre el Patrimonio |
| Wealth tax exemption | Exemption threshold before wealth tax applies |

---

## Technical notes

- The simulation runs entirely in the browser using vanilla JavaScript — no frameworks, no build toolchain.
- The Mulberry32 algorithm is used as the seeded PRNG for Monte Carlo paths; Box-Muller transform converts uniform samples to correlated normal pairs for log-normal return draws.
- State is serialised to `localStorage` on each run and can be encoded in a URL query string for sharing.
- The tax engine is intentionally simplified for planning purposes and does not constitute financial or legal advice. Tax law changes frequently; verify current brackets before making decisions.

---

## License

MIT
