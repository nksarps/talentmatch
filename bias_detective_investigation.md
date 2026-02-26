# Bias Investigation Report: TalentMatch AI HireScore System

**Investigator:** Nana Kwaku Sarpong
**System Under Review:** HireScore AI Applicant Ranking Tool  

## Executive Summary

**Overall Bias Severity: HIGH** — The HireScore system systematically disadvantages women, graduates from non-elite universities, and applicants from regions outside Greater Accra and Ashanti.

**Key Findings:**
- Women comprise only 11% of top-ranked candidates despite being 28% of historical successful hires
- 94% of top candidates come from just three universities (UG, KNUST, Ashesi)
- Applicants from Northern Region score 2.2 points lower on average than Accra-based candidates

**Top 3 Immediate Actions:**
1. Remove proxy features (LinkedIn connections, company names, location data)
2. Collect balanced training data from underrepresented groups
3. Implement weekly demographic performance monitoring


## Task 1: Identify Bias Types

### 1. Historical Bias

| | |
|---|---|
| **Present in HireScore?** | **YES** |
| **Evidence from artifacts** | The training data itself reflects historical hiring discrimination: 72% of successful hires were male, and 77% came from just two regions (Greater Accra and Ashanti). The model learned these patterns as "success signals." |
| **Which groups are disadvantaged?** | Women; applicants from Northern, Upper East, Upper West, Volta, and other non-Ashanti/Greater Accra regions; graduates from smaller or regional universities |

### 2. Sampling Bias

| | |
|---|---|
| **Present in HireScore?** | **YES** |
| **Evidence from artifacts** | The training data overrepresents Software Engineering roles (60% of data) and underrepresents Sales/Marketing and Operations (10% each). Regional distribution is skewed toward Greater Accra (60%) despite Ghana having 16 regions. |
| **Which groups are underrepresented?** | Non-STEM professionals; applicants from the 14 regions outside Greater Accra and Ashanti (only 15% combined in training data) |

### 3. Measurement Bias

| | |
|---|---|
| **Present in HireScore?** | **YES** |
| **Evidence from artifacts** | **LinkedIn connections count** and **number of professional references** are measured the same way for all applicants, but these metrics systematically disadvantage candidates from regions with less professional networking infrastructure or who attended universities with smaller alumni networks on LinkedIn. |
| **What's being measured unfairly?** | Social/professional capital is being measured as "candidate quality," when it actually measures access to professional networks. |

### 4. Proxy Bias

| | |
|---|---|
| **Present in HireScore?** | **YES** |
| **Which features act as proxies?** | - **University attended** → proxies for socioeconomic background and region<br>- **Previous company names** → proxies for geographic location and professional networks<br>- **LinkedIn connections count** → proxies for urban/Accra-based networking opportunities<br>- **Location of previous employment** → direct proxy for region of origin<br>- **Extracurricular activities** → may proxy for socioeconomic status (certain activities require resources) |
| **How do they disadvantage certain groups?** | A qualified candidate from Northern Region who attended a smaller university and worked at a local company will have lower scores on these proxy features than a similarly skilled candidate from Accra who attended KNUST and worked at an Accra-based multinational, regardless of actual job-relevant skills. |


## Task 2: Trace the Bias Pipeline

```
[Historical Hiring Decisions]
    ↓
    Bias: Past recruiters favored male candidates from elite schools and major cities
    ↓
[Training Data Collection] → Bias Point #1: HISTORICAL/SAMPLING BIAS
    ↓                The data reflects past discrimination and overrepresents
    ↓                certain regions, genders, and job categories
    ↓
[Feature Selection] → Bias Point #2: PROXY/MEASUREMENT BIAS
    ↓                Features like LinkedIn connections and previous company
    ↓                names capture social capital, not just merit
    ↓
[Model Training] → Bias Point #3: ALGORITHMIC AMPLIFICATION
    ↓                Model learns and potentially amplifies patterns in biased
    ↓                training data (as seen in scoring results)
    ↓
[Deployment & Scoring] → Bias Point #4: FEEDBACK LOOP BIAS
    ↓                Clients see biased rankings → hire recommended candidates →
    ↓                new "successful hire" data reinforces existing bias
    ↓
[Biased Outcomes]
    Top candidates are 89% male, 94% from elite schools, 91% from two regions
```


## Task 3: Feature Analysis

