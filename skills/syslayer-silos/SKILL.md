---
name: syslayer-silos
description: "Basode: a projekt hagymahéjainak (syslayer) szétválasztása oprendszer-user + fájlrendszer + git repó silókra — kérdőív a héjakról és szerepekről, read-only bemenet (a héj DL1-e), írható kimenet (DL2/DL3 = a következő héj DL1-e). Tervet és telepítő scriptet ad; jóváhagyás nélkül nem hajt végre."
---

# Syslayer-silók felállítása (basode, hagymahéj-izoláció)

Ez a skill egy projekt hagymahéjait (syslayer) **szétválasztja**: minden héj saját oprendszer-usert,
saját fájlrendszer-területet és saját git repót kap, és csak azt írhatja, ami az övé. A cél kernel-szintű
határ: egy eltérített agent (prompt injection, rossz szabály) ne tudja átírni a saját korlátait, a
deploy-kaput vagy egy külsőbb héj döntéseit. Hook, commit-ellenőrzés vagy harness-tiltólista nem határ,
mert ugyanaz a user írja, aki ellen véd.

**Módszer:** a basode hagymahéj-modellje és az „izoláció agent eltérítési veszélyek miatt” szakasz
([`basode_hu.md`](https://github.com/akos-torok-docca/basode/blob/main/basode_hu.md), 4. fejezet és
„#harness_engineering”). A skill nem ismétli, hanem alkalmazza.

**Alapelvek**

1. **Egy héj = egy siló:** oprendszer-user (vagy csoport), fájlrendszer-terület, git repó (vagy repó-rész).
2. **Bemenet read-only, kimenet írható.** A héj bemenete a felé jövő igényeket tartalmazó réteg (a héj
   DL1-e) — ezt csak olvassa. A kimenete (DL2, DL3) a következő, belsőbb héj DL1-e — ezt írja.
   A bemenet a belső héj fájlrendszerében read-only symlinkként vagy read-only mountként jelenik meg.
3. **Az emberi admin-fiók nem héj.** A meglévő emberi fiók jogai nem változnak (a jogfosztása a
   meglévő munkát törheti), de alapból egyik héj sem fut vele; a héjak saját technikai userrel futnak,
   névből is felismerhetően (pl. `<projekt>_dev`, `<projekt>_ops`).
4. **Kifelé erősebb jog, befelé szűkebb.** Külsőbb héj (pl. ops) birtokolja a belsőbb héj szabályait és
   kapuit (agent-szabályfájlok, CI-workflow, deploy-scriptek); a belsőbb héj ezeket csak olvassa.
5. **Credential héjanként.** Git-token, felhő-jog, titok csak annak a héjnak, amelyiknek kell, a
   legszűkebb scope-pal (pl. a fejlesztő héj tokenje: push a saját ágra, workflow- és ágvédelem-jog nélkül).
6. **YAGNI.** Csak annyi siló, amennyi héj ténylegesen külön döntési és kockázati határ; a többi héj
   egy silón osztozik, amíg nem kell szétvágni.

## Kérdőív (a humán döntéshozóval, egy körben)

1. **Héjak.** Melyek a projekt syslayerei (pl. founder, DA, PO, infra/ops, dev, product)? Melyik héj
   csak dokumentum (nincs futó agentje), és melyikben fut agent?
2. **Szerepek héjanként.** Ki dolgozik az adott héjban: ember, melyik agent (harness, modell), és milyen
   munkamódban (tervezés, fejlesztés, üzemeltetés)?
3. **Bemenet és kimenet héjanként.** Melyik mappa a héj DL1-e (bemenet), és melyik a DL2/DL3-a (kimenet)?
   Melyik héj DL1-ét írja?
4. **Kritikus fájlok.** Melyek az agent-szabályok, hookok, CI-workflow-k, deploy-scriptek, és melyik
   héj birtokolja őket?
5. **Credentialök.** Milyen tokenek, kulcsok, felhő-jogok vannak ma, kié, milyen scope-pal? Melyik héjnak
   melyik kell?
6. **Mai állapot.** Ma hány oprendszer-user fut, ki van root-szintű csoportban (sudo, docker, lxd)?
   Hány git repó van, közös munkakönyvtárban élnek-e?
7. **Futtatókörnyezet.** Kell-e a fejlesztő héjnak docker (rootless Docker kiváltja-e a root-szintű
   `docker` csoportot)? Konténer, VM vagy közös gép?
8. **Repó-topológia.** Héjanként külön git repó, vagy egy repó több mappával és CODEOWNERS-szel?
   Kell-e saját távoli repó (pl. GitHub) a héjnak?

## Végrehajtás

1. **Silótábla** a kérdőív alapján: héj | oprendszer-user / csoport | írható mappák | read-only bemenet |
   git repó | credentialök | ki indítja. Mellé a „mai → ezután” tábla, és amit a szétválasztás eltör
   (közös munkakönyvtár, docker, hookok útvonalai).
2. **Telepítő script** (idempotens, dry-run alapértelmezéssel): userek és csoportok, tulajdonos és
   jogosultság a mappákon, read-only symlink / bind mount a bemenetekre, héjanként git-konfig és
   credential-helper, sudo / docker / lxd tagság nélkül. Rollback-szakasszal.
3. **Ellenőrző script:** minden héj userével próbálja írni a más héjhoz tartozó kritikus fájlokat, és
   bukjon, ha sikerül (negatív kontroll); a CI-ben vagy időzítve fut.
4. **Bekötés:** a harness indítása héjanként a saját userrel (pl. `sudo -u <projekt>_dev claude`);
   az agent-szabályok a héj határát rögzítik, de a határ a fájljog, nem a szabály.
5. Végrehajtás csak a humán döntéshozó jóváhagyása után, a projekt agent-módosítási kapuja szerint;
   root-jog kell hozzá, ezért a lépéseket a humán futtatja, copy-paste parancsként.

**Referencia-implementáció:** a Docuversum `agent_user_split_2141` jegye és a
`canvas/plans/26-11-14 20-57 agent_least_privilege.md` plan (dev VM, `dvagents` csoport, rootless Docker,
szűkített PAT).
