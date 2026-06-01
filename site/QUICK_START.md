# Quick Start Guide — Impact Assessment Tool

## What is This?

A system to measure the **Human**, **Systems**, and **Environmental Impact** of AI-assisted projects. Use it to:
- ✅ Evaluate whether AI is truly beneficial
- ✅ Compare impact across your projects
- ✅ Share findings with your team and the broader community
- ✅ Make data-driven decisions about AI adoption

**Time to Complete**: 15-20 minutes per project

---

## Getting Started (30 seconds)

1. **Open** `impact-assessment.html` in any web browser (Chrome, Firefox, Safari, Edge — all work)
2. **Fill in** your project details (name, LLM model, API calls, team size)
3. **Click** "Start Assessment"
4. **Answer** 13 questions about your project
5. **View** your three-pillar scorecard and download results as JSON

**No login. No external servers. No signup. Everything happens in your browser.**

---

## The 3 Questions You'll Be Asked

### 1. Will this AI work actually help?
**Human Impact** — Is the work meaningful, fair, and does it strengthen the team?
- Core purpose alignment
- Skill uplift (not deskilling)
- Reusability by others
- User maintains control (cognitive sovereignty)
- Collaborative & transparent process

**Your score: 0–2**

### 2. Does the system architecture hold up?
**Systems Impact** — Is the work sustainable, resilient, and properly evaluated?
- Speed gains vs. cost
- Quality and trust
- Vendor lock-in risk
- Environmental resource use
- System fragility (easy to maintain?)

**Your score: 0–2**

### 3. What's the environmental cost?
**Environment** — Is the AI approach greener than doing it manually?
- Model size & efficiency
- Impact tracking & measurement
- Comparison to human workflow

**Your score: 0–2**

---

## The Scoring System

Each question has **3 options**:

| Option | Value | Meaning |
|--------|-------|---------|
| 🔴 Not Met | 0 | Work doesn't satisfy this dimension |
| 🟡 Partially Met | 1 | Work shows progress but has gaps |
| 🟢 Fully Met | 2 | Work demonstrates strong alignment |

**Example:**
```
Q: "Does the AI work directly serve your core purpose?"

🔴 Not Met: "The AI feels peripheral to what we're actually trying to do"
🟡 Partially Met: "It helps with some parts, but we're still doing a lot manually"
🟢 Fully Met: "The AI directly solves our main problem"
```

---

## Interpreting Your Results

### Overall Score: 0–2.0

| Score | Meaning | Action |
|-------|---------|--------|
| **0.0–0.5** | 🔴 Misaligned | Pause & reconsider. Address major concerns. |
| **0.5–1.0** | 🟡 Mixed | Some benefits, but significant gaps remain. |
| **1.0–1.5** | 🟡 Balanced | Good overall. Minor improvements possible. |
| **1.5–2.0** | 🟢 Strong | Well-designed AI integration. Recommended. |

### Three-Pillar Breakdown

**You might see:**
```
Human:       1.4/2.0  ← Good, but cognitive sovereignty could improve
Systems:     0.8/2.0  ← Watch out: vendor lock-in risk
Environment: 0.7/2.0  ← Address: improve measurement & optimization
```

**What to do:**
1. Focus on lowest pillar first
2. Read the specific questions that scored 0–1
3. Use recommendations to prioritize fixes
4. Reassess quarterly

---

## Real Example: Content Generation Pipeline

**Project:** Automated blog + social media content creation

**Setup Data:**
- LLM Model: Claude 3.5 Sonnet
- Inference Calls (30 days): 1,500
- API Cost: $45.50
- Team Size: 3
- Duration: 30 days
- Outcome: 50% faster production, better consistency

**Results:**
```
Human Impact:       1.4/2.0  (well-aligned purpose, but needs more training)
Systems Impact:     0.8/2.0  (moderate cost, but heavy vendor lock-in)
Environment:        0.7/2.0  (~0.025 kg CO₂, vs. 2.4 kg for human work = 96× better!)

Overall:            1.0/2.0  (Balanced. Proceed, with improvements.)
```

**Recommendations:**
- Strengthen team understanding of AI outputs (cognitive sovereignty)
- Research multi-vendor architecture to reduce Claude API dependency
- Implement detailed emissions tracking using Code Carbon

---

## What Each Section Means

### Project Setup

**Project Name**: Give it a descriptive name (e.g., "Q4 Report Automation", "Customer Support Bot")

**LLM Model**: Which AI did you use? (Claude, GPT-4, Gemini, etc.)
- Different models have different efficiency profiles
- Pick the one you actually used

**Inference Calls**: How many API requests in your assessment period?
- Example: 1,500 calls to generate blog posts
- Check your API dashboard or billing report

