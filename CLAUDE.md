# ITALODISCO — Roguelike ASCII

Single-file browser roguelike in `italodisco.html` (3541 righe). Tutto il codice è in un unico file HTML+CSS+JS. Non c'è build system, nessuna dipendenza esterna.

## Come avviare

```bash
# Avvia un server locale
ruby -e "require 'webrick'; s=WEBrick::HTTPServer.new(Port:8080,DocumentRoot:'.'); trap('INT'){s.shutdown}; s.start"
# oppure
python3 -m http.server 8080
```
Poi apri `http://localhost:8080/italodisco.html`

## Struttura del codice

Il file è diviso in sezioni marcate con commenti `// ─────`:

| Sezione | Riga | Descrizione |
|---|---|---|
| COSTANTI | ~74 | TS=16px, COLS=80, ROWS=42, TILE enum, palette colori |
| AUDIO | ~97 | Web Audio API, synth italo disco, jingle 145 BPM |
| DIFFICOLTÀ | ~319 | Facile / Normale / Hardcore, getBossFloor() |
| OGGETTI | ~341 | Items con rarità (Comune→Leggendario), rollRarity(), rndItem() |
| ABILITÀ | ~455 | 5 slot hotbar, canUseAbility(), useAbility(), tickAbilityCDs() |
| SINERGIE | ~749 | Combos oggetto+abilità, oggetto+oggetto, checkSynergies() |
| CRAFTING | ~825 | Ricette fusione, doFusion(), doBackfire(), tavolo Anvil |
| STATO GIOCO | ~1088 | Oggetto `G` (stato globale), `p` (player) |
| STATISTICHE | ~1094 | recalcStats(), tickEffects(), addEffect() |
| SALVATAGGIO | ~1264 | saveGame()/loadGame() su localStorage (F5 per salvare) |
| MERCHANT | ~1384 | generateMerchantStock(), shop per piano |
| GENERAZIONE MAPPA | ~1421 | BSP rooms, corridoi, populate() con nemici/oggetti |
| FOV | ~1584 | Raycasting FOV, hasLOS(), computeFOV() |
| COMBATTIMENTO RANGED | ~1641 | doShoot(), doThrowItem(), renderRangedOverlay() |
| ENTITÀ | ~1779 | entityAt(), itemAt(), walkable() |
| MESSAGGI | ~1791 | msg(text, col) — log box in fondo |
| COMBAT | ~1799 | dmg(), tryDodge(), tryMove(), killEnemy() |
| GAMEPLAY | ~1913 | checkLvlUp(), pickup(), useItem(), dropItem() |
| AI NEMICI | ~2081 | enemyTurn(), chasePlayer() |
| LOOP PRINCIPALE | ~2197 | loop(), render() — requestAnimationFrame |
| RENDERING | ~2202 | renderMap(), renderItems(), renderEntities(), renderPlayer() |
| UI SCREENS | ~2297 | Hotbar, inventory, shop, craft, abilità, help, lvlup, morte, vittoria |
| HUD | ~2983 | Barra superiore con stats |
| INPUT | ~3035 | dirFromKey(), gestione tastiera WASD + comandi |
| LEVEL UP | ~3394 | spendPt() — distribuzione punti attributo |
| RESIZE | ~3410 | Canvas responsive |
| TITLE SCREEN | ~3433 | renderTitle(), titleInput() — schermata iniziale |

## Stato globale

```js
G = {
  map, entities, items, floor, turn,
  visible, explored,  // Set di chiavi "x,y"
  camX, camY,
  merchant, merchantStock,
  mode,  // 'game'|'inv'|'ability'|'shop'|'craft'|'help'|'lvlup'|'ranged'|'title'|'dead'|'victory'
  difficulty,  // 'easy'|'normal'|'hard'
  ...
}

p = {
  x, y, hp, maxHp, mp, maxMp,
  atk, def, spd, critPct, dodgePct,
  str, dex, int, vit, wil,  // attributi base
  pts,  // punti da spendere
  lv, xp, xpNext, gold,
  inv[],       // array oggetti (max 10)
  abilities[], // array slot (max 5 hotbar)
  effects[],   // effetti attivi temporanei
  synergies[], // bonus sinergie attivi
  kills, hunger, poisonTurns, ...
}
```

## Meccaniche principali

- **FOV**: raycasting, i nemici fuori visuale non agiscono
- **Turni**: movimento player → endTurn() → enemyTurn() → tick effetti/CD
- **Combattimento**: dmg(atk, def, critPct), dodge con tryDodge()
- **Rarità oggetti**: Comune / Non Comune / Raro / Epico / Leggendario
- **Sinergie**: bonus passivi quando si hanno certi combo equipaggiati
- **Crafting**: fuse 2 oggetti sull'Anvil (TILE 4) → nuovo oggetto o backfire
- **Abilità**: slot hotbar F1-F5, costo MP, cooldown in turni
- **Boss**: piano fisso da getBossFloor() (default floor 5 in normal)
- **Salvataggio**: localStorage, tasto F5

## Controlli

```
WASD / frecce  — movimento
I              — inventario
F              — schermata abilità
G              — modalità sparo/lancio
>              — scendi le scale
.              — attendi turno
?              — aiuto
F5             — salva
```

## Note di sviluppo

- Tutto in un unico file per semplicità — niente bundler, niente npm
- Il canvas si ridimensiona con resize() ad ogni window.resize
- Audio inizializzato al primo input utente (policy browser autoplay)
- Il renderer usa canvas 2D con font monospace 13px
- I salvataggi usano localStorage — non sopravvivono alla cancellazione dati browser
- Tile size TS=16px, griglia 80×42 tile

## Repo GitHub

`https://github.com/PaoloGianotti/italodisco`
