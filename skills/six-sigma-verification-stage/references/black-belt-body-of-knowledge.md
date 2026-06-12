# Six Sigma Black Belt Body Of Knowledge

This reference operationalizes the Six Sigma Black Belt PDF into an agent-usable body of knowledge. It is a paraphrased working reference, not a verbatim copy of the book.

Use this file when a task requires Six Sigma domain knowledge beyond the verification harness: project selection, DMAIC/DMADV planning, Lean analysis, process mapping, statistical reasoning, control charts, Minitab workflows, DOE, or process-improvement facilitation.

## Contents

- Unit 1: Introduction to Six Sigma
- Unit 2: Projects and Processes
- Unit 3: Advanced DMAIC
- Unit 4: Beginner Statistics
- Unit 5: Intermediate Statistics
- Unit 6: Advanced Control and Minitab
- Unit 7: Experiments
- Unit 8: Brainstorming, Process Maps, and Value Stream Mapping
- Core Formulas and Decision Rules
- Agent Quick Lookup

## Unit 1: Introduction To Six Sigma

### Six Sigma Purpose

Six Sigma is both a process-improvement methodology and a statistical way to reason about variation. Its central belief is that variation creates opportunities for error, errors create defects, and defects harm customer satisfaction, cost, and trust.

A Six Sigma agent should:

- Treat intuition as useful but insufficient.
- Require data, measurement, and validation before scaling changes.
- Focus on customer requirements and critical-to-quality outcomes.
- Reduce variation and prevent defects from reaching the customer.
- Preserve gains through controls rather than one-time fixes.

### Data-Driven Decisions

Without a measurement discipline, organizations often implement ideas because they sound reasonable, then discover consequences after customers or operations are affected. Six Sigma reverses that order:

1. Define the problem and customer need.
2. Measure current performance.
3. Analyze causes.
4. Test or pilot solutions.
5. Control the improved process.

In software and AI projects, translate this as:

- Do not ship a feature only because the implementation works locally.
- Define observable quality signals before implementation.
- Test high-risk assumptions with data, logs, UAT, evals, or pilots.
- Monitor after release so regression is detectable.

### Sigma Level, Yield, And DPMO

Sigma level communicates how often a process produces defects relative to opportunities for defects. Six Sigma performance is commonly associated with 3.4 defects per million opportunities.

Use sigma thinking to compare process capability, but do not prioritize by sigma level alone. Also consider customer impact, cost of poor quality, feasibility, compliance, and effort.

Useful terms:

- `Opportunity`: a chance for a defect to occur.
- `Defect`: a failure to meet a defined requirement.
- `Yield`: proportion of outputs that meet requirements.
- `DPMO`: defects per million opportunities.
- `Sigma level`: statistical performance level associated with yield or DPMO.

### Common Six Sigma Principles

- Customer focus: Start from what customers or stakeholders need.
- Value stream thinking: Understand the full sequence required to deliver value.
- Variation reduction: Make outcomes more consistent.
- Waste removal: Remove steps that consume resources without adding value.
- Process control: Sustain improvements after implementation.
- Continuous improvement: Treat each improvement as part of an ongoing system.

### Challenges And Failure Modes

Six Sigma work commonly fails because of:

- Weak leadership support.
- Insufficient resources or training.
- Poor project execution.
- Invalid, missing, delayed, or inaccessible data.
- Resistance to change.
- Applying manufacturing examples too literally to service, software, or knowledge work.

Agent response:

- Check executive/process-owner support before major change plans.
- Look for missing data-capture steps.
- Translate manufacturing concepts into the project domain instead of forcing factory language.
- Prefer low-risk, high-reward first improvements when adoption is fragile.

### History And Application

Six Sigma draws from statistical process control, continuous improvement, Lean manufacturing, Motorola-style defect reduction, and later enterprise process-improvement adoption.

Important lineage:

- Shewhart: statistical process control and control charts.
- Deming: plan-do-check-act and quality culture.
- Toyota/Lean: flow, waste reduction, value stream thinking.
- Motorola: defects-per-opportunity focus and sigma-level quality.
- Enterprise adoption: project governance, champions, belts, tollgates, and financial linkage.

### Other Quality Methods

Six Sigma can coexist with quality and improvement methods such as TQM, Lean, ISO-style quality management, PDCA, business process management, and project management. Use Six Sigma when the problem benefits from measured defect reduction, variation reduction, root-cause analysis, or control planning.

Avoid Six Sigma when:

- The solution is obvious, tiny, and low-risk.
- The data burden outweighs the value.
- The issue is primarily strategic rather than process-based.
- The organization cannot define the defect or customer requirement.

### Lean Concepts

Lean focuses on value, flow, and waste removal. In the skill, Lean tools support Six Sigma by making processes visible and reducing non-value-added work.

