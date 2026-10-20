# Option schema (one object per registrable section)

```json
{
  "id": "ECON 410-002",            // unique: course-section
  "group": "Econ core",            // used for color + filter chip (see GROUPS in template)
  "course": "ECON 410",            // components such as recitations use their own course key, e.g. "ECON 410 Recitation"
  "sec": "002", "cls": "1640",     // section and class number
  "title": "Intermediate Microeconomics",
  "credits": 4.0,                  // recitations/labs that carry no credit: 0
  "instr": "Michelle Sheran-Andrews",   // "TBA" when not posted
  "honors": false, "optional": false,
  "avail": 200, "cap": 200,        // seats available / capacity
  "note": "free text shown in the section list",
  "meetings": [{"d": "TR", "s": 840, "e": 915, "room": "Stone Center 0103"}],  // d uses M T W R F; s/e are minutes after midnight
  "rmp": {"overall":"4.6","diff":"3.7","wta":"91","n":"221","id":"","conf":"High"},   // null if no page
  "src": "cc"                      // "cc" = ConnectCarolina, "public" = public class search
}
```

Presets: `[{"name":"Plan 1","tag":"short label","keys":["BUSI 100-002", ...]}]` where keys are option ids.
