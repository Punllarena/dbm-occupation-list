# DBM Occupation List (Philippines)

Machine-readable CSV of the Philippine government position classification index —
every occupation title with its salary grade, occupational service, occupational
group, and class ID.

Extracted from **DBM Budget Circular No. 2022-2**, *"Index of Occupational Services,
Occupational Groups, Classes and Salary Grades (IOS), CY 2022 Edition"*, issued
April 5, 2022 by the Department of Budget and Management.

Source: <https://www.dbm.gov.ph/index.php?view=article&id=174:budget-circular-no-2022-2>

## Why this exists

The official IOS is published only as PDF volumes. This repo makes it queryable —
`grep`, `pandas`, SQL, a spreadsheet, whatever. Useful for HR/plantilla work,
payroll and salary-grade lookups, job-posting normalization, and public-sector
compensation research.

## The file

`occupations.csv` — 2,575 rows, UTF-8 (with BOM), RFC 4180 quoted (some titles
contain commas, e.g. `"Chairman, Constitutional Commission"`).

| Column | Description | Example |
|---|---|---|
| `Occupation` | Position title as printed in the IOS | `Accountant III` |
| `Salary Grade` | Salary grade, integer 1–33 | `19` |
| `Occupational Service Code` | Service the position belongs to | `02-FS` |
| `Occupational Group Code` | Group within the service (213 distinct) | `AC` |
| `Class ID` | IOS class identifier, unique per row | `A3` |

```csv
Occupation,Salary Grade,Occupational Service Code,Occupational Group Code,Class ID
Accountant I,12,02-FS,AC,A1
Accountant II,16,02-FS,AC,A2
Accountant III,19,02-FS,AC,A3
```

### Occupational services

Twenty services. The gloss below is inferred from the member occupations in each
service — consult the DBM PDF for the exact official titles.

| Code | Gloss | Rows |
|---|---|---|
| 01-GA | General administration | 188 |
| 02-FS | Finance | 206 |
| 03-PS | Planning and statistics | 105 |
| 04-AE | Architecture and engineering | 71 |
| 05-TC | Transportation and communications | 174 |
| 06-CT | Crafts and trades | 179 |
| 07-SS | Social services | 226 |
| 08-IA | Information and arts | 167 |
| 09-MH | Medical and health | 186 |
| 10-EL | Education and library | 237 |
| 11-AA | Agriculture and agrarian reform | 127 |
| 12-TT | Trade and tourism | 78 |
| 13-MP | Sciences and meteorology | 119 |
| 14-DS | Defense and security | 167 |
| 15-LJ | Legal and judicial | 132 |
| 16-FR | Foreign relations | 31 |
| 17-MS | Miscellaneous services | 53 |
| 18-ES | Executive service | 120 |
| 19-LS | Legislative service | 4 |
| 20-CM | Corporate management | 5 |

## Usage

```python
import csv

with open("occupations.csv", encoding="utf-8-sig") as f:
    rows = list(csv.DictReader(f))

sg22 = [r["Occupation"] for r in rows if int(r["Salary Grade"]) == 22]
```

```bash
grep -i 'nurse' occupations.csv
```

Note the `utf-8-sig` encoding — the file starts with a BOM.

## Caveats

- **Repeated titles are not duplicates.** The same title can appear more than once
  with a different salary grade and class ID (e.g. `Administrative Officer I` at
  SG 10 / `ADOF1` and SG 11 / `ADO1`). `Class ID` is the unique key, not
  `Occupation`.
- **One known bad value.** `Youth Development Assistant I` (`07-SS`, class `YDA1`)
  carries salary grade `58`, outside the valid 1–33 range. Extraction artifact;
  left as-is rather than silently guessed. Verify against the PDF before use.
- **Snapshot, not a live feed.** CY 2022 edition. DBM issues later circulars and
  salary grades are periodically adjusted (e.g. by Salary Standardization Law
  tranches) — the salary grade *assignments* here may still hold while the peso
  amounts attached to each grade do not. This repo contains no peso amounts.
- **Unofficial.** Machine extraction from a PDF. Not affiliated with or endorsed
  by the DBM. For anything binding, use the official circular.

## License

The underlying data is a work of the Philippine government and is not covered by
copyright per Sec. 176 of the Intellectual Property Code (RA 8293). This
compilation is released under [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/) —
use it freely.