Classic waste categories:

- Transportation: unnecessary movement of materials, work, data, or people.
- Inventory: excess work-in-progress, stock, tickets, or backlog.
- Motion: unnecessary human movement, context switching, or tool switching.
- Waiting: idle time caused by queues, dependencies, approvals, or slow systems.
- Overproduction: producing more or earlier than needed.
- Overprocessing: extra work that does not improve customer value.
- Defects: rework, scrap, escaped bugs, incidents, or incorrect outputs.

Other waste forms to consider:

- Underused talent.
- Unclear communication.
- Task switching.
- Excessive handoffs.
- Automation that hides rather than removes waste.

### Two Types Of Muda

- Necessary non-value-added work: required by compliance, safety, accounting, legal, or platform constraints.
- Pure waste: removable work with no meaningful customer, business, or compliance value.

Do not delete required controls simply because they look slow. Replace them only when the process can preserve quality, safety, and compliance.

### 5S

5S creates orderly work environments:

- Sort: remove what is unnecessary.
- Set in order: place needed items where they are easy to use.
- Shine: clean and maintain the environment.
- Standardize: make the desired state repeatable.
- Sustain: audit and preserve the standard.

For software work, 5S maps to removing unused code/config, organizing docs and dashboards, keeping environments reproducible, standardizing workflows, and maintaining those standards.

### Just-In-Time

Just-in-time reduces inventory and queue waste by producing or supplying work only when needed. In project planning, use JIT cautiously: it improves flow, but it requires reliable suppliers, stable demand signals, and fast detection of disruptions.

### Basic Statistical Concepts

Standard deviation describes spread around an average. Six Sigma uses spread to reason about process variation and the likelihood that outputs fall outside requirements.

Key ideas:

- Smaller variation usually means more predictable quality.
- A stable process can still be incapable if its output does not meet customer specifications.
- Capability requires both control and alignment with requirements.

### Pareto Principle

The Pareto principle suggests that a small number of causes often create a large share of defects or costs. Use Pareto charts or ranked defect analysis to focus effort where it can matter most.

Agent use:

- Ask which defects drive the most customer harm or cost.
- Avoid treating all findings equally.
- Recommend ranking defects before selecting solutions.

### Voice Of The Customer

Voice of the Customer captures stated and implied customer needs. Sources include surveys, interviews, complaints, support tickets, behavioral data, market data, stakeholder interviews, and subject-matter experts.

Translate VoC into CTQs:

1. Capture the need.
2. Identify quality drivers.
3. Convert drivers into measurable requirements.
4. Validate requirements with customer data, operational data, and subject-matter expertise.

### Basic Metrics

Common process metrics:

- Defects.
- Defect rate.
- Yield.
- Cycle time.
- Lead time.
- Throughput.
- WIP.
- Cost per defect.
- Rework rate.
- First-pass yield.
- Customer satisfaction or complaint rate.

For any metric, define:

- Numerator.
- Denominator.
- Time window.
- Data source.
- Owner.
- Collection method.
- Decision threshold.

### Problem Function: y = f(x)

Six Sigma frames outcomes as functions of inputs:

- `Y`: output, defect, quality characteristic, or business outcome.
- `X`: input, cause, factor, process variable, or controllable condition.

The improvement task is to identify which `X` variables materially influence `Y`, then control or optimize them.

### 5 Whys

The 5 Whys method repeatedly asks why a problem occurs until a more actionable root cause emerges.

Rules:

- Ground each why in evidence.
- Stop when the cause is controllable or testable.
- Avoid blaming people; examine process conditions.
- Confirm the chain with data or observation.

### Problem Statement

A strong problem statement names:

- What is wrong.
- Where it occurs.
- Who or what is affected.
- Magnitude or baseline.
- Time period.
- Business/customer impact.
- What is in scope.

Avoid embedding the solution in the problem statement.

## Unit 2: Projects And Processes

### Process Basics

A process transforms inputs into outputs through activities, decisions, resources, people, systems, and controls. Six Sigma improves processes by making those elements explicit.

Process components:

- Suppliers.
- Inputs.
- Process steps.
- Outputs.
- Customers.
- Process owner.
- Metrics.
- Data sources.
- Controls.

### Process Owners

The process owner is accountable for ongoing process performance after the project team leaves. Verification should fail or become conditional if no owner can sustain the improvement.

### Data In Processes

Data supports process understanding, baseline measurement, defect analysis, root-cause analysis, and control. Data risks include missing metrics, manual-entry errors, inconsistent definitions, delayed access, skew, and compliance restrictions.

### SIPOC

SIPOC maps a process at a high level:

- Suppliers.
- Inputs.
- Process.
- Outputs.
- Customers.

Use SIPOC early when scope is unclear, stakeholders disagree about boundaries, or the agent needs to identify customer/output relationships.

