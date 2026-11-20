# Projekte für Tasks

Stand: 20.11.2026. Fork: https://github.com/MarcBollmann/open-sunsama, Branch `feat/projects`.

## Ausgangslage

Open Sunsama plant Tasks pro Tag und kennt einen Backlog für Tasks ohne Datum. Es gab keine Möglichkeit, Tasks nach Projekten zu trennen, etwa Arbeit gegenüber mehreren privaten Projekten.

Wiederkehrende Tasks gab es bereits (Rechtsklick auf einen Task > "Repeat...", Verwaltung unter Einstellungen > Routines). Diese Funktion bleibt unverändert und bekommt nur die Projekt-Zuordnung.

## Entscheidungen des Nutzers

| Frage | Entscheidung |
| --- | --- |
| Was ist ein Projekt? | Ein Feld am Task. Ungeplante Tasks eines Projekts bilden dessen Backlog, geplante erscheinen mit Projekt-Label im Tagesplan. |
| Struktur | Flache Projektliste. "Arbeit" ist einfach ein Projekt, keine Bereiche darüber. |
| Projekte pro Task | Genau eins oder keins. |
| Wiederkehrende Tasks | Die vorhandene Funktion reicht. Serien bekommen zusätzlich ein Projekt. |
| Umfang | Web, API und MCP-Tools. Keine Mobile-App. |

## Anforderungen

### Projekte verwalten

- P1: Der Nutzer kann beliebig viele Projekte anlegen, jeweils mit Name (max. 100 Zeichen) und Farbe.
- P2: Projekte lassen sich umbenennen, umfärben und in der Reihenfolge verschieben.
- P3: Ein Projekt lässt sich archivieren und wiederherstellen. Archivierte Projekte erscheinen nicht mehr in der Auswahl, ihre Tasks behalten das Projekt.
- P4: Ein Projekt lässt sich löschen. Seine Tasks und Serien bleiben erhalten und stehen danach ohne Projekt da.
- P5: Die Projektliste zeigt pro Projekt die Zahl der offenen Tasks und wie viele davon im Backlog liegen.

### Tasks zuordnen

- T1: Jeder Task hat genau ein Projekt oder keins.
- T2: Das Projekt ist beim Anlegen wählbar (Schnelleingabe und Task-Dialog) und später änderbar (Task-Dialog, Label auf der Karte, Kontextmenü).
- T3: Ein neues Projekt lässt sich direkt in der Projektauswahl anlegen.
- T4: Task-Karten zeigen das Projekt als farbigen Punkt mit Namen.
- T5: Ein duplizierter Task behält das Projekt.
- T6: Einem Task kann nur ein eigenes Projekt zugewiesen werden.

### Filtern und Projekt-Backlog

- F1: Board und Backlog lassen sich gemeinsam auf "Alle Projekte", "Ohne Projekt" oder ein einzelnes Projekt filtern. Die Wahl bleibt auf dem Gerät gespeichert.
- F2: Mit gewähltem Projekt zeigt der Backlog nur dessen ungeplante Tasks.
- F3: Ein neuer Task bekommt als Vorgabe das gerade gefilterte Projekt.

### Wiederkehrende Tasks

- R1: Eine Serie hat ein Projekt oder keins. Wird sie aus einem Task erstellt, übernimmt sie dessen Projekt.
- R2: Jeder von der Serie erzeugte Task bekommt das Projekt der Serie.
- R3: Das Projekt einer Serie ist unter Einstellungen > Routines änderbar. Die Änderung gilt auch für alle noch offenen Tasks der Serie.

### API und MCP

- A1: `GET/POST /projects`, `PATCH/DELETE /projects/:id`, `POST /projects/reorder`.
- A2: `projectId` beim Anlegen und Ändern von Tasks und Serien; `GET /tasks?projectId=<id>|none` als Filter.
- A3: MCP-Tools `list_projects`, `create_project`, `update_project`, `delete_project`; `list_tasks`, `create_task` und `update_task` kennen `projectId`.

## Nicht im Umfang

- Bereiche oder verschachtelte Projekte.
- Mehrere Projekte pro Task.
- Neue Wiederholungsmuster oder eine neue Routinen-Verwaltung.
- Expo- und React-Native-App.
- Projektfilter in der Kalender- und Tasks-Listenansicht sowie in der Suche.
- Projekte an Ideen-Karten.

## Datenmodell

- Neue Tabelle `projects` (`id`, `user_id`, `name`, `color`, `position`, `archived_at`, Zeitstempel).
- `tasks.project_id` und `task_series.project_id`, jeweils `ON DELETE SET NULL`.
- Migration `packages/database/drizzle/0006_projects.sql`. Sie ändert keine vorhandenen Daten; bestehende Tasks stehen danach ohne Projekt da.
