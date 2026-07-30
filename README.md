A tracking system that records the outcome of a website content audit across 12,710 pages. For every page it holds a named custodian, a decision, the reason for that decision and the date it was agreed. Three linked Excel record sets and a validated intake form feed a two page Power BI dashboard that reports the progress of the audit and where accountability remains outstanding.

It was built for the **Digital Team** at the Johannesburg Stock Exchange, which runs the audit ahead of the website's migration to a new content management system. Every page is assessed before the move. Pages that are retained are carried forward, and pages that are not are sunsetted, meaning they are withdrawn from the site against a recorded decision, reason and date.

## What the dashboard reports

![Sunsetting Procedure Tracker dashboard](images/sunsetting-tracker.png)

*Page 1, Sunsetting Procedure Tracker. Total pages, how many have been discussed, how many have a confirmed decision and what percentage that represents, the distribution of decisions across the site, breakdowns by division and by custodian, a progress stage view, and a page level detail list. Filterable by website section, decision and division.*

![Custodianship Analysis dashboard](images/custodianship-analysis.png)

*Page 2, Custodianship Analysis. Where accountability sits and where it is still outstanding, broken down by custodian, by division and by website section, with a business unit filter.*

**A note on the data.** Every value in this repository is generated. It exists to populate the tool and demonstrate that the tracking works, and it contains no real page addresses, custodians, divisions or decisions. Only the contents of the cells are invented. The system itself, meaning its structure, fields, validation rules, controlled vocabularies and dashboards, is the one in use. There is more on this in [About the data](#about-the-data).

---

## What is in this repository

| File                                                                    | Description                                                                                                                                                                                 |
| ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Webpage_Custodianship_Records.xlsx](Webpage_Custodianship_Records.xlsx) | Who is responsible for each of the 12,710 pages. A named person per page, the division and business unit they sit in, and whether that person has confirmed it or it is still an assumption |
| [Sunsetting_Outcome_Records.xlsx](Sunsetting_Outcome_Records.xlsx)       | What was decided for each of the same 12,710 pages, the reason for it, and the date it was agreed. One decision per page, chosen from a fixed list of ten                                   |
| [URL_Performance_Dataset.xlsx](URL_Performance_Dataset.xlsx)             | How much traffic each page gets. It covers 9,727 pages, fewer than the other two, and the data model section below explains why that gap is deliberate                                      |
| [Custodian_Page_URLs_Template.xlsx](Custodian_Page_URLs_Template.xlsx)   | The form sent out to a division. It lists their pages and gives them one column to say what should happen to each one                                                                       |
| [Sunsetting_Console_Dashboard.pbix](Sunsetting_Console_Dashboard.pbix)   | The Power BI file behind the two dashboards shown above. Open it if you have Power BI Desktop                                                                                               |
| [Data_Dictionary.pdf](Data_Dictionary.pdf)                               | 14 pages. Every field, what it means, what values it accepts, what each dashboard measure calculates, and the ten rules the data has to pass                                                |
| [Project_Brief.pdf](Project_Brief.pdf)                                   | 5 pages. What the programme covers, who is responsible for what, how a page gets assessed, and over what timeline                                                                           |

The three record sets are the tool's data, the template is how that data gets collected, and the dashboard is how it gets read. Nothing needs installing beyond Excel, and Power BI Desktop only if you want to open the model yourself.

## The problem

An audit of this scale is constrained by accountability rather than by volume. A decision to withdraw a page has to be authorised by the person responsible for it, and on a website of any size that responsibility is rarely recorded anywhere. The team that commissioned a page has been reorganised, the person who owned it has moved on, or it was never formally assigned.

Five problems follow from that, and the tool is built to address each one:

- **A removal cannot be authorised.** With no accountable owner there is no one to ask and no one to sign off, so the default outcome is that the page is retained.
- **A decision cannot be defended.** If a page is withdrawn and the reason is questioned six months later, an undocumented judgment is not a sufficient answer at a regulated organisation.
- **Progress cannot be reported.** Without a record for each page there is no basis for stating how much of the audit has been reviewed, how much is agreed and how much is outstanding.
- **Traffic ranks pages but does not decide them.** Page views are evidence, not a decision rule. A page with minimal traffic may exist to satisfy a standing regulatory commitment.
- **A migration carries across whatever is left undecided.** Any page without a decision at the point of the move is retained by default, which is the outcome the audit exists to prevent.

The tool therefore establishes ownership before it records decisions. Every page carries a named custodian, a decision, a reason and a date, held in one model.

## How the three files fit together

Everything joins on one thing: `URL Links`, the page's web address. The same list of addresses runs through all three files, so any page can be read across ownership, decision and traffic in one go.

```
Custodian_Page_URLs_Template.xlsx        the form
  one validated Decision column            sent to a division, returned page by page
        |
        v
  verification discussion                  held with the division, page by page
        |
        v
+---------------------------+---------------------------+
|                           |                           |
Webpage_Custodianship       Sunsetting_Outcome          URL_Performance
_Records.xlsx               _Records.xlsx               _Dataset.xlsx
who owns this page          what was decided,           what it is worth
                            why, and when               in traffic
12,710 rows                 12,710 rows                 9,727 rows
|                           |                           |
+---------------------------+---------------------------+
                            |
                    joined on URL Links
                            |
                            v
            Sunsetting_Console_Dashboard.pbix
            Page 1   Sunsetting Procedure Tracker
            Page 2   Custodianship Analysis
```

Ownership and outcomes cover exactly the same set of pages, one row each. Every page has an ownership record and an outcome record even when that outcome is still "not decided yet", because a page nobody has got to is something you need to be able to count. An unanswered page is a row, not a blank.

Traffic covers fewer pages, and the reason matters. A sitemap is the machine readable index of a site's pages, the file search engines use to find them. A page that is not in the sitemap has no traffic record, so it has no row in the performance file. The difference between 12,710 and 9,727 is therefore not missing data. It is a specific group: pages the sitemap does not index, plus pages published after the traffic measurement window closed. The Data Dictionary defines that group explicitly, so nobody reads the gap as a data quality problem.

**Not every URL is a web page.** A content management system hands out addresses for stored files too, such as PDFs and images, and on a site of any size there are far more of those than actual pages. They need decisions like everything else, so they are in scope, but they sit in their own section of the model and are never counted as pages.

Field by field definitions, the full list of accepted values and what each measure calculates are all in **[Data_Dictionary.pdf](Data_Dictionary.pdf)**.

## How the decisions get collected

The principle underneath the whole process is a simple one. **The business owns the decision. The Digital Team owns the instrument and the record.** Keeping that line clean is most of the process design.

1. Every page on the site is compiled into a list, classified by which section of the website it belongs to, and grouped by the division responsible for it.
2. Each division gets a letter and the list of its own pages, roughly two weeks before we sit down with them. Two weeks is not arbitrary. A division genuinely cannot answer this from memory. They have to go and check which services are still live and talk to whoever asked for the content originally.
3. The division records a decision against each of its pages on the form, [Custodian_Page_URLs_Template.xlsx](Custodian_Page_URLs_Template.xlsx), choosing between **Keep**, **Consolidate** or **Sunset**.
4. We then hold a verification discussion with the division and go through it page by page.
5. What the division decided is translated into one of the tracker's ten procedures and written to the record along with a reason and the date we discussed it.
6. Confirmed pages are then unpublished or sent wherever they were agreed to go.

**Step 4 does the real work.** A returned form tells you what a division wants. The conversation tells you whether that division is the right one to be asking, and it finds the pages that two divisions both claim or that neither will take. A form on its own never finds those.

**Step 5 is a translation, not a transcription.** I record it as its own step so the judgment involved stays in the audit trail. The form offers three options and the tracker records ten, and that mismatch is intentional: a division is being asked what it wants to happen to its content, not to pick the correct internal procedure. Making the form match the tracker would have been easier to build and harder to fill in.