SIPOC tips:

- Keep the process at 5-7 major steps.
- Start with the process and outputs if suppliers are unclear.
- Include internal and external customers.
- Use SIPOC outputs as candidates for CTQ analysis.

### Quality

Quality means satisfying stated, implied, or obligatory requirements. Requirements can come from:

- Customers.
- Regulators.
- Brand standards.
- Internal process owners.
- Safety or security expectations.
- Contracts or SLAs.

An agent should verify whose requirement is being optimized. Customer quality, compliance quality, and operational convenience are not always the same.

### Critical To Quality

CTQs convert customer or stakeholder needs into measurable process requirements. CTQs should be specific enough to drive measurement and control.

CTQ tree:

1. Customer need.
2. Quality drivers.
3. Measurable requirements.

Quality check:

- If the requirements are met, will the customer be satisfied?
- Are the requirements measurable?
- Are the requirements realistic?
- Do they match VoC and subject-matter expertise?

### Cost Of Quality And Cost Of Poor Quality

Cost of poor quality includes internal and external failure costs.

- Internal failures: scrap, rework, reprocessing, delays, repeated testing, extra reviews before delivery.
- External failures: returns, refunds, warranty claims, support burden, incidents, churn, reputation loss, compliance penalties.

Cost of quality also includes prevention and appraisal costs.

- Prevention: training, quality planning, requirements management, design reviews, mistake-proofing.
- Appraisal: audits, inspections, tests, reviews, surveys, certifications.

Six Sigma aims to build quality into the process, reducing failure costs and avoiding unnecessary appraisal layers.

### Project Selection

Good Six Sigma projects:

- Matter to customers or business outcomes.
- Have measurable defects or variation.
- Are scoped tightly enough to complete.
- Have available data or a plausible data plan.
- Have sponsor and process-owner support.
- Provide benefits that justify effort.

Avoid projects that are too broad, politically unsupported, data-starved, trivial, or already solved.

Enterprise selection often weighs:

- Strategic alignment.
- Customer impact.
- Cost of poor quality.
- Feasibility.
- Resource availability.
- Risk.
- Time to benefit.

### Team Management

Typical Six Sigma roles:

- Sponsor or champion: secures support, removes barriers, approves gates.
- Black Belt: leads method, analysis, and project execution.
- Green Belt/team members: contribute process knowledge and improvement work.
- Subject-matter experts: provide operating reality.
- Process owner: receives and sustains the improved process.

Plan verification should check team authority, decision rights, timeline, budget, communication, and escalation paths.

### Timelines, Scheduling, And Budgets

Six Sigma project plans should include:

- Phase milestones.
- Tollgate review dates.
- Data-collection windows.
- Analysis and pilot time.
- Training/documentation work.
- Transition and control-plan setup.
- Budget for labor, tools, training, software, equipment, and disruption.

### DMAIC And DMADV

Use DMAIC to improve an existing process:

- Define.
- Measure.
- Analyze.
- Improve.
- Control.

Use DMADV when designing a new product, service, or process:

- Define.
- Measure.
- Analyze.
- Design.
- Verify.

If the plan changes an existing system but redesigns major behavior from scratch, agents may blend both: DMAIC for understanding current defects, DMADV for designing and verifying the new state.

## Unit 3: Advanced DMAIC

### Define Phase

Define clarifies the problem, scope, customer, CTQs, business case, team, timeline, and expected value.

Core artifacts:

- Project charter.
- Problem statement.
- Goal statement.
- Scope and non-goals.
- Business case.
- Stakeholder list.
- SIPOC.
- Initial CTQs.
- High-level timeline.
- Communication plan.

Define tollgate asks:

- Is the problem worth solving?
- Is success measurable?
- Is the scope realistic?
- Is the team authorized?
- Are customer requirements understood enough to continue?

### Project Charter

A useful charter includes:

- Project name.
- Sponsor/champion.
- Process owner.
- Problem statement.
- Goal statement.
- Business case.
- Scope.
- Assumptions.
- Constraints.
- Team members.
- Milestones.
- Expected benefits.
- Initial risks.

### Ground Rules

Ground rules set expectations for participation, decision-making, documentation, conflict handling, meeting cadence, and data discipline.

### Define Toolset

Useful Define tools:

- VoC.
- CTQ tree.
- SIPOC.
- Stakeholder analysis.
- Project charter.
- Communication plan.
- Initial process map.
- High-level risk scan.

### Measure Phase

Measure establishes current performance and validates the measurement approach.

Core artifacts:

- Data collection plan.
- Operational definitions.
- Baseline metrics.
- Measurement-system review.
- FMEA.
- Sampling approach.
- Initial capability or defect analysis where relevant.

Measure tollgate asks:

