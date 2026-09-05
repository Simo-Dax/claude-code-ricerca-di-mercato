# output/

Qui scrivono le skill. Una cartella per brand: apri Claude Code nella cartella del brand e
gli output finiscono sotto.

```
output/
├── 01_VOC_Research/   voc-*.html, personas-*.html      ← /pm-dati-qualitativi, /pm-personas
├── 03_Ad_Spy/         swipe file Meta + _scratch/      ← /pm-competitor-spy, /pm-competitor-spy-video
│   └── google/        swipe file Google Transparency   ← /pm-google-spy
├── intermediate/      i .md che gli agenti si passano  ← SA1, SA2, /pm-insight
└── final/             i deliverable compilati
```

Regola: il testo che passa fra agenti sta in `intermediate/`, gli asset e gli HTML nelle
cartelle numerate, i deliverable chiusi in `final/`.
