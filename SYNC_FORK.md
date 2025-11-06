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
