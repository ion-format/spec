# ION Format Specification v1.0

Status: Draft
Date: 2026-09-24
Authors: IonIce, Inc.

---

## 1. Design Principles

### 1.1 Human Readable
ION records are plain text. A person can read and understand a record without any tools.

### 1.2 Minimal
Every tag earns its place. No required fields that aren't truly required.

### 1.3 Pipe-Delimited
Fields within a line are separated by `|`. This avoids ambiguity with commas in names.

### 1.4 Line-Based
Each line is a self-contained record. Parsers can stream line by line.

### 1.5 Extensible
Unknown tags are ignored. New tags can be added in MINOR versions without breaking existing parsers.

---

## 2. File Structure

```
{COMMON HEADER}
{BLANK LINE}
{TYPE HEADER}
{BLANK LINE}
{RECORDS}
```

### 2.1 Common Header

```
ION|{version}
TYPE|{type_code}
SRC|{app_name}|{app_version}
DATE|{ISO8601_datetime}
```

All four lines are required.

### 2.2 Type Header

Each record type defines its own type header line:

```
INX|{spec_version}
ACE|{spec_version}
GYM|{spec_version}
KIT|{spec_version}
ICE|{spec_version}
```

### 2.3 Encoding

- Character encoding: **UTF-8**
- Line endings: **LF** (`\n`) or **CRLF** (`\r\n`)
- Blank lines are permitted and ignored between records

---

## 3. Data Types

| Type | Format | Example |
|------|--------|---------|
| String | UTF-8 text, no `\|` | `슈퍼스타스` |
| DateTime | ISO 8601 | `2026-09-23T06:11:24` |
| Timestamp | Unix epoch (integer) | `1758999084` |
| Integer | Decimal digits | `42` |
| Float | Decimal with `.` | `60.5` |
| Side | `A` or `B` | `A` |

