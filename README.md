# ION Format

**Open Data Standard for Amateur Ice Hockey**

Version 1.0 · Released 2026-09-23 · [ionformat.org](https://ionformat.org)

---

## What is ION Format?

ION Format is an open, human-readable data standard for amateur ice hockey.  
Any app, any platform, any device — if it speaks ION, the data moves freely.

Inspired by the belief that **hockey data belongs to the players, not the platforms.**

---

## Why ION Format?

Today, every hockey app stores data in its own private format.  
Switch apps → lose your records. No portability. No interoperability.

ION Format fixes this:

- ✅ **Open** — free to use, free to implement
- ✅ **Human-readable** — plain text, no binary blobs
- ✅ **QR-friendly** — compact enough to fit in a single QR code
- ✅ **Versioned** — backward compatible across versions
- ✅ **Sport-specific** — designed for ice hockey from day one

---

## Record Types

| Type | Description | App |
|------|-------------|-----|
| `INX` | Game record (goals, shots, penalties) | IonNow |
| `ACE` | Lineup & roster | IonAce |
| `GYM` | Training session | IonGym |
| `KIT` | Equipment inventory | IonKit |
| `ICE` | Team & player data | IonIce |

---

## Common Header

Every ION record starts with a common header:

```
ION|1.0
TYPE|INX
SRC|IonNow|1.1
DATE|2026-09-23T06:11:24
```

| Field | Description |
|-------|-------------|
| `ION\|{version}` | Format version |
| `TYPE\|{type}` | Record type (INX / ACE / GYM / KIT / ICE) |
| `SRC\|{app}\|{version}` | Source app and version |
| `DATE\|{ISO8601}` | Creation timestamp |

---

## INX — Game Record

**Used by:** IonNow

```
ION|1.0
TYPE|INX
SRC|IonNow|1.1
DATE|2026-09-23T06:11:24

INX|1.6
T|슈퍼스타스 팀 도연|슈퍼스타스 팀 유진
V|광운대학교 아이스링크
S|2026-09-23T06:11:24
E|2026-09-23T07:16:31

G|A|2026-09-23T06:14:36
G|A|2026-09-23T06:22:30
G|B|2026-09-23T06:36:39
S|A|2026-09-23T06:44:12
P|B|2|2026-09-23T06:50:00
```

| Tag | Description |
|-----|-------------|
| `T\|A\|B` | Team names |
| `V\|venue` | Venue |
| `S\|datetime` | Game start |
| `E\|datetime` | Game end |
| `G\|side\|datetime` | Goal (A or B team) |
| `S\|side\|datetime` | Shot on goal |
| `P\|side\|min\|datetime` | Penalty (minutes) |

---

## ACE — Lineup Record

**Used by:** IonAce

```
ION|1.0
TYPE|ACE
SRC|IonAce|1.0
DATE|2026-09-23T10:00:00

ACE|1.0
T|슈퍼스타스
V|3
E|Tommy Sonn
D|2026-09-23T060000

G|1라인
P|LW||손태무
P|C||김민기
P|RW||박형관

G|D1
P|LD||홍길동
P|RD||김철수

G|GK
P|GK||정수호
```

| Tag | Description |
|-----|-------------|
| `T\|name` | Team name |
| `V\|version` | Lineup version number |
| `E\|editor` | Last editor |
| `D\|datetime` | Last updated |
| `G\|name` | Group (line) name |
| `P\|pos\|no\|name` | Player (position, jersey#, name) |

---

## GYM — Training Record

**Used by:** IonGym

```
ION|1.0
TYPE|GYM
SRC|IonGym|1.0
DATE|2026-09-23T07:00:00

GYM|1.0
A|Tommy Sonn|LW|InSeason
S|2026-09-23T07:00:00
E|2026-09-23T08:05:00

X|Squat|Lower|4|8|60
X|Deadlift|Lower|3|5|80
X|Plank|Core|3|||60
X|Running|Cardio|1|||0|5.0
```

| Tag | Description |
|-----|-------------|
| `A\|name\|pos\|phase` | Athlete info |
| `S\|datetime` | Session start |
| `E\|datetime` | Session end |
| `X\|exercise\|category\|sets\|reps\|weight\|duration\|distance` | Exercise entry |

---

## KIT — Equipment Record

**Used by:** IonKit

```
ION|1.0
TYPE|KIT
SRC|IonKit|1.0
DATE|2026-09-23T12:00:00

KIT|1.0
O|Tommy Sonn

I|Skates|Bauer|Supreme M5 Pro|Excellent|2025-03-01|850000
I|Stick|CCM|Jetspeed FT6 Pro|Good|2026-01-15|280000
I|Helmet|Bauer|Re-Akt 200|Good|2024-09-01|320000
I|Jersey|Reebok|Legacy|Excellent|2020-05-01|150000
```

| Tag | Description |
|-----|-------------|
| `O\|name` | Owner |
| `I\|category\|brand\|model\|condition\|date\|price` | Item |

---

## Versioning

ION Format follows semantic versioning:

```
MAJOR.MINOR.PATCH
  1  .  0  .  0
```

- **MAJOR** — breaking changes (rare)
- **MINOR** — new tags added (backward compatible)
- **PATCH** — bug fixes to spec

**Rule:** A parser for `1.x` must be able to read any `1.y` file where `y <= x`.  
Unknown tags are silently ignored.

---

## QR Encoding

ION records are designed to fit within a single QR code (Version 40, ECC M):

- Max capacity: ~2,700 bytes per QR
- Encoding: UTF-8
- If data exceeds limit, split with header:
  ```
  ION-PART|1/3|{data}
  ION-PART|2/3|{data}
  ION-PART|3/3|{data}
  ```

---

## Implementing ION Format

Anyone can implement ION Format. No license required.

**To add ION support to your app:**
1. Parse the common header to identify record type and version
2. Implement the parser for the record type(s) you need
3. Unknown tags must be silently ignored (forward compatibility)
4. Optionally register your app at [ionformat.org/apps](https://ionformat.org/apps)

---

## Contributing

ION Format is community-driven.

- 💬 [Discussions](https://github.com/ionformat/spec/discussions) — propose new features
- 🐛 [Issues](https://github.com/ionformat/spec/issues) — report spec problems
- 📝 [Pull Requests](https://github.com/ionformat/spec/pulls) — submit changes

**Process:**
1. Open a Discussion with your proposal
2. Community feedback (2 weeks minimum)
3. Core team review
4. Merge into next MINOR version

---

## Record Type Registry

Want to propose a new record type?  
Open a Discussion with prefix `[NEW TYPE]` and include:

- Type code (3 letters)
- Use case description
- Proposed tag structure
- Reference implementation

---

## License

ION Format specification is released under **CC0 1.0 Universal**.  
Free to use, implement, and extend — no attribution required.

---

## About

ION Format was created on 2026-09-24 by [IonIce, Inc.](https://ionice.hockey)
as part of the [IonIce](https://ionice.hockey) project.

> *"Hockey data belongs to the players, not the platforms."*

---

**ionformat.org** · [GitHub](https://github.com/ionformat/spec) · [IonIce](https://ionice.hockey)
