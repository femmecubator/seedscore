# Seedscore | Impact Assessment System — What's in this Package
Research documentation for Seedscore, an IAT Tool used for Accountable AI audit created for the Smol Gardens Project.

## 📋 What You Have

A complete, production-ready system for measuring and publishing the **Human**, **Systems**, and **Environmental Impact** of AI-assisted work.

### The Core Problem

Organizations building with AI often ask:
- **"Is this actually beneficial for our team?"** (Human Impact)
- **"Is this sustainable and resilient?"** (Systems Impact)
- **"What's the environmental cost?"** (Environmental Impact)

Existing frameworks don't adequately address all three dimensions together, and there's no standard way to compare impact across projects and organizations.

### The Solution

A **15-question scorecard** that generates comparable, publishable impact assessments. Think of it like:
- **A sustainability report** for AI projects
- **A skills audit** for team capabilities
- **A carbon footprint** with human-workflow comparison

---

## 📦 Files in This Package

### 1. **impact-assessment.html** ⭐ START HERE
**The working tool you use to assess projects**

- Single-file HTML (no dependencies, no server needed)
- Runs entirely in your browser
- 4 tabs: Project Setup → Assessment → Dashboard → Report
- Input: Project metadata + 13 questions
- Output: Three-pillar scorecard + exportable JSON

**How to use:**
1. Open in any web browser
2. Enter project details (LLM, API calls, team size, etc.)
3. Answer 15 questions (5 min each)
4. Get scorecard and export results
5. Share the JSON report with your team

---

### 2. **QUICK_START.md**
**For people who just want to use the tool**

- 5-minute overview of what the tool does
- How to interpret scores
- Real example walkthrough
- FAQ section
- Common troubleshooting

**Who reads this:** Project managers, product owners, anyone running an assessment

---

### 3. **IMPLEMENTATION_GUIDE.md**
**For people building on top of this or deploying it**

- Full technical architecture
- The 13 questions with detailed rubrics
- Data schema & JSON structure
- Environmental calculation formulas
- 5-phase roadmap (MVP → persistence → benchmarking → integration → analytics)
- Customization examples (adding questions, adjusting weights, etc.)
- Publishing & citation guidelines

**Who reads this:** Engineers, architects, governance leads, open-source contributors

---

### 4. **DATA_SCHEMA.json**
**Machine-readable schema for integrations**

- JSON Schema definitions for Project, Assessment, ScoreCard, Report
- Complete example objects
- Emissions data by LLM model
- Human comparison calculations

**Who uses this:** API developers, database architects, data science teams

---

## 🎯 Quick Navigation

**I want to assess my first project:**
→ Open `impact-assessment.html` in your browser

**I want to understand what I'm doing:**
→ Read `QUICK_START.md` (20 min)

**I want to customize or extend this:**
→ Read `IMPLEMENTATION_GUIDE.md` (detailed technical guide)

**I want to build an API or backend:**
→ Reference `DATA_SCHEMA.json` for data structures

---

## 🚀 Getting Started (Next 30 Minutes)

### Step 1: Download
Download all 4 files from this package. Put them in a folder.

### Step 2: Run Your First Assessment
1. Double-click `impact-assessment.html`
2. Fill in project details (2 min)
3. Answer 13 questions (10 min)
4. View your scorecard (5 min)
5. Export JSON results (1 min)

### Step 3: Share Results
Share the exported JSON with your team. Ask: "What do you think? Do you agree with these scores?"

### Step 4: Plan Improvements
Based on your scores, identify the lowest pillar and plan improvements.

---

## 📊 The Assessment at a Glance

### 15 Questions, 3 Pillars

**Human Impact (5 questions)**
- Core purpose alignment
- Labor & skills uplift
- Reusability by others
- Cognitive sovereignty (users in control)
- Collaboration & fairness

**Systems Impact (5 questions)**
- Speed vs. cost tradeoff
- Quality & trust
- Vendor lock-in risk
- Environmental resource use
- System resilience & maintainability

**Environment (3 questions)**
- Model efficiency
- Impact tracking
- Comparison to human workflow

### Scoring: 0–2.0 per pillar
- **0 = Not Met** (concern)
- **1 = Partially Met** (progress)
- **2 = Fully Met** (strong alignment)

