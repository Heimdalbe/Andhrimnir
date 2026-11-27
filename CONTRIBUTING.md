# Werken aan Andhrímnir: stappenplan voor programmeurs

Dit document legt uit hoe je van een issue op het bord tot gemergde code komt. Lees het één keer volledig; daarna kun je de **spiekbrief** onderaan gebruiken.

Nog nooit met git of pull requests gewerkt? Geen probleem. Elke stap legt uit wat je doet en waarom. Vraag gerust hulp aan Seppe of de ICT.

---

## 0. Eenmalig: je klaarzetten

1. Zorg dat je **Write-rechten** hebt op de repository `Heimdalbe/Andhrimnir` (vraag aan Seppe of de ICT).
2. Installeer **git** en stel je naam en e-mail in (gebruik het e-mailadres van je GitHub-account):
   ```bash
   git config --global user.name "Voornaam Achternaam"
   git config --global user.email "jij@voorbeeld.be"
   ```
3. Clone de repository en ga naar de branch `dev`:
   ```bash
   git clone https://github.com/Heimdalbe/Andhrimnir.git
   cd Andhrimnir
   git switch dev
   ```
4. Volg de README voor de lokale setup (API, database, frontends).
5. Lees de **werkafspraken** in het scopedocument, zeker de regel over AI: AI mag je **alleen** gebruiken om testen te schrijven, niet voor logica, belangrijke code of grafische elementen.

---

## 1. Het bord begrijpen

Het bord **Andhrimnir** in GitHub Projects toont alle werk.

| Kolom | Betekenis |
|---|---|
| Backlog | Nog niet klaar om op te pakken: er is nog een open punt, een andere issue blokeerd deze, of het is nice to have / later |
| Ready | Klaar om op te pakken |
| In progress | Iemand werkt eraan |
| In review | Pull request wacht op review |
| Done | Gemergd in `dev` |

| Begrip | Betekenis |
|---|---|
| **Feature** | Een groot onderdeel, één per FDD (bv. `[CAT-004] Bestellen door de klant`). Je werkt nooit rechtstreeks op een Feature. |
| **Task** | Een stuk werk onder een Feature, zo klein dat het in **één pull request** past. Hier werk je aan. |
| **Bug** | Een fout die tijdens het bouwen of testen gevonden is. |
| **Milestone** | `MVP LAN` = moet klaar zijn voor de LAN. `Nice to have` = als er tijd over is. `Later` = volgende editie. |
| **Priority** | Urgent → High → Medium → Low. Urgent zijn de fundamenten waar andere Tasks op wachten. |
| **Effort** | Low (een paar uur), Medium (één à twee avonden), High (meerdere avonden, complexer). |
| **Labels** | Waar je in de code werkt: `api`, `database`, `frontend-user`, `frontend-keuken`, `frontend-kassa`, `test`. |
| **Blocked by** | Deze Task kan pas starten als die andere Task klaar is. |
| **FDD** | Functional Design Document: de uitgebreide beschrijving van wat er gebouwd moet worden. Elke Task verwijst naar zijn FDD. |

---

## 2. Een Task kiezen

1. Kijk in de kolom **Ready**, milestone **MVP LAN**.
2. Kies bij voorkeur de hoogste **Priority** (Urgent, dan High).
3. Controleer bij **Relationships** dat de Task **niet geblokkeerd** is door een Task die nog niet Done is.
4. Kies iets dat bij je past:
   - **1ste jaar:** begin met **Effort Low** in een frontend (`frontend-user`, `frontend-kassa`, `frontend-keuken`) of een Task met label `test`. Werk gerust samen met een ouderejaars (pair programming).
   - **2de jaar:** Effort Low of Medium, frontend of eenvoudige API-Tasks.
   - **3de jaar:** de Urgent- en High-Tasks in de `api`, zeker die met Effort High (bv. bestelling bevestigen, realtime updates).
5. Werk aan **maximaal één Task tegelijk**.

---

## 3. Melden en toewijzen

Je kiest zelf, maar je meldt het eerst.

1. Plaats een **comment** op de Task:
   > Ik wil deze Task oppakken. @Seppe / @ICT, oké?
2. Wacht op een **akkoord** in het issue (zo werken nooit twee mensen aan hetzelfde).
3. Daarna:
   - zet jezelf bij **Assignees** (Assign yourself);
   - zet de **Status** op **In progress**.