| Feature | Bias Risk Level | Rationale |
|---------|----------------|-----------|
| LinkedIn connections count | **HIGH** | Direct proxy for urban professional networks; disadvantages candidates from regions with fewer LinkedIn users |
| Previous company names | **HIGH** | Correlates with region and access to multinational companies |
| Location of previous employment | **HIGH** | Direct geographic signal that proxies for region of origin |
| University attended | **MEDIUM-HIGH** | Strong proxy for region and socioeconomic background |
| Age (derived) | **MEDIUM** | Could correlate with gender (women's career interruptions) but may be job-relevant |
| Extracurricular activities | **MEDIUM** | Some activities require resources unavailable in all regions |
| Number of professional references | **MEDIUM** | Similar to LinkedIn connections - measures network access |
| Years of experience | **LOW** | Directly job-relevant; bias only if measured inconsistently |
| Skills listed | **LOW** | Most direct measure of job qualification |

## Task 4: Mitigation Plan

### Immediate Actions (This Week)

| Action | Owner | Rationale | Success Metric |
|--------|-------|-----------|----------------|
| **Remove LinkedIn connections count** | Data Engineering | High-risk proxy feature with low job relevance | Feature removed from model; scores recalculated for pending applications |
| **Remove or anonymize previous company names** | Data Engineering | Replace with "years of experience" and "industry category" only | Model no longer has access to company prestige signals |
| **Disable location-based features** | Data Engineering | Remove "location of previous employment" from feature set | Geographic signals eliminated from scoring |
| **Create weekly demographic dashboard** | QA Engineering | Track score distribution by gender, region, university | Dashboard operational and reviewed in weekly team meeting |

### Short-Term Actions (Next 1-3 Months)

| Action | Owner | Details | Success Metric |
|--------|-------|---------|----------------|
| **Collect balanced training data** | HR Operations | Partner with clients to gather successful hire data from women, Northern region candidates, and non-elite university graduates | 500+ new diverse success records added to training set |
| **Reweight training data** | Data Science | Apply pre-processing techniques (e.g., Reweighing algorithm) to give more weight to underrepresented groups in historical data | Training data distribution moves toward 50% gender balance, 30%+ non-core regions |
| **Adopt adversarial debiasing during retraining** | ML Engineering | Implement in-processing technique where model is trained to predict job success while adversary tries to predict protected attributes | Reduced correlation between predictions and protected attributes |
| **Add human review threshold** | Product | Flag applications where top candidate demographics deviate significantly from applicant pool for manual review | Human review triggered for at least 10% of hiring decisions |
| **Audit skills extraction** | ML Engineering | Ensure skills extraction doesn't favor certain terminology or formats | Skills recognized equitably across all applicant groups |

### Long-Term Actions (Next 6-12 Months)

| Action | Owner | Details | Success Metric |
|--------|-------|---------|----------------|
| **Adopt Equal Opportunity as core fairness metric** | Leadership | Focus on equalizing true positive rates across groups (qualified candidates should be recommended at same rate regardless of demographics) | True positive rates within 5% across gender, region, and university groups |
| **Develop region-aware normalization** | Data Science | Normalize features like extracurriculars by what's available in each region, not absolute counts | Fairer comparison across regions |
| **Client education program** | Customer Success | Train clients on bias risks and appropriate use of HireScore as one input, not sole decision maker | 100% of clients complete training; reduced over-reliance on scores |
| **Annual fairness audit** | External Auditor | Third-party review of model performance across protected groups | Public transparency report released annually |
| **Implement "impact assessment" for new features** | Product/QA | Any new feature added to model must undergo bias risk assessment before deployment | 100% of new features have documented bias review |
| **Publish transparency report** | Communications | Annual public disclosure of demographic performance and mitigation efforts | Report published and accessible on company website |

## Fairness Definition Trade-offs

TalentMatch must choose which fairness definition to prioritize:

| Definition | What It Means | Trade-off |
|------------|---------------|-----------|
| **Demographic Parity** | Proportion of recommended candidates matches population demographics | May recommend less qualified candidates to meet quotas |
| **Equal Opportunity** | Qualified candidates have equal chance of recommendation regardless of group | Focuses on "getting it right" for good candidates; doesn't address over-recommendation to unqualified majority candidates |
| **Equalized Odds** | Both false positive and false negative rates are equal across groups | Most mathematically stringent; may require significant model changes or performance trade-offs |

### Recommendation

Adopt **Equal Opportunity** as the primary metric. This ensures that equally qualified candidates from all backgrounds have the same chance of being in the top recommendations, which aligns with TalentMatch's value of identifying the best talent regardless of demographics.

Secondary metrics to track:
- **Demographic parity** for awareness of overall representation
- **Score distribution** by group to monitor for drift
- **Applicant-to-recommendation ratio** by group

## Conclusion

The HireScore system requires immediate intervention to prevent ongoing discriminatory outcomes. The evidence clearly shows that the model has learned and amplified historical biases present in Ghana's job market, particularly favoring male candidates from Accra/Ashanti who attended elite universities.

By implementing the phased mitigation plan above, TalentMatch AI can transform HireScore into a tool that identifies talent equitably across all of Ghana's diverse regions and communities. Success will require ongoing commitment from leadership, engineering, and customer-facing teams, as well as transparency with clients and applicants about the steps being taken to ensure fairness.
