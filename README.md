# Content Sunsetting Programme Console

**Every URL on the estate · 3 linked record sets · 10 decision states · 2 dashboards**

A tracking system that decides, page by page across a corporate website, which pages are
carried forward to a new content management system and which are switched off. It makes every
URL carry a named owner, a decision, a reason and a date.

I built and maintain this tool for the Digital Team at the Johannesburg Stock Exchange. The
system, schema, logic and dashboards here are the real ones. **Every value shown is
synthetic**, generated against a fictitious exchange, Sableridge Exchange. More on that in
[About the data](#about-the-data).

---

## What the tool is

"Sunsetting" is the controlled retirement of a web page: taking it out of service with a
recorded decision, reason and date, rather than editing or quietly deleting it.

A migration to a new content management system carries content forward one page at a time, so
every page becomes a decision. This tool is where those decisions are made, recorded and
reported on. It holds three linked record sets, an intake instrument issued to the business,
and a dashboard that reports progress.

## The problem it solves

The binding constraint is **ownership, not volume**.

On a website of any age, a given page usually cannot be traced to a person who will answer for
it. The team that requested it has reorganised, the person who owned it has moved on, or it
was never assigned to anyone. That gap blocks everything downstream:

- **Nobody can authorise a removal.** With no accountable owner there is nobody to ask and
  nobody to sign off, so the safe default is to keep the page. That default is what produces
  the accumulation in the first place.
- **A decision leaves no defensible record.** If a page is removed and someone asks six months
  later why, "it looked out of date" is not an answer.
- **Progress cannot be measured.** Without a per-page record there is no way to say how much
  has been reviewed, how much is agreed and how much has not been looked at.
- **Traffic alone will not decide it.** Page views are evidence, not a decision rule. A page
  with almost no traffic may carry a standing regulatory commitment. Volume ranks candidates;
  it does not settle them.
- **A migration inherits every unresolved page.** Anything not decided before the move is
  decided by default, in favour of carrying it across.

Before: ownership sat in people's heads and decisions lived in email. After: every URL carries
a named custodian, a decision, a reason and a date, in one model.

## What it does

<!-- Screenshots land here at Step 5. Filenames are fixed so this section needs no edit. -->

![Sunsetting Tracker dashboard](images/sunsetting-tracker.png)

*Page 1, Sunsetting Tracker. Headline measures for total pages, URLs discussed, procedures
confirmed and the confirmed percentage; the distribution of decisions across the estate;
breakdowns by division and by custodian; a progress stage view; and a page level detail table.
Sliceable by website section, decision and division.*

![Custodianship Analysis dashboard](images/custodianship-analysis.png)

*Page 2, Custodianship Analysis. Where accountability sits and where it is still missing,
broken down by custodian, division and website section.*

## Architecture and the data model

Everything joins on one key: `URL Links`, the page's web address.

| Record set | Answers | Rows |
|---|---|---|
| [Webpage_Custodianship_Records.xlsx](Webpage_Custodianship_Records.xlsx) | Who owns this page? | 12,710 |
| [Sunsetting_Outcome_Records.xlsx](Sunsetting_Outcome_Records.xlsx) | What was decided, why, and when? | 12,710 |
| [URL_Performance_Dataset.xlsx](URL_Performance_Dataset.xlsx) | What is it worth in traffic? | 9,727 |

Custodianship and outcomes are one to one over the same URL set: every page has both an
ownership record and an outcome record, even when that outcome is still unconfirmed.

Performance is deliberately narrower. A page absent from the sitemap has no traffic evidence
to hold, so it has no performance row. The gap between 12,710 and 9,727 is not missing data,
it is a population: pages the sitemap does not index, plus pages published after the
measurement window closed.

Field by field definitions, the controlled vocabularies and the measure semantics are in
**[Data_Dictionary.pdf](Data_Dictionary.pdf)**.

**Not every URL is a web page.** A content management system exposes stored files, such as
documents and images, as URLs too, and on a mature estate those outnumber public pages
heavily. They need decisions like anything else, so they are in scope, but they sit in their
own section of the model and are never mixed into a page count.

## How decisions are collected and recorded

The business owns the decision. The Digital Team owns the instrument and the record.

1. Every URL is compiled, classified by website section and grouped by the division
   responsible for it.
2. Each division receives a letter and the list of pages assigned to it, roughly two weeks
   before its verification discussion. Two weeks is calibrated against what is being asked: a
   division cannot answer from memory, it has to check which services are live and consult the
   people who requested the content.
3. The division records a decision per page on the response form,
   [Custodian_Page_URLs_Template.xlsx](Custodian_Page_URLs_Template.xlsx), using three values:
   **Keep**, **Consolidate** or **Sunset**.
4. A verification discussion is then held with the division, page by page.
5. Divisional decisions are translated into one of the tracker's ten procedures and written to
   the record with a reason and the discussion date.
6. Confirmed pages are unpublished or routed to their agreed destination.

Step 4 is the part that does the work. A returned form answers what a division wants. A
discussion establishes whether the division is the right party to be answering at all, and
surfaces the pages that two divisions both claim or neither claims.

Step 5 is a translation step, not a transcription step, and it is recorded as its own step so
the judgment stays visible in the audit trail. The form offers three values and the tracker
records ten, because a division is being asked what it wants for its content, not to select a
programme procedure.

The full process design, roles and timeline are in
**[Project_Brief.pdf](Project_Brief.pdf)**.

## How it is built

Three spreadsheet record sets, a validated intake workbook, and a two page Power BI model
joined on `URL Links`. The design decisions worth naming:

**One validated decision column, not three tick boxes.** The form captures the decision in a
single column constrained to a controlled list. Three independent booleans would let a
respondent tick two contradictory answers, or none, and "unanswered" would be
indistinguishable from "no".

**Decision values are constrained, never free text.** A misspelling in a free text decision
field is invisible until someone types the value correctly, at which point the chart silently
gains a second category and every roll up built on it is wrong.

**The consolidation target is free text on purpose.** A respondent has to be able to nominate
any page on the estate, so binding that field to a list of sample rows would quietly cap what
can be said.

**Drop down sources live on a dedicated `Lists` sheet.** The data region holds nothing but
data, so any extraction of it, by query, export or copy, picks up records and not stray
vocabulary sitting in spare columns.

**A page nobody owns cannot be marked for retirement.** This is enforced in the data rather
than left to discipline: a URL whose custodian is unconfirmed cannot carry a confirmed
decision. It is one of ten integrity rules, listed in Appendix A of the Data Dictionary, which
are executable checks rather than assertions in prose.

**Every table in the model is bound to a visual.** A table loaded but referenced by nothing is
dead weight that misleads whoever maintains the model next, because they cannot tell whether
removing it is safe.

**`URLs Discussed` and `Procedures Confirmed` are kept as separate measures.** A page can be
discussed and then deferred to a further session, so it is discussed but not confirmed. The gap
between the two is the programme's deferred backlog, and a single progress measure would hide
exactly the number worth watching.

## About the data

Every figure, name, URL and decision in this repository is synthetic, generated from a seeded
script against a fictitious organisation. The structure is real and the proportions are
invented. No live page address, custodian, division or decision appears anywhere in these
files.

The generator is not published, so nothing here asks you to take the synthetic data on trust
and then verify it yourself. What is published is the schema, the vocabularies, the integrity
rules and the measures, which are the parts worth reading.

## Limitations

- **Traffic is a measurement window, not a lifetime count.** It ranks pages against each
  other within that window and should not be read as total historical usage.
- **`Page Created` is defined and reserved rather than populated.** Page age is the most
  useful signal the model does not yet carry. Populating it from content management system
  metadata is the highest value extension to the model.
- **Measures are documented as specification.** What each one means and how it behaves under
  slicing is stated deliberately, rather than reverse engineered from a compiled model file.
- **Custodian counts include system labels.** Three values in the custodian column are not
  people: they mark content that no individual owns. That is correct behaviour, since the
  estate genuinely includes both, but a count of custodians is not a count of staff.

## Author

**Ndivhuwo** · ndivhuwojse@gmail.com · [linkedin.com/in/ndivhuwo-makhavhu](https://linkedin.com/in/ndivhuwo-makhavhu) · [github.com/MainDevWork](https://github.com/MainDevWork)