### Output: Three-Pillar Scorecard
```
Human:        1.4/2.0  ✓ Good
Systems:      0.8/2.0  ⚠ Needs work (vendor lock-in)
Environment:  0.7/2.0  ⚠ Needs work (improve measurement)
Overall:      1.0/2.0
```

---

## 🔬 Example: Real Project Assessment

**Project:** Content Generation Pipeline
- LLM: Claude 3.5 Sonnet
- API Calls (30 days): 1,500
- Team: 3 people
- Duration: 30 days
- Outcome: 50% faster content, better consistency

**Scores:**
```
Human:        1.4   (well-aligned, but training needed)
Systems:      0.8   (moderate, but vendor risk)
Environment:  0.7   (saves 96× CO₂ vs. manual work!)
```

**Key Finding:** AI approach is much greener than human labor, but team needs more training on interpreting outputs.

---

## 🌍 Environmental Metrics

The tool estimates CO₂ footprint using:
- Inference call count
- Average tokens per call (~1,200)
- Model-specific emissions (pre-loaded for Claude, GPT-4, Gemini, etc.)

**Example:**
```
1,500 calls × 1,200 tokens × 0.0000138 kg CO₂/token ÷ 1,000 = 0.025 kg CO₂

vs. Human alternative:
3 people × 30 days × 8 hours × 0.02 kg CO₂/hour = 2.4 kg CO₂

AI is 96× more efficient!
```

---

## 📈 5-Phase Implementation Roadmap

### Phase 1: MVP ✓ (DONE)
- Single-file HTML tool
- 15 questions, three pillars
- JSON export

### Phase 2: Persistence (1–2 weeks)
- LocalStorage to save assessments
- Multi-project tracking
- Basic trending

### Phase 3: Benchmarking (2–4 weeks)
- Backend database
- Aggregate results across orgs
- Public comparison dashboard

### Phase 4: Integration (4–8 weeks)
- Claude.ai sidebar extension
- Slack bot
- GitHub Actions for CI/CD

### Phase 5: Analytics (8–12 weeks)
- Trend analysis (is Human Impact improving?)
- Correlation analysis (System Impact vs Human Impact)
- Governance rules & policies

---

## 🔧 How to Customize

### Add Your Own Questions
Edit the `questions` array in the HTML:
```javascript
const questions = [
  {
    id: 'myq1',
    category: 'human',  // or 'systems' or 'environment'
    title: 'Your question here?',
    context: 'Why it matters...',
    options: [
      { value: 0, label: 'Not Met', desc: '...' },
      { value: 1, label: 'Partially Met', desc: '...' },
      { value: 2, label: 'Fully Met', desc: '...' }
    ]
  }
];
```

### Weight Pillars Differently
Change the overall calculation:
```javascript
// Default: equal weight
overallScore = (human + systems + environment) / 3;

// Custom: Environment matters most (50%)
overallScore = (human × 0.25) + (systems × 0.25) + (environment × 0.5);
```

### Add LLM-Specific Emissions Data
```javascript
const emissionsData = {
  'Claude 3.5 Sonnet': 0.0000138,
  'GPT-4': 0.0000195,
  'Your Custom Model': 0.000012,
};
```

---

## 📚 What to Do With Your Results

### Internally
1. Share the JSON report with stakeholders
2. Track scores over time (quarterly reassessments)
3. Identify trends (are we improving on human agency?)
4. Set organizational thresholds (e.g., "must score ≥1.0 before launch")

### Externally (Contribute to Community)
1. Export your reports as JSON
2. Upload to a public GitHub repo (e.g., `ai-impact-reports`)
3. Add metadata: industry, project type, outcomes
4. Link to this methodology so others can replicate
5. Share findings in blog posts, papers, or conferences

### For Research
Aggregate anonymized assessments to:
- Identify which dimensions are most commonly weak
- Find best practices in high-scoring projects
- Benchmark by industry/model/company size
- Inform policy & governance

---

## ❓ Common Questions

**Q: Do I need a server?**
A: No. The HTML tool runs entirely in your browser. No login, no cloud required.

**Q: Can I customize the questions?**
A: Yes. Edit the HTML `questions` array. Keep the standard 13 for comparability.

