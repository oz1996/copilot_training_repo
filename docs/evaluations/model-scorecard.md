# Model scorecard — rule R1 (pensionableSalary)

> One run is a sample, not a benchmark. Synthetic data only — never paste secrets or real member data.

| | Model A | Model B |
|---|---|---|
| Model (name as shown in the picker) | Copilot Fusion | GTP-6.1 Sol|
| Harness · Mode · Permissions | Copilot · Interactive · Manual | Copilot · Interactive · Manual |
| Reasoning effort | Medium | Medium |
| Date | 2026-11-18 | 2026-11-18 |

## Scores

| Criterion | How to score | Model A | Model B |
|---|---|---|---|
| Correctness verified | Expected values that match the `node -e` output (x / 5) | 4/5 | 5/5 |
| Edge cases found | Which did it cover? franchise boundary · salary cap · part-time · below franchise → 0 · invalid factor (0 or > 1) · negative salary | franchise boundary, rounding, salary cap, invalid factor, negative salary | franchise boundary, salary cap, rounding, NaN salary, invalid factor |
| Hallucinations | Invented functions, parameters, files or rules? List them (none = 👍) | N/A | N/A |
| Latency feel | fast / ok / slow | fast | fast |
| Cost / credits | Relative cost of the model (supported-models doc) — heavier model = more AI credits | More expensive | Cheaper |

## Verification output

```text
Model A:
	node -e "import('./api/src/domain/pension.ts').then(m=>{for (const [s,f] of [[17544,1],[17545.015,1],[150000,0.5],[100000,0],[-1,1]]) console.log(s,f,m.pensionableSalary(s,f))})"
	17544 1 0 MATCHES
	17545.015 1 0.01 DOES NOT MATCHES
	150000 0.5 60127.5 MATCHES
	file:///workspaces/copilot_training_repo/api/src/domain/pension.ts:22
		throw new RangeError('partTimeFactor must be greater than 0 and at most 1'); MATCHES
			  ^
	RangeError: partTimeFactor must be greater than 0 and at most 1
		at Module.pensionableSalary (file:///workspaces/copilot_training_repo/api/src/domain/pension.ts:22:11)
		at [eval]:1:144 MATCHES

Model B:
	node -e "import('./api/src/domain/pension.ts').then(m=>{for (const [s,f] of [[17544,1],[200000,0.5],[17545.25,0.5],[NaN,1],[50000,0]]) console.log(s,f,m.pensionableSalary(s,f))})"
	17544 1 0 MATCHES
	200000 0.5 60127.5 MATCHES
	17545.25 0.5 0.13 MATCHES
	file:///workspaces/copilot_training_repo/api/src/domain/pension.ts:19
		throw new RangeError('fullTimeSalary must be a non-negative number');
			  ^

	RangeError: fullTimeSalary must be a non-negative number
		at Module.pensionableSalary (file:///workspaces/copilot_training_repo/api/src/domain/pension.ts:19:11)
		at [eval]:1:145 MATCHES

```

## Verdict

- For **this kind of task** I would use: GTP-sol 6.1
- Because: It got all the expected output right (both models tested very similar things though)
- Would I trust it **without** the `node -e` check? yes / no — why? No, because I see it can get it wrong.
- To make this a real benchmark I would need: unit tests to see that what the model outputs is actually correct