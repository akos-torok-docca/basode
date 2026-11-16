---
name: stakeholder-design
description: "Basode: a projekt stakeholder-paneljének felállítása nulláról — stakeholder-lista, persona-űrlapok, dimenziók, identitás promptok, eval-bekötés. Kérdőív-vezérelt; nem futtat panelt."
---

# Stakeholder-panel felállítása (basode, 1. héj)

Ez a skill egy projekt stakeholder-panelját **kialakítja**, nem futtatja. A futtatás (előzetes és utólagos
−5…+5 eval) a projekt saját delivery systemjének dolga.

**Módszer** (miért, űrlap, dimenziók, −5…+5 rubrika, kalibráció, eval-metrikák, futtatási elv): a basode
stakeholder-eval módszere. Amíg az ősforrásba ([`basode_en.md`](https://github.com/akos-torok-docca/basode/blob/main/basode_en.md))
nem került, a referencia-implementáció módszer-doksija a Docuversum delivery repóban él
(`docs_of_delivery_architect/doclayer1_of_delivery_architect/stakeholder_eval_method.md`).

**Persona-sablon:** `${CLAUDE_PLUGIN_ROOT}/templates/stakeholder_template.md`.

## Kérdőív (a humán döntéshozóval, egy körben)

1. **Hol él a panel?** A delivery system melyik dokumentum-rétegében (módszer: DL1; a projekt saját
   listája és szabályai: DL3) és melyik mappában.
2. **Milyen forrásokból dolgozunk?** Vízió, mesék / personák, üzleti modell, szerepkör-modell, jogi és
   adatkezelési doksik — a fájlok listája.
3. **Lefedettség.** Mind a négy körre (platform; ügyfél; ökoszisztéma; nem-user érintett) a módszer
   lefedettségi kérdései alapján javasolsz stakeholdereket; a humán döntéshozó kiegészít, összevon, töröl.
4. **Mikor fusson** az előzetes és az utólagos mérés a projekt workflow-jában (melyik lépés előtt / után)?
5. **Kapu-erő:** tanácsadó (vétó → humán döntés) vagy blokkoló (vétó → a cső megáll)?

## Végrehajtás

1. Personánként a sablon kitöltése a 2. pont forrásaiból; ahol nincs forrás, a válasz elé „feltételezés".
2. Szerepkör-specifikus dimenziók: personánként kettő, a B1-ből és a B3-ból.
3. README a persona-mappában: personák táblája „Érintett, ha…" oszloppal (az E4 megfordítása) és a
   csoportok táblája (a címzéshez).
4. Eval-napló és futtató: append-only napló (soronként egy persona-ítélet) és egy script, amely a közös
   kontextust egyszer építi fel, és personánként független modellhívással, cache-elt közös előtaggal pontoztat.
   Referencia-implementáció: a Docuversum `hack/dev/stakeholder_eval.py`.
5. Bekötés a 4. pont szerinti workflow-lépésekbe; a projekt agent-módosítási kapuja szerint jóváhagyással.