**Q: How do I compare scores with other projects?**
A: Export the JSON and build a simple dashboard. The `DATA_SCHEMA.json` defines the format.

**Q: Should every AI project be assessed?**
A: Recommend assessing all "significant" AI projects (>$1k cost, >10 team hours, customer-facing, new vendor).

**Q: Can I integrate this with my existing tools?**
A: Yes. The data schema is straightforward JSON. APIs, bots, and integrations can consume it.

**Q: How accurate are the environmental estimates?**
A: Rough but useful for comparison. For precise measurement, integrate Code Carbon.

---

## 🎓 Key Concepts

| Concept | Meaning | Example |
|---------|---------|---------|
| **Human Impact** | Does AI strengthen or weaken the team? | Team learns more about AI outputs (good) vs. blindly trusts them (bad) |
| **Systems Impact** | Is the architecture sustainable? | Multi-vendor architecture (resilient) vs. single vendor (fragile) |
| **Environment** | What's the carbon footprint? | AI uses 96× less energy than human work (very good) |
| **Cognitive Sovereignty** | Users understand & critique outputs | Team debates AI decisions vs. auto-accepts them |
| **Vendor Lock-in** | Dependency on single provider | Easy to switch models (good) vs. hard to migrate (bad) |
| **Reusability** | Can others use this work? | Open, documented, easy to adopt vs. proprietary, bespoke |
| **Systemic Fragility** | How easily does the system break? | Well-designed, tested vs. brittle, hard to maintain |

---

## 🤝 Contributing

This is an open framework. Contributions welcome:
- **New question sets** (for specific industries, use cases)
- **Integration examples** (Slack bots, API servers, dashboards)
- **Emissions data** (more precise per-model estimates)
- **Translations** (make available in multiple languages)
- **Case studies** (share your assessments and findings)

---

## 📄 License & Citation

**License:** CC BY-SA 4.0 (Creative Commons Attribution-ShareAlike)
- You can use, modify, and distribute freely
- You must give credit to the original creators
- Derivative works must use the same license

**How to cite:**
```bibtex
@tool{seedscore-impact-assessment-2026,
  title={Seedscore Impact Assessment Tool: Measuring Human, Systems, and Environmental Impact of AI-enabled Work},
  author={[Krizia Fernando/Open Civic Tech]},
  year={2026},
  url={https://github.com/femmecubator/seedscore},
  license={CC BY-SA 4.0}
}
```

---

## 🚦 Next Steps

### For Your First Assessment (Today)
1. ✅ Open `impact-assessment.html`
2. ✅ Fill in your most recent project
3. ✅ Answer 13 questions (~10 min)
4. ✅ Export and review results
5. ✅ Share with your team

### For Your Organization (This Week)
1. ✅ Run 2–3 more assessments on different projects
2. ✅ Collect feedback on questions
3. ✅ Identify patterns (e.g., low Systems Impact across all projects?)
4. ✅ Share findings in team meeting

### For Long-Term Impact (This Month)
1. ✅ Set quarterly assessment schedule
2. ✅ Define organizational thresholds ("all new AI projects must score ≥1.0")
3. ✅ Build simple tracking dashboard (Google Sheets, Airtable, etc.)
4. ✅ Plan Phase 2 improvements (persistence, benchmarking, etc.)
5. ✅ Contribute findings to public dataset

---

## 💬 Support

**Questions or issues?**
1. Check `QUICK_START.md` FAQ
2. Review `IMPLEMENTATION_GUIDE.md` for technical details
3. File an issue on GitHub

**Want to contribute?**
- Fork the repo
- Submit a pull request
- Share your assessments & findings

**Want to use commercially?**
- You can! CC BY-SA 4.0 allows commercial use
- Just credit the original methodology

---

## 🎉 You're Ready!

You now have everything needed to:
✅ Assess your AI projects  
✅ Understand impact across three dimensions  
✅ Make data-driven decisions  
✅ Share findings with your team and community  
✅ Contribute to the collective knowledge about AI impact  

**Start with `seedscore.html` — open it now!**

---

**Last Updated:** September 2026 
**Version:** 1.0 MVP  
**Status:** Production-ready for testing  

🌱 Community-owned, Responsible and Accountable 🌱 
