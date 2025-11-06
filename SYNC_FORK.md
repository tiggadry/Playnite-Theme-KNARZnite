# Průvodce synchronizací forku s původním repozitářem

Tento dokument vysvětluje, jak synchronizovat váš fork repozitáře Playnite-Theme-KNARZnite se změnami z původního (upstream) repozitáře.

## Obsah

1. [Nastavení upstream repozitáře](#nastavení-upstream-repozitáře)
2. [Synchronizace všech změn](#synchronizace-všech-změn)
3. [Řešení konfliktů](#řešení-konfliktů)
4. [Výběr konkrétních změn (cherry-picking)](#výběr-konkrétních-změn-cherry-picking)
5. [Praktické příklady](#praktické-příklady)

## Nastavení upstream repozitáře

Pokud ještě nemáte nastavený upstream repozitář, musíte ho přidat:

```bash
# Zkontrolujte aktuální remote repozitáře
git remote -v

# Přidejte upstream repozitář (původní repozitář HerrKnarz)
git remote add upstream https://github.com/HerrKnarz/Playnite-Theme-KNARZnite.git

# Ověřte, že byl upstream přidán
git remote -v
```

Nyní byste měli vidět:
- `origin` - váš fork (<vaše-uživatelské-jméno>/Playnite-Theme-KNARZnite)
- `upstream` - původní repozitář (HerrKnarz/Playnite-Theme-KNARZnite)

## Synchronizace všech změn

### Krok 1: Stáhněte změny z upstream

```bash
# Stáhněte všechny změny z upstream repozitáře
git fetch upstream

# Zobrazte, jaké změny jsou k dispozici
git log HEAD..upstream/main --oneline
```

### Krok 2: Sloučte změny

```bash
# Ujistěte se, že jste na vaší hlavní větvi
git checkout main

# Sloučte změny z upstream/main do vaší větve
git merge upstream/main

# Nebo použijte rebase pro lineárnější historii
git rebase upstream/main
```

### Krok 3: Nahrajte změny do vašeho forku

```bash
# Nahrajte sloučené změny
git push origin main

# Pokud jste použili rebase, možná budete muset použít force push
# POZOR: Použijte pouze pokud si jste jisti, že chcete přepsat historii
git push --force-with-lease origin main
```

## Řešení konfliktů

Když Git nemůže automaticky sloučit změny, objeví se konflikty. Zde je postup pro jejich řešení:

### 1. Identifikujte konfliktní soubory

```bash
# Git vám ukáže seznam konfliktních souborů
git status
```

### 2. Otevřete a upravte konfliktní soubory

Konfliktní soubor bude obsahovat značky:

```
<<<<<<< HEAD
Váš kód (z vaší verze)
=======
Kód z upstream (původní repozitář)
>>>>>>> upstream/main
```

**Možnosti řešení:**

a) **Ponechat vaše změny:**
   - Smažte značky `<<<<<<<`, `=======`, `>>>>>>>` a kód z upstream
   - Ponechejte pouze váš kód

b) **Přijmout změny z upstream:**
   - Smažte značky a váš kód
   - Ponechejte pouze kód z upstream

c) **Kombinovat obě změny:**
   - Manuálně upravte kód tak, aby obsahoval to nejlepší z obou verzí
   - Odstraňte všechny značky konfliktů

### 3. Označte konflikty jako vyřešené

```bash
# Po úpravě souborů je přidejte
git add <soubor_s_konfliktem>

# Nebo přidejte všechny vyřešené soubory
git add .
```

### 4. Dokončete merge nebo rebase

```bash
# Pokud jste použili merge
git commit -m "Sloučení změn z upstream a vyřešení konfliktů"

# Pokud jste použili rebase
git rebase --continue

# V případě problémů můžete merge/rebase zrušit
git merge --abort  # pro merge
git rebase --abort # pro rebase
```

### 5. Nahrajte změny

```bash
git push origin main
```

## Výběr konkrétních změn (cherry-picking)

Pokud nechcete všechny změny z upstream, ale pouze některé konkrétní commity:

### 1. Najděte commit, který chcete přenést

```bash
# Zobrazte seznam commitů z upstream
git log upstream/main --oneline

# Nebo zobrazte rozdíly mezi vaší větví a upstream
git log HEAD..upstream/main --oneline --graph
```

### 2. Cherry-pick konkrétní commit

```bash
# Přeneste konkrétní commit (použijte jeho SHA)
git cherry-pick <commit-sha>

# Příklad:
git cherry-pick abc1234
```

### 3. Cherry-pick více commitů najednou

```bash
# Přeneste rozsah commitů
git cherry-pick <commit-sha-start>..<commit-sha-end>

# Přeneste více jednotlivých commitů
git cherry-pick abc1234 def5678 ghi9012
```

### 4. Řešení konfliktů při cherry-picking

Pokud při cherry-pick narazíte na konflikt:

```bash
# Vyřešte konflikty v souborech (stejně jako u merge)
# Pak přidejte vyřešené soubory
git add .

# Pokračujte v cherry-pick
git cherry-pick --continue

# Nebo cherry-pick zrušte
git cherry-pick --abort
```

### 5. Nahrajte změny

```bash
git push origin main
```

## Praktické příklady

### Příklad 1: Úplná synchronizace s upstream

```bash
# 1. Ujistěte se, že máte čistý working directory
git status

# 2. Stáhněte změny
git fetch upstream

# 3. Přepněte se na main větev
git checkout main

# 4. Sloučte změny
git merge upstream/main

# 5. Vyřešte případné konflikty (viz výše)

# 6. Nahrajte do vašeho forku
git push origin main
```

### Příklad 2: Selektivní přenos změn

```bash
# 1. Stáhněte změny
git fetch upstream

# 2. Prohlédněte si dostupné změny
git log upstream/main --oneline -10

# 3. Najděte commit, který vás zajímá (např. "abc1234 - Fixed button layout")
# 4. Cherry-pick tento commit
git cherry-pick abc1234

# 5. Nahrajte změnu
git push origin main
```

### Příklad 3: Zrušení sloučení, které nechcete

Pokud jste sloučili změny, ale zjistili jste, že je nechcete:

```bash
# Pokud jste ještě nenahráli změny na GitHub
git reset --hard HEAD~1

# Pokud jste již nahráli změny (POZOR: přepíše historii)
git reset --hard HEAD~1
git push --force-with-lease origin main
```

### Příklad 4: Porovnání změn před sloučením

```bash
# Zobrazte soubory, které byly změněny v upstream
git diff --name-status HEAD upstream/main

# Zobrazte podrobné rozdíly
git diff HEAD upstream/main

# Zobrazte rozdíly pro konkrétní soubor
git diff HEAD upstream/main -- <cesta/k/souboru>
```

## Doporučené workflow

1. **Před začátkem**: Vždy si zálohujte vaši práci (vytvořte branch nebo tag)
   ```bash
   git tag backup-before-sync
   # nebo
   git checkout -b backup-branch
   git checkout main
   ```

2. **Pravidelně kontrolujte upstream**: Alespoň jednou za měsíc zkontrolujte, zda nejsou nové změny
   ```bash
   git fetch upstream
   git log HEAD..upstream/main --oneline
   ```

3. **Testujte po sloučení**: Po sloučení změn vždy otestujte, že téma funguje správně v Playnite

4. **Dokumentujte změny**: Pokud jste udělali vlastní úpravy, zaznamenejte si je, abyste je mohli snadno znovu aplikovat po aktualizaci

## Užitečné příkazy

```bash
# Zobrazit grafickou historii
git log --oneline --graph --all

# Zobrazit, kdo změnil konkrétní soubor
git log --follow -- <soubor>

# Zobrazit obsah souboru z jiné větve
git show upstream/main:<soubor>

# Porovnat konkrétní soubor mezi větvemi
git diff main upstream/main -- <soubor>

# Zobrazit seznam souborů změněných v commitu
git show --name-only <commit-sha>
```

## Získání pomoci

Pokud narazíte na problémy:

1. Zkontrolujte status: `git status`
2. Podívejte se na log: `git log --oneline -10`
3. V případě vážných problémů můžete operaci zrušit pomocí `git merge --abort` nebo `git rebase --abort`
4. Můžete se vrátit k předchozímu stavu pomocí `git reset --hard <commit-sha>`

## Varování

- **Force push** (`git push --force`) přepíše historii. Používejte pouze pokud si jste jisti.
- Raději používejte `--force-with-lease`, což je bezpečnější varianta.
- Před větším sloučením si vždy vytvořte zálohu (branch nebo tag).
- Po rebase je často nutný force push - to přepíše historii ve vašem forku.

---

**Poznámka**: Tento průvodce předpokládá, že pracujete s větví `main`. Pokud používáte jinou větev, upravte příkazy odpovídajícím způsobem.

## Seznam všech příkazů a jejich atributů

Tato sekce obsahuje kompletní přehled všech Git příkazů použitých v tomto dokumentu, včetně jejich atributů a popisů.

### git remote

**Popis**: Správa vzdálených repozitářů (remote repositories).

| Atribut/Parametr | Popis |
|------------------|-------|
| `-v` | Zobrazí verbose (podrobný) výstup - ukáže URL adresu pro každý remote repozitář |
| `add <název> <url>` | Přidá nový remote repozitář s daným názvem a URL |

**Příklady použití**:
- `git remote -v` - Zobrazí seznam všech remote repozitářů s jejich URL
- `git remote add upstream <url>` - Přidá upstream repozitář

---

### git fetch

**Popis**: Stáhne změny ze vzdáleného repozitáře bez automatického sloučení s aktuální větví.

| Atribut/Parametr | Popis |
|------------------|-------|
| `<remote>` | Název remote repozitáře, ze kterého se mají stáhnout změny |

**Příklady použití**:
- `git fetch upstream` - Stáhne všechny změny z upstream repozitáře

---

### git log

**Popis**: Zobrazí historii commitů.

| Atribut/Parametr | Popis |
|------------------|-------|
| `--oneline` | Zobrazí každý commit na jednom řádku (zkrácený formát) |
| `--graph` | Zobrazí grafické znázornění větvení a sloučení |
| `--all` | Zobrazí všechny větve, nejen aktuální |
| `--follow` | Sleduje historii souboru i přes přejmenování |
| `-<n>` | Omezí výstup na posledních N commitů (např. `-10` pro 10 commitů) |
| `HEAD..<branch>` | Zobrazí commity, které jsou v <branch>, ale ne v HEAD |
| `-- <soubor>` | Filtruje pouze commity týkající se konkrétního souboru |

**Příklady použití**:
- `git log HEAD..upstream/main --oneline` - Zobrazí commity z upstream, které ještě nejsou v aktuální větvi
- `git log --oneline --graph --all` - Zobrazí grafickou historii všech větví
- `git log --follow -- <soubor>` - Zobrazí historii konkrétního souboru

---

### git checkout

**Popis**: Přepíná mezi větvemi nebo obnovuje soubory z určitého stavu.

| Atribut/Parametr | Popis |
|------------------|-------|
| `<branch>` | Název větve, na kterou se chcete přepnout |
| `-b <branch>` | Vytvoří novou větev a přepne se na ni |

**Příklady použití**:
- `git checkout main` - Přepne se na větev main
- `git checkout -b backup-branch` - Vytvoří a přepne se na novou větev backup-branch

---

### git merge

**Popis**: Sloučí změny z jiné větve do aktuální větve.

| Atribut/Parametr | Popis |
|------------------|-------|
| `<branch>` | Název větve, kterou chcete sloučit do aktuální větve |
| `--abort` | Zruší probíhající merge a vrátí se do stavu před merge |

**Příklady použití**:
- `git merge upstream/main` - Sloučí změny z upstream/main do aktuální větve
- `git merge --abort` - Zruší probíhající merge

---

### git rebase

**Popis**: Přenese commity na novou základní větev, vytváří lineárnější historii.

| Atribut/Parametr | Popis |
|------------------|-------|
| `<branch>` | Název větve, na kterou se mají commity přenést |
| `--continue` | Pokračuje v rebase po vyřešení konfliktů |
| `--abort` | Zruší probíhající rebase a vrátí se do původního stavu |

**Příklady použití**:
- `git rebase upstream/main` - Přenese vaše commity na vrchol upstream/main
- `git rebase --continue` - Pokračuje v rebase po vyřešení konfliktů
- `git rebase --abort` - Zruší probíhající rebase

---

### git push

**Popis**: Nahraje lokální commity do vzdáleného repozitáře.

| Atribut/Parametr | Popis |
|------------------|-------|
| `<remote>` | Název remote repozitáře (obvykle origin) |
| `<branch>` | Název větve, kterou chcete nahrát |
| `--force` | Vynuceně přepíše vzdálenou větev (nebezpečné!) |
| `--force-with-lease` | Bezpečnější varianta force push - kontroluje, že nepřepíšete cizí změny |

**Příklady použití**:
- `git push origin main` - Nahraje větev main do origin
- `git push --force-with-lease origin main` - Bezpečně vynuceně nahraje main

---

### git status

**Popis**: Zobrazí stav pracovního adresáře a staging oblasti.

**Příklady použití**:
- `git status` - Zobrazí změněné soubory, soubory připravené k commitu a aktuální větev

---

### git add

**Popis**: Přidá soubory do staging oblasti (připraví je k commitu).

| Atribut/Parametr | Popis |
|------------------|-------|
| `<soubor>` | Cesta k souboru, který chcete přidat |
| `.` | Přidá všechny změněné soubory v aktuálním adresáři a podadresářích |

**Příklady použití**:
- `git add <soubor>` - Přidá konkrétní soubor
- `git add .` - Přidá všechny změněné soubory

---

### git commit

**Popis**: Vytvoří nový commit se změnami ve staging oblasti.

| Atribut/Parametr | Popis |
|------------------|-------|
| `-m "<zpráva>"` | Specifikuje commit zprávu přímo z příkazové řádky |

**Příklady použití**:
- `git commit -m "Sloučení změn z upstream a vyřešení konfliktů"` - Vytvoří commit se zadanou zprávou

---

### git cherry-pick

**Popis**: Aplikuje změny z konkrétních commitů na aktuální větev.

| Atribut/Parametr | Popis |
|------------------|-------|
| `<commit-sha>` | SHA hash commitu, který chcete aplikovat |
| `<sha1>..<sha2>` | Rozsah commitů k aplikaci |
| `<sha1> <sha2> <sha3>` | Více jednotlivých commitů k aplikaci |
| `--continue` | Pokračuje v cherry-pick po vyřešení konfliktů |
| `--abort` | Zruší probíhající cherry-pick |

**Příklady použití**:
- `git cherry-pick abc1234` - Aplikuje commit abc1234 na aktuální větev
- `git cherry-pick abc1234 def5678` - Aplikuje více commitů
- `git cherry-pick --continue` - Pokračuje po vyřešení konfliktů

---

### git diff

**Popis**: Zobrazí rozdíly mezi commity, větvemi nebo soubory.

| Atribut/Parametr | Popis |
|------------------|-------|
| `<branch1> <branch2>` | Porovná dvě větve |
| `HEAD <branch>` | Porovná aktuální stav s jinou větví |
| `--name-status` | Zobrazí pouze názvy souborů a status změn (A=added, M=modified, D=deleted) |
| `-- <soubor>` | Omezí diff pouze na konkrétní soubor |

**Příklady použití**:
- `git diff HEAD upstream/main` - Zobrazí rozdíly mezi aktuální větví a upstream/main
- `git diff --name-status HEAD upstream/main` - Zobrazí seznam změněných souborů
- `git diff HEAD upstream/main -- <soubor>` - Zobrazí rozdíly pro konkrétní soubor

---

### git reset

**Popis**: Resetuje aktuální větev na specifický stav.

| Atribut/Parametr | Popis |
|------------------|-------|
| `--hard` | Kompletně resetuje pracovní adresář i staging area (zahodí všechny změny) |
| `<commit-sha>` | Commit, na který se má resetovat |
| `HEAD~<n>` | Resetuje o N commitů zpět (např. HEAD~1 = jeden commit zpět) |

**Příklady použití**:
- `git reset --hard HEAD~1` - Zruší poslední commit a zahodí změny
- `git reset --hard <commit-sha>` - Resetuje na konkrétní commit

**VAROVÁNÍ**: `--hard` trvale smaže změny!

---

### git tag

**Popis**: Vytváří, zobrazuje nebo maže tagy (značky pro důležité body v historii).

| Atribut/Parametr | Popis |
|------------------|-------|
| `<název>` | Vytvoří nový tag s daným názvem na aktuálním commitu |

**Příklady použití**:
- `git tag backup-before-sync` - Vytvoří tag pro zálohu před synchronizací

---

### git show

**Popis**: Zobrazí detaily o commitu, tagu nebo souboru.

| Atribut/Parametr | Popis |
|------------------|-------|
| `<commit-sha>` | SHA hash commitu, který chcete zobrazit |
| `<branch>:<soubor>` | Zobrazí konkrétní soubor z určité větve |
| `--name-only` | Zobrazí pouze názvy souborů bez rozdílů |

**Příklady použití**:
- `git show upstream/main:<soubor>` - Zobrazí obsah souboru z upstream/main
- `git show --name-only <commit-sha>` - Zobrazí seznam souborů změněných v commitu

---

## Shrnutí podle kategorie

### Správa remote repozitářů
- `git remote -v` - Zobrazení remote repozitářů
- `git remote add` - Přidání remote repozitáře

### Stahování změn
- `git fetch` - Stažení změn bez sloučení

### Prohlížení historie a změn
- `git log` - Historie commitů
- `git diff` - Porovnání změn
- `git show` - Detaily commitu nebo souboru
- `git status` - Aktuální stav

### Přepínání a správa větví
- `git checkout` - Přepnutí větve

### Sloučení změn
- `git merge` - Sloučení větví
- `git rebase` - Rebase větve
- `git cherry-pick` - Výběr konkrétních commitů

### Ukládání změn
- `git add` - Přidání do staging area
- `git commit` - Vytvoření commitu
- `git push` - Nahrání do remote repozitáře

### Zrušení změn
- `git merge --abort` - Zrušení merge
- `git rebase --abort` - Zrušení rebase
- `git cherry-pick --abort` - Zrušení cherry-pick
- `git reset --hard` - Reset na předchozí stav

### Záloha
- `git tag` - Vytvoření tagu pro zálohu