- Is the defect clearly defined?
- Can the process be measured reliably?
- Is the baseline credible?
- Are measurement risks identified?
- Are likely failure modes visible?

### FMEA

Failure Modes and Effects Analysis identifies how a process can fail and prioritizes risk.

Common columns:

- Process step.
- Failure mode.
- Failure effect.
- Failure cause.
- Current control.
- Severity.
- Occurrence.
- Detection.
- Risk priority number or qualitative priority.
- Recommended action.
- Owner.
- Due date.
- Revised risk after action.

Use FMEA to prevent defects, not only document them.

### Data Collection

A data collection plan should define:

- Metric.
- Operational definition.
- Source.
- Sampling method.
- Sample size.
- Time window.
- Collection owner.
- Recording method.
- Data validation method.
- Privacy/compliance constraints.

Good data collection avoids changing the process simply by observing it unless the observation effect is intentional and controlled.

### Analyze Phase

Analyze identifies and verifies root causes.

Core artifacts:

- Root-cause analysis.
- Graphical analysis.
- Statistical analysis where appropriate.
- Cause-and-effect evidence.
- Prioritized causes.
- Updated project risk.

Analyze tollgate asks:

- Are root causes evidence-backed?
- Were multiple causes considered?
- Does the data support the conclusion?
- Are conclusions communicated in a way decision-makers can understand?

### Root-Cause Analysis

Useful tools:

- 5 Whys.
- Fishbone/Ishikawa diagram.
- Pareto chart.
- Scatter plot.
- Process map review.
- Data stratification.
- Regression or correlation when relationships are numerical.

Avoid stopping at symptoms, blaming people, or treating correlation as causation without support.

### Graphical Analysis

Graphs help expose patterns, outliers, distributions, trends, clusters, and relationships.

Common tools:

- Histogram.
- Pareto chart.
- Box plot.
- Scatter plot.
- Time series plot.
- Run chart.
- Control chart.
- Dot plot.
- Bar chart.

Graphical analysis is often the bridge between raw data and statistical testing.

### Statistical Analysis

Statistical analysis tests whether observed differences or relationships are likely meaningful. Use statistical tools when the decision risk justifies the complexity.

Agent rule:

- Use the simplest valid method.
- State assumptions.
- Distinguish practical significance from statistical significance.
- Translate findings into business or customer impact.

### Improve Phase

Improve selects, tests, and implements solutions that address verified causes.

Core artifacts:

- Solution ideas.
- Selection matrix.
- Cost/benefit analysis.
- Pilot plan.
- Implementation plan.
- Training and documentation plan.
- Signoff record.

Improve tollgate asks:

- Do solutions map to root causes?
- Was the best solution selected with clear criteria?
- Has the solution been tested or piloted when risk requires it?
- Are costs, benefits, and disruption understood?
- Are SOPs/training ready?

### Solution Selection Matrix

A solution matrix ranks alternatives using criteria such as:

- Expected impact.
- Ease of implementation.
- Cost.
- Time.
- Risk.
- Customer benefit.
- Sustainability.
- Compliance.
- Team/process-owner support.

Weight criteria when some factors matter more than others.

### Cost-Benefit Analysis

Compare implementation cost against expected benefits.

Costs can include:

- Labor.
- Software.
- Equipment.
- Training.
- Disruption.
- Maintenance.
- Migration.
- Opportunity cost.

Benefits can include:

- Cost savings.
- Cost avoidance.
- Revenue increase.
- Defect reduction.
- Cycle-time reduction.
- Customer retention.
- Morale or safety improvements.

Payback thinking estimates when the improvement returns its cost.

### Piloting A Solution

Pilot when a change is expensive, broad, risky, hard to reverse, operationally disruptive, or uncertain.

Pilot types:

- Limited population.
- Limited geography/team.
- Limited time.
- Dry run.
- Alpha/beta group.
- Test market.

Pilot plan must define:

- Scope.
- Time box.
- Success metrics.
- Failure metrics.
- Feedback method.
- Rollback.
- Decision owner.
- Scale-up decision rule.

### Implementation Planning

Implementation plans should include:

- Tasks.
- Owners.
- Due dates.
- Dependencies.
- Required approvals.
- Communication.
- Training.
- Documentation.
- Rollback.
- Verification.
- Handoff.

The plan should keep documentation centralized and accessible.

### Control Phase

Control sustains the improvement after the project team transitions ownership.

Core artifacts:

- Revised FMEA.
- Control plan.
- Monitoring method.
- Control charts or equivalent dashboard.
- SOPs.
- Training completion.
- Corrective actions.
- Process-owner handoff.
- Sponsor update.
- Lessons learned.

Control tollgate asks:

- Is the new process capable and/or performing acceptably?
- Does a process owner have the tools and authority to maintain it?
- Is monitoring active?
- Are corrective actions defined?
- Were lessons and future improvements captured?