**API Cost**: Total amount spent ($45.50, €30, etc.)
- Used to understand speed/cost tradeoff
- Part of Systems Impact score

**Team Size**: How many people worked on this?
- Affects "human hours equivalent" calculations
- Important for fairness analysis

**Duration**: How many days did the project run?
- Helps normalize metrics across different timescales

**Outcomes**: What did you achieve?
- Used for interpretation (is the AI work delivering value?)

---

### The Assessment Itself

You'll see **13 questions total**:
- **5 on Human Impact** (purpose, skills, reusability, sovereignty, collaboration)
- **5 on Systems Impact** (speed, quality, vendor, environment, fragility)
- **3 on Environment** (model size, tracking, human comparison)

**Answering Tips:**
- Be honest. There's no "right" answer.
- If unsure between two options, pick the lower one (avoid over-scoring)
- Read the context hint ("_Why this matters..._")
- You can go back to change answers before submitting

---

### Your Dashboard

**Three-Pillar Cards** — Big visual display of your scores
```
Human     Systems    Environment
 1.4        0.8          0.7
```

**Metric Breakdown** — Which specific questions scored high/low?
```
Core Purpose              ████████ 2/2
Labor & Skills           ████ 1/2
Cognitive Sovereignty    ████ 1/2
[...]
```

**Environmental Metrics**
- Estimated CO₂ (kg)
- Inference calls
- Model used

**Efficiency Comparison** — AI hours vs. manual hours
```
AI approach:      60 hours
Manual work:    1,440 hours  (24× more)
```

---

### Your Report

The report is a **shareable document** with:
- Project summary
- All three scores
- Key findings
- Recommendations
- Emissions estimates

**Download as JSON** to:
- Archive for later reference
- Share with your team
- Compare against future projects
- Contribute to public benchmarking dataset

---

## FAQ

### Q: Do I have to answer all questions?
**A:** Yes. Each question must be answered to proceed.

### Q: What if my answer changes during the assessment?
**A:** You can go back at any time. Click "← Back" to revise previous answers.

### Q: Should I assess each project separately?
**A:** Yes. Create a new assessment for each distinct project or product area. This gives you comparison data over time.

### Q: How accurate are the CO₂ estimates?
**A:** They're **rough estimates**, not precise measurements. Use them for:
- ✅ Relative comparison (AI vs. manual)
- ✅ Trend direction (is it improving?)
- ❌ Exact carbon footprint (use Code Carbon for precise tracking)

See `emissionsData` in the JSON schema for per-model details.

### Q: Can I modify the questions for my organization?
**A:** Yes! The HTML is fully editable. Change the `questions` array in the `<script>` section. But keep the standard 13 for benchmarking.

### Q: How do I compare my scores against others?
**A:** Share your JSON report in the community dataset. As more assessments come in, benchmarks will emerge (e.g., "median Human Impact for content projects = 1.2").

### Q: What's the difference between "Partially Met" and "Fully Met"?
**A:** 
- **Partially Met (1)**: You're doing it, but with gaps or inconsistency
- **Fully Met (2)**: You're doing it well, systematically, and deliberately

Example:
```
"Does your team understand AI outputs?"

Partially: "We review them, but most people don't question the AI's logic"
Fully Met: "We understand the underlying approach and actively debate results"
```

### Q: Can I do assessments for multiple LLMs in one project?
**A:** Yes. Either:
1. Create separate assessments per LLM (more granular), or
2. Use the dominant LLM and note the others in "Outcomes"

---

## Workflow: Assessing Your First Project

**Step 1: Pick a Recent Project** (30 days old, something completed)
- Not too fresh (you need data on outcomes)
- Not too old (you remember the details)

**Step 2: Gather Data** (5 min)
- [ ] Project name & description
- [ ] LLM used (check API dashboard or code)
- [ ] API calls in period (check billing)
- [ ] Total cost
- [ ] Team size
- [ ] Key outcomes achieved

**Step 3: Open the Tool** (1 min)
- Double-click `impact-assessment.html`
- Browser opens automatically

**Step 4: Fill Project Setup** (2 min)
- Enter project info
- Click "Start Assessment"

**Step 5: Answer Questions** (10 min)
- Read each question carefully
- Choose Not Met / Partially Met / Fully Met
- Click "Next" to proceed
- Use "Back" if you need to revise

**Step 6: Review Results** (2 min)
- Look at three-pillar scorecard
- Read metric breakdown
- Check environmental comparison

**Step 7: Export & Share** (1 min)
- Click "Export as JSON"
- Save to your project folder
- Share with team if desired

**Total Time: 15–20 minutes**