**Empty fields:** Use empty string between pipes: `P|LW||손태무` (jersey# omitted)

---

## 4. INX Specification (Game Record)

### 4.1 Version History

| Version | Changes |
|---------|---------|
| 1.0 | Initial release |
| 1.1 | Added venue field |
| 1.5 | Added estimated start flag |
| 1.6 | Added shot on goal (`S` tag) |

### 4.2 Tags

```
INX|{version}

# Required
T|{team_a}|{team_b}          Team names

# Optional
V|{venue}                     Venue name
S|{datetime}[|EST]            Game start (EST = estimated)
E|{datetime}                  Game end

# Events (one per line, ordered by time)
G|{side}|{datetime}[|{scorer_jersey}]     Goal
S|{side}|{datetime}[|{shooter_jersey}]    Shot on goal  
P|{side}|{minutes}|{datetime}[|{jersey}]  Penalty
U|{datetime}                              Unclassified tap
```

### 4.3 Example

```
ION|1.0
TYPE|INX
SRC|IonNow|1.1
DATE|2026-09-23T07:22:00

INX|1.6
T|슈퍼스타스 팀 도연|슈퍼스타스 팀 유진
V|광운대학교 아이스링크
S|2026-09-23T06:11:24
E|2026-09-23T07:16:31

G|A|2026-09-23T06:14:36
G|A|2026-09-23T06:22:30
G|A|2026-09-23T06:25:40
G|A|2026-09-23T06:27:47
G|B|2026-09-23T06:36:39
G|B|2026-09-23T06:40:45
G|A|2026-09-23T06:55:52
G|A|2026-09-23T07:01:27
G|A|2026-09-23T07:03:18
S|A|2026-09-23T06:44:12
```

---

## 5. ACE Specification (Lineup Record)

### 5.1 Version History

| Version | Changes |
|---------|---------|
| 1.0 | Initial release |

### 5.2 Tags

```
ACE|{version}

T|{team_name}                 Team name
V|{version_number}            Lineup version (integer, increments on each save)
E|{editor_name}               Last editor
D|{datetime}                  Last updated

# Change history (up to 5 recent)
H|{version}|{editor}|{datetime}[|{memo}]

# Groups and players
G|{group_name}                Start of a group (line)
P|{position}|{jersey}|{name}  Player in current group
```

### 5.3 Positions

`LW` `C` `RW` `LD` `RD` `GK`

Empty position field means unassigned.

### 5.4 Example

```
ION|1.0
TYPE|ACE
SRC|IonAce|1.0
DATE|2026-09-23T10:22:00

ACE|1.0
T|슈퍼스타스
V|3
E|Tommy Sonn
D|2026-09-23T10:22:00
H|2|Tommy Sonn|2026-09-23T09:15:00|골리 변경
H|1|Tommy Sonn|2026-09-23T08:00:00|최초 작성

G|1라인
P|LW||손태무
P|C||김민기
P|RW||박형관

G|2라인
P|LW||이도연
P|C||최진우
P|RW||박유진

G|D1
P|LD||홍길동
P|RD||김철수

G|GK
P|GK|1|정수호
P|GK|30|박민준
```

---

## 6. GYM Specification (Training Record)

### 6.1 Version History

| Version | Changes |
|---------|---------|
| 1.0 | Initial release |

### 6.2 Tags

```
GYM|{version}

A|{name}|{position}|{phase}   Athlete (phase: OffSeason/PreSeason/InSeason)
S|{datetime}                  Session start
E|{datetime}                  Session end
N|{note}                      Session note

# Exercise entry
X|{name}|{category}|{sets}|{reps}|{weight_kg}|{duration_sec}|{distance_km}
```

### 6.3 Categories

`Lower` `Core` `Upper` `Cardio` `Agility` `Flexibility`

### 6.4 Example

```
ION|1.0
TYPE|GYM
SRC|IonGym|1.0
DATE|2026-09-23T08:05:00

GYM|1.0
A|Tommy Sonn|LW|InSeason
S|2026-09-23T07:00:00
E|2026-09-23T08:05:00

X|Squat|Lower|4|8|60||
X|Deadlift|Lower|3|5|80||
X|Box Jump|Lower|3|10|||
X|Plank|Core|3|||60|
X|Running|Cardio|1||||5.0
```

---

## 7. KIT Specification (Equipment Record)

### 7.1 Version History

| Version | Changes |
|---------|---------|
| 1.0 | Initial release |

### 7.2 Tags

```
KIT|{version}

O|{owner_name}                Owner

# Item
I|{category}|{brand}|{model}|{condition}|{purchase_date}|{price_krw}
M|{category}|{brand}|{type}|{date}|{cost}  Maintenance record
```

### 7.3 Conditions

`BrandNew` `Excellent` `Good` `Fair` `Worn`

### 7.4 Categories

`Skates` `Stick` `Helmet` `Jersey` `Gloves` `Pants` `ShoulderPad` `ElbowPad` `ShinGuard` `Bag` `Other`

---

## 8. Parsing Rules

### 8.1 Required behavior

1. Parse the common header first
2. Check `ION|{version}` — reject if major version is unsupported
3. Read `TYPE` to determine record type
4. Parse known tags; **silently ignore unknown tags**
5. Missing optional fields = treat as absent (not error)

### 8.2 Error handling

| Situation | Behavior |
|-----------|----------|
| Unknown tag | Ignore and continue |
| Missing required tag | Report error |
| Malformed field | Skip line, continue |
| Unknown major version | Reject with error |
| Unknown minor version | Parse with best effort |

### 8.3 Forward compatibility

A parser for `1.0` reading a `1.6` file must:
- Parse all tags it knows
- Ignore all tags it doesn't know
- NOT fail because of unknown tags

---

## 9. QR Encoding

### 9.1 Single QR

- Max safe payload: 2,700 bytes (UTF-8)
- Error correction: Level M
- Version: Auto

### 9.2 Multi-part QR

When payload exceeds 2,700 bytes:

```
ION-PART|{index}/{total}
{payload_chunk}
```

Example (3 parts):
```
ION-PART|1/3
ION|1.0
TYPE|INX
...
```

Parsers must:
1. Detect `ION-PART` header
2. Collect all parts
3. Concatenate in order
4. Parse as single record

---

## 10. Changelog

### v1.0 (2026-09-23)
- Initial public release
- Record types: INX 1.6, ACE 1.0, GYM 1.0, KIT 1.0
- Common header defined
- QR encoding rules defined
- Versioning rules defined

---

## Appendix A: Comparison with Existing Formats

| Platform | Format | Open? | Portable? |
|----------|--------|-------|-----------|
| TeamSnap | Proprietary JSON/API | ❌ | ❌ |
| SportsEngine | Proprietary | ❌ | ❌ |
| Mad Puck | Proprietary | ❌ | ❌ |
| GameSheet | Proprietary PDF/CSV | ❌ | Partial |
| **ION Format** | **Open text** | **✅** | **✅** |

---

## Appendix B: Design Decisions

**Why pipe (`|`) not comma?**  
Player names and team names often contain commas in some languages. Pipe is rare in proper nouns.

**Why not JSON or XML?**  
JSON/XML are too verbose for QR codes. ION records need to fit in ~2,700 bytes. A plain text format is 3-5× more compact.

**Why not CSV?**  
CSV has no concept of record type or version. ION needs both for forward compatibility.

**Why ISO 8601 for dates?**  
Unambiguous across locales and timezones. Sortable as string.

---

*ION Format Specification v1.0 — CC0 1.0 Universal*  
*ionformat.org · github.com/ionformat/spec*