The full process, the roles and the timeline are in **[Project_Brief.pdf](Project_Brief.pdf)**.

## How it is built

Three spreadsheet record sets, one validated intake workbook, and a two page Power BI model joined on `URL Links`. Each design decision below is followed by the specific failure it prevents.

**One validated decision column, not three tick boxes.** The form captures the decision in a single column limited to a fixed list. With three separate tick boxes, somebody can tick two contradictory answers or none at all, and a blank cannot be told apart from a deliberate "no".

**Decision values come from a fixed list, never free text.** A misspelling in a free text decision field stays invisible until somebody types the value correctly. At that point the chart gains a second category and every total built on it is wrong. Nothing errors. You get the wrong number.

**The consolidation target is free text on purpose.** If a division wants a page merged into another one, they have to be able to name any page on the site. Restricting that field to a list would limit what they can tell us.

**Drop down lists live on their own `Lists` sheet.** The sheet holding the data holds nothing but data, so exporting, querying or copying it picks up records only, not vocabulary parked in spare columns.

**A page nobody owns cannot be marked for retirement.** If a page's custodian is unconfirmed, it cannot carry a confirmed decision. This is enforced in the data, not left to good intentions. It is one of ten integrity rules in Appendix A of the Data Dictionary, and all ten are executable checks.

**Every table in the model is connected to a visual.** A table that is loaded but used by nothing misleads the next person to maintain the model, because they cannot tell whether removing it is safe.

**`URLs Discussed` and `Procedures Confirmed` are two separate measures.** A page can be discussed and then held over for another session, so it is discussed but not confirmed. The gap between the two numbers is the backlog. A single progress measure would hide it.

## Tools and technologies

| Tool                       | What it does here                                                                                                                           |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **Microsoft Excel**  | The three record sets and the intake form, built with structured tables, data validation and controlled lists on a dedicated`Lists` sheet |
| **Power BI Desktop** | The data model, the measures and both report pages                                                                                          |
| **Power Query**      | Connecting to the source files and shaping them on load                                                                                     |
| **DAX**              | The measures, and the calculated`Tracking Stage` column behind the progress view                                                          |
| **Git / GitHub**     | Version control and publishing this repository                                                                                              |

## About the data

Every figure, name, address and decision in this repository is synthetic, produced by a seeded script against a fictitious organisation. The structure is real and the numbers are invented. No live page address, custodian, division or decision appears anywhere in these files.

The generator itself is not published, so nothing here asks you to take the synthetic data on trust and then go and verify it yourself. What is published is the structure, the vocabularies, the integrity rules and the measures, which are the parts actually worth reading.

## Limitations

- **Traffic is a measurement window, not a lifetime total.** It is useful for ranking pages against each other within that window, and should not be read as how many people have ever visited a page.
- **`Page Created` is defined and reserved rather than filled in.** How old a page is would be the single most useful signal the model does not yet carry, and populating it from the content management system's own metadata is the most valuable thing that could be added next.
- **The measures are documented as a specification.** What each one means and how it behaves when you filter the report is written down deliberately, rather than reverse engineered out of a compiled file after the fact.
- **The custodian count includes system labels.** Three of the values in the custodian column are not people. They mark content that no individual owns, such as the media library. That is correct, because the site genuinely contains both, but it does mean a count of custodians is not a count of staff.

## Author and contact

**Ndivhuwo**, design, build and documentation.

- Email: [ndivhuwojse@gmail.com](mailto:ndivhuwojse@gmail.com)
- LinkedIn: [www.linkedin.com/in/ndivhuwo-makhavhu](https://www.linkedin.com/in/ndivhuwo-makhavhu)
- GitHub: [github.com/MainDevWork](https://github.com/MainDevWork)

[**Back to the top**](#content-sunsetting-programme-console)
