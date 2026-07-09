# AI-Powered LinkedIn Candidate Qualification Pipeline

This project automates the process of screening LinkedIn candidates for engineering roles using semantic similarity. It scrapes LinkedIn profile data, scores candidates against an ideal candidate profile using Sentence Transformers, generates personalized outreach messages for qualified candidates, performs email enrichment only for qualified candidates, and exports the results to an Excel file for recruiter review or further automation.

---

## Features

- Import LinkedIn profile URLs from a CSV file
- Scrape LinkedIn profile data using Apify (HarvestAPI)
- Score candidates using Sentence Transformers (all-MiniLM-L6-v2)
- Generate semantic similarity scores (0–100)
- Create human-readable justifications for every score
- Automatically classify candidates as **Pending** or **Rejected**
- Generate personalized outreach emails only for qualified candidates
- Perform a second email enrichment step **only for Pending candidates** to reduce API costs
- Export all results to an Excel file
- Ready for integration with n8n or Make.com

---

## Workflow

```text
leads.csv
    │
    ▼
Apify LinkedIn Profile Scraper
(Profile Details - No Email)
    │
    ▼
SentenceTransformer Scoring
    │
    ▼
Candidate Qualification
    │
    ├───────────────┐
    │               │
Rejected         Pending
    │               │
    │               ▼
    │       Apify Email Search
    │       (Pending Only)
    │               │
    └───────────────┘
            │
            ▼
Generate Personalized Outreach
            │
            ▼
qualified_leads.xlsx
            │
            ▼
(Optional)
n8n / Make.com Email Automation
```

---

## Project Structure

```
project/
│
├── task_1.ipynb
├── leads.csv
├── qualified_leads.xlsx
├── README.md
└── requirements.txt
```

---

## Technologies Used

- Python
- Pandas
- NumPy
- Sentence Transformers
- all-MiniLM-L6-v2
- Apify Client
- HarvestAPI LinkedIn Profile Scraper
- OpenPyXL

---

## Installation

```bash
pip install pandas numpy sentence-transformers apify-client openpyxl
```

---

## Input

Create a CSV file named:

```
leads.csv
```

Example:

| profile_url |
|--------------|
| https://www.linkedin.com/in/example1 |
| https://www.linkedin.com/in/example2 |

---

## Ideal Candidate Profile

Candidates are compared against an Ideal Candidate Profile (ICP), which includes criteria such as:

- Civil Engineering
- Mechanical Engineering
- Structural Engineering
- Construction Management
- Project Management
- Entry-level or internship experience
- AutoCAD exposure
- US-based candidates
- Interest in telecom or OSP engineering

Sentence Transformers compute the semantic similarity between each LinkedIn profile and the ICP.

---

## Candidate Scoring

Each candidate receives:

- Fit Score (0–100)
- Justification
- Status

Classification:

- **Pending** → Score ≥ 75
- **Rejected** → Score < 75

---

## Email Enrichment

To optimize API costs, email enrichment is performed **only after candidate qualification**.

Workflow:

1. Scrape profile details (without email)
2. Score candidates
3. Identify qualified candidates (Pending)
4. Perform a second Apify run to search for emails only for Pending candidates
5. Merge the discovered email addresses into the final Excel file

If no verified email is found, the **Candidate Email** field remains blank.

---

## Personalized Outreach

A personalized outreach message is generated only for **Pending** candidates.

Rejected candidates:

- No outreach message is generated.
- No email is sent.

---

## Output

The pipeline generates:

```
qualified_leads.xlsx
```

Example:

| Candidate Name | Profile Link | Candidate Email | Fit Score | Justification | Generated Personalized Outreach Text | Status |
|----------------|--------------|-----------------|-----------|---------------|---------------------------------------|--------|
| James Larkin | linkedin.com/... | jlarkin@company.com | 81 | Strong semantic match... | Hi James... | Pending |
| Lara King | linkedin.com/... | | 81 | Strong semantic match... | Hi Lara... | Pending |
| Adam Diomande | linkedin.com/... | | 63 | Below qualification threshold... | | Rejected |

---

## Status Definitions

| Status | Meaning |
|----------|---------|
| Pending | Qualified candidate awaiting outreach |
| Sent | Outreach email successfully sent (handled by automation platform) |
| Rejected | Candidate did not meet the qualification threshold |

---

## Future Automation

The generated Excel file is designed for automation platforms such as **n8n** or **Make.com**.

Recommended workflow:

```
Read Excel
      │
      ▼
Status == Pending
      │
      ▼
Candidate Email exists?
      │
 ┌────┴────┐
 │         │
No        Yes
 │         │
 │         ▼
 │    Send Email
 │         │
 └────────►Update Status = Sent
```

---

## Notes

- Semantic matching is performed using **Sentence Transformers**, without relying on large language model APIs.
- Email enrichment is executed only for qualified candidates to minimize API usage and reduce costs.
- The pipeline is modular, allowing the scoring model, candidate profile, or automation platform to be replaced independently.

---

## Author

**Akter Zaman**

Built as part of an AI Recruitment Automation assessment to demonstrate semantic candidate matching, cost-aware workflow design, and automated outreach preparation.