### Control Plan

Control plans should include:

- Company/team/process name.
- Creator and revision date.
- Process owner.
- Process steps requiring control.
- CTQ or metric.
- Specification or acceptable limits.
- Unit of measurement.
- Measurement method.
- Sample size.
- Measurement frequency.
- Responsible person.
- Record location.
- Corrective action.
- Related SOP/policy.

Prefer corrective action at the process level when possible, so employees closest to the work can respond quickly.

### Visual Management

Visual controls reduce ambiguity and make process status obvious. Examples:

- Posted instructions.
- Color coding.
- Dashboards.
- Audit boards.
- Safety signals.
- Standard work posters.
- Status indicators.
- Error-proofing prompts.

For software projects, visual management can mean dashboards, alert states, runbooks, checklist screens, CI badges, and clear UI states.

### SPC And Control Charts

Control charts show whether a process remains statistically controlled over time.

Common elements:

- Center line.
- Upper control limit.
- Lower control limit.
- Zones based on standard deviations.
- Time-ordered observations.

Common out-of-control signals include:

- A point outside control limits.
- Many consecutive points on one side of the center line.
- Sustained trend upward or downward.
- Repeating non-random patterns.
- Points clustering near limits.
- Points clustering too tightly near the center.

Use control charts when process data is time-ordered and sustaining performance matters.

### Control Versus Capability

Control means the process is stable and predictable. Capability means the process can meet customer requirements. A controlled process can still be incapable if its stable output misses specification limits.

Agent rule:

- Check stability before interpreting capability.
- Do not celebrate reduced variation if the center is still wrong.

### Process Capability

Capability indices compare process variation and centering to specification limits. `Cpk` is commonly used to represent actual capability considering process centering.

Common interpretation:

- `Cpk < 1.0`: process often fails requirements.
- `Cpk around 1.0`: marginal.
- `Cpk around 1.33`: often treated as a minimum capability target in many settings.
- `Cpk 1.5+`: stronger capability.
- `Cpk 2.0`: commonly associated with highly capable processes.

Use domain expectations; not every process requires the same capability level.

### Team Reflection

After handoff, the team should reflect on:

- What improved.
- What failed or surprised the team.
- What controls remain fragile.
- What future improvement ideas emerged.
- What should be standardized for future projects.

## Unit 4: Beginner Statistics

### Intermediate Graphical Analysis

Use graphical analysis to understand data before formal testing.

Common graphs and uses:

- Histogram: distribution shape, center, spread, skew, and potential multimodality.
- Pareto chart: ranked categories and cumulative impact.
- Scatter plot: relationship between two variables.
- Box plot: median, spread, outliers, and group comparison.
- Time series plot: trends and shifts over time.
- Dot plot: small-sample distribution.
- Bar chart: category comparison.
- Run chart: process behavior over time without formal control limits.

Graph quality checks:

- Label axes and units.
- Avoid misleading scales.
- Show sample size when relevant.
- Separate categories only when definitions are stable.
- Do not infer causation from visual correlation alone.

### X-Bar Control Chart Without Statistical Software

When software is unavailable:

1. Collect rational subgroups.
2. Calculate subgroup averages.
3. Calculate ranges or standard deviations.
4. Estimate center line.
5. Estimate control limits using appropriate constants.
6. Plot subgroup averages over time.
7. Apply out-of-control tests.

Manual control charts are useful for learning and lightweight monitoring, but automated dashboards are better when frequent detection matters.

### Excel And Data Analysis Tools

Spreadsheet analysis can support histograms, descriptive statistics, regression, correlation, charts, and simple tests. Check formulas, ranges, filters, and hidden rows carefully. Spreadsheet errors are a measurement-system risk.

### Probability Distributions

Probability distributions model how values or events are expected to occur.

Use distributions to:

- Estimate likelihood.
- Model process behavior.
- Choose statistical tests.
- Calculate confidence and risk.
- Simulate outcomes.

### Histograms

Histograms group continuous data into bins. Use them to inspect distribution shape before assuming normality.

Watch for:

- Skew.
- Outliers.
- Multiple peaks.
- Truncation.
- Too many or too few bins.
- Specification limits outside or inside the distribution.

### Normal Distribution

Normal distributions are symmetric, bell-shaped distributions described by mean and standard deviation. Many Six Sigma methods assume normality or use normal approximations.

Important ideas:

- Mean, median, and mode coincide in a perfect normal distribution.
- Standard deviation controls spread.
- Z-scores express distance from the mean in standard deviations.
- Approximately 68%, 95%, and 99.7% of normal data lie within 1, 2, and 3 standard deviations of the mean.

### Normal Probabilities

Use z-scores to calculate probabilities:

```text
z = (x - mean) / standard deviation
```

Agent caution:

- Check whether normality is plausible.
- Use actual project data when possible.
- Treat probability output as decision support, not certainty.

### Correlation

Correlation measures association between variables.

Interpretation:

- Positive: variables tend to increase together.
- Negative: one tends to decrease as the other increases.
- Near zero: little linear relationship.

Cautions:

- Correlation does not prove causation.
- Outliers can distort correlation.
- Nonlinear relationships may be missed.
- Stratified groups can create misleading patterns.

### Regression

Regression models relationships between one or more predictors and an output.

Use regression to:

- Estimate how `X` variables influence `Y`.
- Predict outcomes.
- Compare factor strength.
- Support root-cause analysis.

Check:

- Model assumptions.
- Residual patterns.
- Practical significance.
- Overfitting.
- Data range: do not extrapolate casually.

## Unit 5: Intermediate Statistics

### Non-Normal Distributions

Processes are not always normal. Select models based on data type and behavior.

Continuous distributions:

- Exponential: time between events or waiting-time style processes.
- Weibull: reliability, life data, failure-time analysis.
- Lognormal: positive skew where multiplicative effects are plausible.
- Uniform: values roughly equally likely across a range.

Discrete distributions:

- Binomial: number of successes in fixed trials.
- Poisson: event counts over time/area/opportunity when events are rare and independent enough.
- Hypergeometric: sampling without replacement.

Agent rule:

- Choose distributions based on process meaning, not fit alone.
- If distribution choice is uncertain and important, recommend statistical review.

### Applying Data To Real Situations

Statistical fit must serve the decision. A mathematically acceptable model can still be operationally wrong if definitions, sampling, or process boundaries are wrong.

Always connect distribution analysis to:

- Defect definition.
- Customer requirement.
- Process mechanism.
- Measurement system.
- Actionable decision.

### Hypothesis Testing Basics

Hypothesis testing evaluates whether data provides enough evidence to reject a default assumption.

Terms:

- Null hypothesis: default claim, often no difference/no effect.
- Alternative hypothesis: claim being tested.
- Alpha: chosen false-positive risk.
- P-value: probability of data this extreme if the null were true.
- Confidence level: complement of alpha.
- Type I error: false positive.
- Type II error: false negative.
- Power: ability to detect a real effect.

Agent cautions:

- Statistical significance does not guarantee practical value.
- Non-significant does not prove no effect.
- Test assumptions matter.
- Multiple testing increases false-positive risk.

### Selecting A Hypothesis Test

Choose based on:

- Data type: continuous, binary, count, ordinal, categorical.
- Number of groups.
- Independent vs paired samples.
- Distribution assumptions.
- Variance assumptions.
- Objective: mean, median, proportion, variance, relationship, independence.

Common tests:

- One-sample t-test.
- Two-sample t-test.
- Paired t-test.
- One proportion test.
- Two proportion test.
- Chi-square test.
- ANOVA.
- Correlation/regression tests.
- Nonparametric alternatives when assumptions fail.

### Why Run Hypothesis Tests

Run tests to avoid mistaking random variation for real improvement. Use them when the decision has enough cost, risk, or uncertainty to justify formal inference.

### Sample Size

Sample size planning depends on:

- Desired confidence.
- Desired power.
- Expected effect size.
- Variation.
- Data type.
- Population size when relevant.
- Practical constraints.

Too-small samples can miss real effects. Too-large samples can detect trivial effects. The right sample size supports the decision.

### Hypothesis Testing Errors

- Type I: conclude there is a difference when there is not.
- Type II: fail to detect a real difference.

Reducing one error type can increase the other unless sample size changes. Tie error tolerance to business risk.

### Advanced Control Charts

Choose control charts by data type and subgrouping.

Variables data:

- I-MR chart: individual measurements.
- Xbar-R chart: subgroup averages and ranges.
- Xbar-S chart: subgroup averages and standard deviations.

Attribute data:

- P chart: proportion defective with varying subgroup sizes.
- NP chart: number defective with constant subgroup size.
- C chart: count of defects with constant opportunity area.
- U chart: defects per unit with varying opportunity area.

Chart choice matters; wrong charts create false signals or missed signals.

### Business-Friendly Statistical Presentations

When presenting statistics:

- Start with the decision, not the method.
- Explain why the test fits the question.
- Translate findings into process/customer impact.
- Show visuals before formulas when possible.
- Include limitations and assumptions.
- Avoid overstating certainty.

## Unit 6: Advanced Control And Minitab

### Minitab Purpose

Minitab supports statistical analysis, graphing, quality tools, capability analysis, control charts, hypothesis tests, ANOVA, DOE, and gage studies.

Agent use:

- Provide menu-path style guidance when users ask for Minitab workflow.
- Translate Minitab output into business meaning.
- Do not require Minitab if open-source tools or spreadsheets are sufficient for the task.

### Minitab Interface Concepts

Common areas:

- Worksheet/data grid.
- Session/output pane.
- Graph windows.
- Project manager.
- Menu-driven analysis.

Data should be organized with variables in columns and observations in rows.

### Minitab Menus

Common menu categories:

- File: open/save/import/export.
- Edit/Data: worksheet manipulation.
- Calc: formulas, random data, probability functions.
- Stat: basic statistics, regression, ANOVA, quality tools, DOE.
- Graph: charts and plots.
- Assistant: guided analyses in some versions.

### Graph Menu

Use graph options for:

- Histograms.
- Scatterplots.
- Boxplots.
- Dotplots.
- Time series plots.
- Probability plots.
- Bar and pie charts.

Always verify chart configuration, grouping variables, filters, and missing values.

### Quality Tools

Minitab quality tools can support:

- Capability analysis.
- Control charts.
- Pareto charts.
- Cause-and-effect diagrams.
- Gage studies.
- Acceptance sampling.

Use quality tools to connect statistical output to process control.

### Gage Studies

Gage Repeatability and Reproducibility studies evaluate measurement-system variation.

Key concepts:

- Repeatability: same operator/equipment measuring same item consistently.
- Reproducibility: different operators or setups measuring consistently.
- Part-to-part variation: real differences among measured items.

If the measurement system is weak, process conclusions are suspect.

### Basic Statistics In Minitab

Common outputs:

- Mean.
- Median.
- Standard deviation.
- Variance.
- Minimum/maximum.
- Quartiles.
- Confidence intervals.
- Normality checks.

Interpret descriptive statistics before jumping to tests.

### Statistical Tests In Minitab

Minitab can run:

- T-tests.
- Proportion tests.
- Variance tests.
- Chi-square tests.
- ANOVA.
- Regression.
- Correlation.
- Nonparametric tests.

Agent must explain the result in decision language: what changed, how confident we are, whether it matters, and what action follows.

## Unit 7: Experiments

### ANOVA

Analysis of variance compares means across more than two groups by separating within-group and between-group variation.

Use ANOVA when:

- The output is continuous.
- There are multiple groups or factor levels.
- The question is whether group means differ.

Check:

- Independence.
- Normality of residuals.
- Similar variances.
- Practical significance.
- Post-hoc comparisons when needed.

### Why Run Experiments

Experiments deliberately change inputs to observe effects on outputs. They are useful when observational data cannot clearly separate causes.

Reasons:

- Test causal relationships.
- Optimize settings.
- Discover interactions.
- Reduce trial-and-error.
- Compare alternatives efficiently.

### Best-Guess Trials Versus Factorial Experiments

One-factor-at-a-time or best-guess experimentation can miss interactions and waste trials. Factorial experiments vary factors systematically so main effects and interactions can be detected.

### Factorial Experimentation

Factorial designs test combinations of factor levels.

Terms:

- Factor: input variable.
- Level: setting of a factor.
- Response: output measured.
- Main effect: average impact of one factor.
- Interaction: when one factor's effect depends on another factor.
- `2^k`: two-level design with `k` factors.

### Creating A Designed Experiment

General steps:

1. Define response variable and goal.
2. Select factors and levels.
3. Identify constraints and nuisance variables.
4. Choose design type.
5. Randomize run order when possible.
6. Replicate or repeat when needed.
7. Collect data consistently.
8. Analyze main effects, interactions, and residuals.
9. Confirm results.
10. Update control plan.

### Interactions

Interactions matter because optimizing one factor independently can produce bad results when another factor changes. Agents should explicitly ask whether important interactions are plausible.

### Multi-Level Factorials

Multi-level factorials allow factors to have more than two levels. They can model richer response behavior but require more runs and stronger planning.

### DOE Success Tips

- Keep the objective clear.
- Limit factors to those with plausible impact.
- Ensure measurement is reliable.
- Randomize where possible.
- Control nuisance variables.
- Avoid changing uncontrolled conditions mid-experiment.
- Document run conditions.
- Validate findings before full rollout.

## Unit 8: Brainstorming, Process Maps, And Value Stream Mapping

### Brainstorming And Facilitation Tools

Use structured brainstorming to generate and organize ideas before analysis or solution selection.

Tools:

- Activity network diagram: sequence work and dependencies.
- Affinity diagram: group ideas by natural relationships.
- Interrelationship diagram: show cause/effect relationships among factors.
- Force field analysis: compare forces for and against change.
- Responsibility chart: clarify ownership and participation.
- Nominal group technique: generate, rank, and prioritize ideas.
- Check sheets: collect defect/event counts consistently.
- SWOT: strengths, weaknesses, opportunities, threats.
- Starbursting: ask who/what/when/where/why/how around an idea.
- Role-play/figure brainstorming: generate perspectives by adopting roles.
- Brainwriting: silent written idea generation.
- Solo brainstorming: individual preparation before group work.