---

## Next Steps After Your First Assessment

### For Your Team
1. **Run 2–3 more assessments** on different projects
2. **Collect feedback**: Which questions were confusing?
3. **Identify patterns**: Do all projects have low Systems scores? If so, vendor lock-in is a concern
4. **Share results**: Post anonymized findings in team Slack/email

### For Your Organization
1. **Create a policy**: "All AI projects require impact assessment before launch"
2. **Track over time**: Quarterly reassessments to see trends
3. **Benchmark**: Compare your Human Impact scores to the industry median
4. **Governance**: Set minimum thresholds (e.g., "must score ≥1.0 overall")

### For the Community
1. **Export your reports** as JSON
2. **Share on GitHub** (create a public repo: `ai-impact-reports/`)
3. **Contribute findings** to emerging benchmarks
4. **Cite the methodology**: Link to this tool so others can replicate

---

## Customization & Extensions

### Add Organization-Specific Questions
Edit the HTML `questions` array to add your org's priorities:
```javascript
{
  id: 'custom1',
  category: 'human',
  title: 'Does this align with our DEI commitments?',
  context: 'AI should not perpetuate bias...',
  options: [
    { value: 0, label: 'Not Met', desc: '...' },
    // ...
  ]
}
```

### Weight Scoring by Importance
```javascript
// Make Human Impact worth 50%, Systems 30%, Environment 20%
const overall = (human × 0.5) + (systems × 0.3) + (env × 0.2);
```

### Add Environmental Tracking
Integrate Code Carbon (codecarbon.io) to replace estimates with real measurements:
```javascript
// In the environmental metrics section:
const realEmissions = await getCodeCarbonData(projectId);
document.getElementById('realCO2').textContent = realEmissions.kg;
```

---

## Troubleshooting

**The HTML won't open**
- Right-click → "Open with" → choose your browser
- Drag the file into your browser window
- Check file isn't named something weird (should end in `.html`)

**My scores seem too high/low**
- Re-read the rubrics for each option
- Be more critical — "Fully Met" means *systematically excellent*
- Consider consulting with team members for consensus

**I can't export the JSON**
- Check browser permissions (File > Export)
- Try downloading instead of opening the file
- Use browser DevTools (F12) to copy the data manually

**The environmental estimate seems wrong**
- Estimates use 1.2k tokens per call and model-specific emissions
- For precision, integrate Code Carbon or ML.energy
- Check assumed tokens/call matches your actual usage

---

## Key Concepts (Glossary)

| Term | Meaning |
|------|---------|
| **Human Impact** | Does the AI work strengthen or weaken the team? Is it ethical? |
| **Systems Impact** | Is the AI architecture sustainable, resilient, and well-designed? |
| **Environment** | What's the carbon footprint compared to manual work? |
| **Cognitive Sovereignty** | Users understand and critically assess AI outputs (not blindly accepting) |
| **Vendor Lock-in** | Dependency on a single AI provider; hard to switch if pricing/terms change |
| **Systemic Fragility** | System breaks easily; hard to debug, modify, or improve |
| **Reusability** | Can others adopt or learn from this work? |
| **Emissions per Token** | kg CO₂ produced by one token of inference (varies by model & hardware) |
| **Partially Met** | You're doing it, but inconsistently or with gaps |
| **Fully Met** | You're doing it systematically and intentionally |

---

## Resources

**Emissions Calculators:**
- Code Carbon: https://codecarbon.io (real-time tracking)
- ML.energy: https://ml.energy (detailed breakdowns)
- Hugging Face Model Cards: Model-specific estimates

**Frameworks & Reading:**
- Microsoft Responsible AI: Responsible AI Principles
- Green AI: https://arxiv.org/abs/1909.09408
- Participatory AI: Ensuring human agency in systems

**Similar Tools:**
- AI Fairness 360 (IBM)
- Model Cards (Google)
- Datasheets for Datasets

**Community:**
- Hugging Face Hub (share findings)
- EA Forum (effective altruism)
- AI Now Institute (research)

---

## Support

**Questions about scoring?**
- Review the scoring rubric in the question context
- Discuss with your team — consensus is often better than individual judgment
- File an issue on GitHub if the question itself is confusing

**Found a bug?**
- Share the steps to reproduce
- Include your browser (Chrome, Firefox, etc.)
- Attach a screenshot if helpful

**Want to contribute?**
- Submit new question sets
- Improve the emissions calculations
- Add integrations (Slack, GitHub, etc.)
- Translate to other languages

---

**Good luck with your first assessment! 🌱**

---

**Last Updated**: December 2024  
**Version**: 1.0  
**License**: CC BY-SA 4.0
