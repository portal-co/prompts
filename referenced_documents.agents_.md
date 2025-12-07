# AI Agent Documentation Standards: Source Attribution and Verification

## Purpose
This document defines requirements for AI agents when referencing external sources, handling ambiguous information, and maintaining documentation integrity. All agents must clearly distinguish between verified authoritative sources and inferred or training-derived information.

---

## 1. **Source Citation Requirements**

### Authoritative Sources
When AI agents reference information from verified sources, citations **must** include:

**For Documents (PDFs, DOCs, etc.):**
```
Source: <document-name> — <file-path-or-URL>
Location: Page X, Section Y (or specific location identifier)
Retrieved: <ISO-timestamp>
```

**For Web Resources:**
```
Source: <page-title> — <URL>
Section: <specific-section-or-heading>
Retrieved: <ISO-timestamp>
Archived: <archive-URL-if-available>
```

**For APIs/Databases:**
```
Source: <API-name> — <endpoint-or-query>
Version: <API-version>
Retrieved: <ISO-timestamp>
```

**For Code Repositories:**
```
Source: <repo-name> — <repo-URL>
Location: <file-path>, Lines X-Y or Commit <hash>
Retrieved: <ISO-timestamp>
```

**For Specifications/Standards:**
```
Source: <spec-name> (e.g., RFC 1234, ISO 9001)
Section: <specific-section>
Version: <version-number>
Published: <publication-date>
```

### Citation Format in Code/Documentation
```python
# Source: Hardware Manual v2.3 — docs/hardware_spec.pdf, Page 45
# Retrieved: 2025-12-07T09:00:00Z
# Verified timeout value for DMA controller initialization
TIMEOUT_MS = 500
```

---

## 2. **Handling Uncertain or Ambiguous Information**

### TODO:VERIFY Tag
Always mark ambiguous, inferred, or training-derived results with `TODO:VERIFY` and a concise rationale:

**Training-Derived Information:**
```python
# TODO:VERIFY — Inferred from AI training data; no explicit source citation available
# Rationale: Common pattern in industry but not verified against project specs
```

**OCR or Parsed Content:**
```python
# TODO:VERIFY — Extracted via OCR from AGCIS manual page 127
# Rationale: OCR confidence 87%, may contain transcription errors
```

**Incomplete Source:**
```python
# TODO:VERIFY — Derived from partial documentation in legacy_notes.txt
# Rationale: Original source document appears incomplete or corrupted
```

**Inference from Context:**
```python
# TODO:VERIFY — Inferred from related API behavior and error messages
# Rationale: No explicit documentation found; based on observed system behavior
```

**Multiple Conflicting Sources:**
```python
# TODO:VERIFY — Conflicting information in user_guide.pdf (p.12) vs. api_ref.html (section 3.2)
# Rationale: Chose api_ref.html value as more recent; requires expert review
```

### TODO:VERIFY Requirements
1. **Mandatory Components:**
   - `TODO:VERIFY` tag for searchability
   - One-line rationale explaining uncertainty
   - Indication of source type (training-derived, OCR, inference, etc.)
2. **Placement:** Add immediately above or adjacent to the uncertain code/content
3. **Conciseness:** Keep entries brief but informative (1-2 lines)
4. **Tracking:** Log all TODO:VERIFY items in change log for human review

### UNKNOWN Tag
For behaviors or information that cannot be determined, use the `UNKNOWN` tag:

```python
# UNKNOWN — Hardware race condition on concurrent writes
# Sources checked: hardware_spec.pdf, errata_v1.2.txt, mailing list archives
# Recommendation: Requires hardware testing or vendor clarification
```

**UNKNOWN Requirements:**
1. **Document Sources Checked:** List what was consulted
2. **Explain Why Unknown:** State what information is missing
3. **Provide Recommendations:** Suggest next steps (testing, expert consultation, etc.)
4. **Flag for Review:** Add to change log for human attention

---

## 3. **Training-Derived Information Policy**

### Explicit Marking
All information derived from AI training data rather than project-specific sources **must** be explicitly marked:

```python
# TRAINING-DERIVED — Common industry pattern for rate limiting
# TODO:VERIFY — Should be validated against project requirements
# No authoritative source within project documentation
RATE_LIMIT_REQUESTS = 100  # requests per minute
```

### When Training-Derived Information is Acceptable
- **Boilerplate code:** Standard patterns (e.g., error handling, logging)
- **Common algorithms:** Well-known implementations (e.g., sorting, searching)
- **Best practices:** Industry-standard approaches (e.g., input validation)
- **Placeholder values:** Clearly marked as examples requiring configuration

### When Training-Derived Information is NOT Acceptable
- **Security-critical values:** Timeouts, buffer sizes, cryptographic parameters
- **Business logic:** Domain-specific rules, calculations, workflows
- **Hardware specifications:** Timing values, register addresses, protocol details
- **API contracts:** Endpoint behavior, data formats, error codes

**Rule:** When in doubt, mark as `TODO:VERIFY` and require human review.

---

## 4. **Resolving TODOs and UNKNOWNs**

### Resolution Process
1. **Locate Authoritative Source:** Find verified documentation, run tests, or consult experts
2. **Update Code/Documentation:** Replace TODO/UNKNOWN with proper citation
3. **Log Resolution:** Document findings in change log
4. **Update Reference Index:** Add new authoritative citations to central reference file

### Resolution Template
```python
# RESOLVED: 2025-12-07T14:30:00Z by <reviewer-name>
# Source: Hardware Manual v2.3 — docs/hardware_spec.pdf, Page 45
# Previously: TODO:VERIFY — Training-derived timeout value
TIMEOUT_MS = 500  # Verified: DMA controller initialization timeout
```

### Central Reference Index
Maintain a central reference file (e.g., `ref/AUTHORITATIVE_SOURCES.md`) listing:
- All authoritative documentation sources
- Version numbers and retrieval dates
- Responsible maintainers for each source category
- Update schedule for time-sensitive sources

**Update Rule:** When resolving TODOs, add new sources to the reference index with full citation details.

---

## 5. **Search and Audit**

### Searchability Requirements
All tags must use consistent formatting for easy searching:
- `TODO:VERIFY` — For uncertain/ambiguous information
- `UNKNOWN` — For genuinely unknown behaviors
- `TRAINING-DERIVED` — For AI training data sources
- `RESOLVED` — For previously uncertain information now verified

### Audit Commands
```bash
# Find all verification tasks
grep -r "TODO:VERIFY" .

# Find unknown behaviors
grep -r "UNKNOWN" .

# Find training-derived code
grep -r "TRAINING-DERIVED" .

# Check resolution history
grep -r "RESOLVED" . | grep "$(date +%Y-%m)"
```

### Periodic Review
- **Weekly:** Review new TODO:VERIFY and UNKNOWN entries
- **Monthly:** Audit resolution rate and update reference index
- **Quarterly:** Validate authoritative source versions are current

---

*These documentation standards are mandatory for all AI agent operations in this repository. For questions, contact the repository maintainers.*

