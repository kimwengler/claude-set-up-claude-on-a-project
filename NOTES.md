# NOTES

## CLAUDE.md – was drin ist und was nicht

**Drin:**
- **Beschreibung:** ein Satz, damit Claude sofort weiß, worum es geht.
- **Commands:** `npm run dev`, `npm test`, `npm run lint` und dazu, wie man eine einzelne Testdatei oder einen einzelnen Test ausführt. Diese Befehle braucht Claude ständig. Den Hinweis, dass CI Lint und Tests prüft, habe ich aufgenommen, damit Claude beides vor dem Abschluss laufen lässt.
- **Conventions:** vier konkrete Regeln, die sich aus dem Code ergeben:
  - CommonJS statt ES-Module
  - Datenzugriff nur über `db/store.js`
  - Fehlerantworten als `{ error: "..." }` mit 400/404
  - Tests mit `node:test` und `supertest`, nicht mit Jest

  Ohne diese Regeln würde Claude leicht `import` oder Jest verwenden oder in den Routen direkt auf die Daten zugreifen.
- **Architecture:** `server.js` exportiert `app` und startet nur, wenn es direkt ausgeführt wird. Pro Ressource gibt es eine Router-Datei. Der Store liegt im Speicher und wird von allen Tests geteilt. Diese Zusammenhänge erschließen sich erst, wenn man mehrere Dateien liest.

**Bewusst weggelassen:**
- **Vollständige Dateiliste und Endpunkt-Details:** Claude findet sie selbst im Code. Außerdem würden sie schnell veralten.
- **Kursanleitung aus dem README und einmalige Aufgaben:** Sie gelten nur für diese Abgabe und nicht für jede Sitzung.
- **Allgemeine Ratschläge** wie „sauberen Code schreiben“: Sie verbrauchen Kontext, ohne Claude etwas Neues zu sagen.
- **Sensible Daten:** Es gibt keine Werte aus `.env` und keine Secrets. Die Datei wird committet und ist für alle sichtbar.

## Berechtigungsregeln (`.claude/settings.json`)

- **Allow – `Bash(npm test:*)`:** Tests sind harmlos und laufen sehr oft. Ohne diese Regel müsste ich jeden Testlauf einzeln bestätigen.
- **Ask – `Bash(git push:*)`:** Ein Push verändert das Remote-Repository und ist für andere sichtbar. Ich will jedes Mal selbst entscheiden, ob und wann gepusht wird.
- **Deny – `Read(./.env)`:** In `.env` stehen echte Secrets wie Datenbank-Zugangsdaten. Ohne diese Regel könnte Claude die Datei lesen, etwa beim Debuggen der Konfiguration. Die Secrets würden dann im Gesprächsverlauf landen und könnten in Logs, Ausgaben oder Commits auftauchen.
- **Deny – `Bash(git push --force:*)`:** Ein Force-Push überschreibt die Historie auf dem Remote und kann Commits anderer unwiderruflich löschen. Die Ask-Regel allein reicht dafür nicht, denn ein schnelles „Ja“ aus Gewohnheit wäre schon zu viel. Deny-Regeln haben Vorrang vor Ask- und Allow-Regeln.

  Einschränkung: Die Regel greift nur, wenn der Befehl genau mit `git push --force` anfängt. Varianten wie `git push -f` fallen nicht darunter. Für sie gilt aber weiterhin die Ask-Regel, ich werde also trotzdem gefragt.

## Überprüfung

- `/memory`: hat `./CLAUDE.md` als geladene Projektanweisung angezeigt.
- `/permissions`: hat die Allow-Regel `Bash(npm test:*)` angezeigt sowie die Deny-Regeln `Read(./.env)` und `Bash(git push --force:*)`. Die optionale Ask-Regel für `git push` wurde in der Ansicht nicht angezeigt.
- Testlauf: `npm ci` wurde erfolgreich ausgeführt, anschließend waren alle vier Tests mit `npm test` erfolgreich.