---

## 4. Begrijpen wat er gevraagd wordt

1. Lees de Task volledig: **Wat**, **Acceptatiecriteria** en eventuele **open punten**.
2. Lees het betrokken hoofdstuk in de **FDD** (vermeld onder Bron).
3. De **acceptatiecriteria** zijn je checklist: je bent pas klaar als ze allemaal kloppen.
4. Iets onduidelijk? Stel je vraag als **comment op de Task**, niet in een privébericht. Zo vindt de volgende die het zich afvraagt het antwoord ook.

---

## 5. Een branch maken

Je werkt **nooit** rechtstreeks op `dev`, `ACC` of `Prod`. Elke Task krijgt een eigen branch.

**Conventie voor branchnamen**

```
<type>/<issuenummer>-<korte-omschrijving>
```

| Type | Gebruik |
|---|---|
| `feature/` | Nieuwe functionaliteit (Task) |
| `fix/` | Een Bug oplossen |
| `test/` | Alleen testen toevoegen |
| `docs/` | Alleen documentatie |

Voorbeelden: `feature/27-bestelling-bevestigen`, `fix/71-saldo-negatief`, `test/58-k6-piektest`

Kleine letters, woorden gescheiden door koppeltekens.

**Zo maak je de branch** (altijd vertrekken van de nieuwste `dev`):

```bash
git switch dev
git pull
git switch -c feature/27-bestelling-bevestigen
```

Alternatief: in het issue bij **Development** → **Create a branch**. Kies dan als bron **`dev`** en pas de naam aan volgens de conventie.

---

## 6. Programmeren en committen

1. Blijf binnen de scope van je Task. Zie je iets anders dat fout is? Maak er een **Bug**-issue van in plaats van het mee op te lossen.
2. Maak **kleine commits** met een duidelijke boodschap. Een commit is één logisch stapje (bv. "endpoint toegevoegd", "validatie toegevoegd").

**Conventie voor commitberichten**

```
<type>(<onderdeel>): <wat je deed> (#issuenummer)
```

| Type | Betekenis |
|---|---|
| `feat` | Nieuwe functionaliteit |
| `fix` | Bug opgelost |
| `test` | Testen toegevoegd of aangepast |
| `refactor` | Code herschreven zonder ander gedrag |
| `docs` | Documentatie |
| `chore` | Onderhoud (configuratie, packages, …) |

Onderdeel: `api`, `db`, `user`, `keuken`, `kassa`

Voorbeelden:
```
feat(api): endpoint om een bestelling te bevestigen (#27)
test(api): gelijktijdige bestellingen voor laatste plek in slot (#27)
fix(kassa): opladen met negatief bedrag geweigerd (#71)
```

Gebruik de gebiedende wijs of een korte beschrijving, in het Nederlands.

```bash
git add .
git commit -m "feat(api): endpoint om een bestelling te bevestigen (#27)"
```

3. Schrijf **testen** voor je code. Hier mag je AI gebruiken; voor de code zelf niet.

---

## 7. Zelf controleren

Voor je een pull request maakt:

- [ ] Alle **acceptatiecriteria** uit de Task kloppen (test ze zelf).
- [ ] Je testen slagen lokaal.
- [ ] De applicatie start zonder fouten.
- [ ] Geen achtergebleven `console.log`, testcode of uitgecommentarieerde stukken.
- [ ] Geen wachtwoorden, tokens of sleutels in de code.

**Haal de nieuwste `dev` binnen**, zodat je zeker niet met verouderde code werkt:

```bash
git fetch origin
git merge origin/dev
```

Krijg je een **merge conflict**? Dan hebben jij en iemand anders hetzelfde stuk code aangepast. Open de bestanden, kies de juiste versie (vaak een combinatie), test opnieuw en commit. Lukt het niet, vraag hulp.

---

## 8. Pull request maken

1. Push je branch:
   ```bash
   git push -u origin feature/27-bestelling-bevestigen
   ```