### Process Maps

Process maps make work visible. They show steps, decisions, flows, loops, handoffs, delays, and rework.

Use process maps to:

- Align stakeholders.
- Identify waste.
- Expose hidden handoffs.
- Find measurement points.
- Support root-cause analysis.
- Design future-state workflows.

### Process Map Types

- Basic flow chart: sequence and decisions.
- Swimlane diagram: steps organized by role/team/system.
- Deployment map: ownership and handoffs.
- Detailed process map: more granular steps, rework, inputs, outputs.

### Process Map Symbols

Common symbols:

- Oval: start/end.
- Rectangle: process step.
- Diamond: decision.
- Arrow: flow.
- Parallelogram: input/output.
- Document shape: document/artifact.
- Database cylinder: stored data.
- Delay symbol: waiting.

The symbol set matters less than consistent use and stakeholder understanding.

### Creating Process Maps

Steps:

1. Define process boundaries.
2. Identify start and end.
3. Gather people who know the work.
4. Capture actual current state, not ideal state.
5. Add decision points and handoffs.
6. Mark delays, rework, defects, and data capture.
7. Validate with operators/process owners.
8. Use the map to find improvements.

### Swimlane Diagrams

Use swimlanes when handoffs between people, teams, systems, or roles matter. They are especially useful for software workflows, support processes, approval flows, and operational runbooks.

### Value Stream Mapping

Value stream mapping shows the flow of materials, information, people, and work needed to deliver value.

Use VSM to:

- Distinguish value-added from non-value-added activity.
- Identify delays, queues, inventory, rework, and information-flow breakdowns.
- Compare current state to future state.
- Improve flow rather than isolated steps.

### Value Stream Map Elements

Common elements:

- Customer demand.
- Supplier inputs.
- Process boxes.
- Data boxes.
- Inventory or queue markers.
- Information flow.
- Material/work flow.
- Timeline with value-added and wait time.
- Push/pull signals.

### Value Stream Mapping Steps

1. Select product/service/process family.
2. Define customer and demand.
3. Map current-state flow.
4. Capture cycle time, wait time, WIP, defect/rework, staffing, and information flow.
5. Identify bottlenecks and waste.
6. Design future state.
7. Create implementation plan.
8. Control and revisit after changes.

### Future-State Value Stream Maps

Future-state maps should reduce waste while preserving quality and control. Include realistic constraints, ownership, training, data collection, and monitoring. Avoid designing an ideal process that the organization cannot operate.

## Core Formulas And Decision Rules

### Yield

```text
yield = (opportunities - defects) / opportunities
yield_percent = yield * 100
```

### DPMO

```text
DPMO = (defects / opportunities) * 1,000,000
```

For multi-step products:

```text
opportunities = units * defect_opportunities_per_unit
```

### Z-Score

```text
z = (x - mean) / standard_deviation
```

### Basic Capability Concept

Capability compares process variation and centering to specification limits. Use `Cp` for potential capability when centered and `Cpk` for actual capability considering centering.

Common conceptual formulas:

```text
Cp = (USL - LSL) / (6 * sigma)
Cpk = minimum((USL - mean) / (3 * sigma), (mean - LSL) / (3 * sigma))
```

### FMEA Risk Priority

```text
RPN = severity * occurrence * detection
```

Use RPN as a ranking aid, not a substitute for judgment.

### Payback

```text
payback_period = implementation_cost / periodic_benefit
```

### CTQ Requirement Test

A CTQ requirement is usable when:

- It maps to a real customer/stakeholder need.
- It is measurable.
- It has a threshold or target.
- It can be monitored after implementation.
- Meeting it would plausibly satisfy the need.

## Agent Quick Lookup

Use this routing guide:

- Need to audit a plan before implementation: use `harness.md` and `templates.md`.
- Need Six Sigma terminology or concept grounding: use this file.
- Need DMAIC/DMADV phase readiness: use Unit 3 plus `harness.md`.
- Need data/statistics guidance: use Units 4-6.
- Need experiment planning: use Unit 7.
- Need process mapping, brainstorming, Lean, SIPOC, or VSM: use Units 1, 2, and 8.
- Need control/sustainment after release: use Control Phase, Control Plan, SPC, capability, and `templates.md`.

When responding as an agent:

- Start from the user's actual project goal.
- Select only the relevant Six Sigma tools.
- Avoid making the work heavier than the risk requires.
- State assumptions and evidence gaps.
- Translate manufacturing terms into the user's domain.
- End with a concrete next action, readiness decision, or corrected plan artifact.