2. Op GitHub verschijnt de knop **Compare & pull request**.
3. Controleer de **base branch**: dit moet **`dev`** zijn, niet `Prod` of `ACC`.
4. **Titel**: dezelfde als de Task, bv. `[CAT-004] API: bestelling bevestigen`.
5. **Beschrijving**: gebruik dit sjabloon:

   ```markdown
   Closes #27

   ## Wat
   - Korte opsomming van wat je gebouwd hebt.

   ## Hoe getest
   - Welke testen je schreef en wat je manueel controleerde.

   ## Screenshots
   (alleen bij frontend-wijzigingen)

   ## Opmerkingen voor de reviewer
   - Twijfels, keuzes, dingen waar je feedback op wil.
   ```

   `Closes #27` koppelt de pull request aan de Task.
6. Eén pull request = **één Task**. Combineer nooit meerdere Tasks.
7. Zet de Task op **In review**.

Tip: wil je al vroeg feedback? Maak een **Draft pull request**. Die kan nog niet gemergd worden, maar anderen kunnen al meekijken.

---

## 9. Review

1. **GitHub Copilot** doet eventueel een eerste automatische review. Bekijk de opmerkingen; ze zijn suggesties, geen verplichtingen.
2. De **ICT of Seppe** reviewt en keurt goed (**Approve**) of vraagt aanpassingen (**Request changes**).
3. Commentaar geven op pull requests is **sterk aangeraden**, ook op die van anderen. Kijk dus ook regelmatig naar open pull requests van de rest.
4. Kreeg je opmerkingen?
   - Pas je code aan op dezelfde branch, commit en push. De pull request wordt automatisch bijgewerkt.
   - Antwoord op elke opmerking (bv. "Aangepast in de laatste commit" of uitleg waarom niet) en klik op **Resolve conversation**.
5. Review is geen kritiek op jou, maar op de code. Iedereen krijgt opmerkingen, ook de ervaren mensen.

---

## 10. Merge en afronden

1. Na goedkeuring merget de reviewer de pull request in `dev` (bij voorkeur **Squash and merge**: alle commits worden één nette commit).
2. De Task gaat naar **Done**. Gebeurt dat niet automatisch, zet de status dan zelf op Done en sluit het issue.
3. Verwijder je branch (knop **Delete branch** in de pull request).
4. Zet je lokale `dev` weer up-to-date:
   ```bash
   git switch dev
   git pull
   ```
5. Pak de volgende Task op (terug naar stap 2).

---

## 11. Van Dev naar ACC naar Prod

Dat doe jij niet; de **ICT of Seppe** zet de code door:

| Branch | Doel |
|---|---|
| `dev` | Alle gemergde Tasks; hier wordt continu aan gewerkt |
| `ACC` | Acceptatie: stabiele versie om te testen (bv. belastingstesten, generale repetitie) |
| `Prod` | De versie die op de LAN draait |

---

## 12. Een bug melden

Vind je een fout (in je eigen werk na de merge of in dat van iemand anders)?

1. Maak een **nieuw issue** van type **Bug**.
2. Titel: `[BUG ON CAT-xxx] Korte omschrijving van de fout`.
3. Beschrijving:
   ```markdown
   ## Stappen om te reproduceren
   1. …
   2. …

   ## Verwacht resultaat
   …

   ## Werkelijk resultaat
   …

   ## Gerelateerd
   - Task #27
   ```
4. Koppel de Bug aan de betrokken Feature als parent.

---

## 13. Vastgelopen?

- Zit je langer dan een uur vast op hetzelfde probleem? Vraag hulp: comment op de Task, of spreek iemand aan.
- Blijkt je Task te groot? Meld het in een comment; dan kijken we of we dit kunnen opsplitsen.
- Kun je niet verder omdat een andere Task nog niet klaar is? Leg een **Blocked by**-relatie en zet je Task terug op **Ready**, zodat iemand anders kan helpen.
- Heb je geen tijd meer? Geen probleem, maar meld het. Haal jezelf weg bij Assignees en zet de Task terug op **Ready**.

---

## Spiekbrief

```bash
# Nieuwe Task starten
git switch dev
git pull
git switch -c feature/<nr>-<omschrijving>

# Werken
git add .
git commit -m "feat(api): wat je deed (#<nr>)"

# Klaar? Eerst dev binnenhalen
git fetch origin
git merge origin/dev

# Pushen en pull request maken (base: dev)
git push -u origin feature/<nr>-<omschrijving>

# Na de merge
git switch dev
git pull
```

| Stap | Status op het bord |
|---|---|
| Task gekozen en akkoord gekregen | In progress |
| Pull request gemaakt | In review |
| Pull request gemerged in `dev` | Done |
