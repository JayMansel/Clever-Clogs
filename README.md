# Clever Clogs — family game night

**Version 3.0** — the quiz, the word race and the donkey derby.

Three games for the family, each person on their own phone. One person starts a
room and shares a four-letter code (or an invite link); everyone else joins, and
the scores update on every phone. The host picks which game the room plays —
**Quiz night**, the **Word race** or the **Donkey derby** — and can switch
between them between games without anyone re-joining.

One page, one link, one room code. It works on any phone, tablet or laptop with
a browser.

## Quiz night

- 686 questions across 11 everyday categories (General Knowledge, Food & Drink, Film & TV,
  Music, Geography, History, Science, Animals & Nature, Sport, **Wales**, Words & Phrases)
- **Music through the decades**: ’60s, ’70s, ’80s, ’90s, Noughties, 2010s and 2020s —
  30-odd questions each, so any decade can carry a full 20-question game on its own
- Three question types: multiple choice, true/false, and "nearest number wins"
- Points for right answers and speed (1,000 down to 500), final question counts double
- Host settings: 10/15/20 questions, Relaxed 30s / Normal 20s / Quick 12s,
  pick categories, "easier questions only", number rounds on/off. The decades are
  off by default (so a normal game stays a mixed bag); tap them on, or use the
  All / None shortcuts for a music night.
- Reveal after every question (who picked what, a "did you know?" fact, standings)
- Questions aren't repeated across games on the host's device until the bank runs low

## Word race

Everyone races the same five-letter word at the same time, on their own phone.

- Six guesses each. Green = right letter, right place; amber = right letter,
  wrong place; grey = not in the word.
- You can see how your rivals are getting on — their tiles show up as little
  coloured blocks, **without the letters** — and who has solved it.
- Fewer guesses scores more: 1,200 / 1,000 / 850 / 700 / 550 / 400, plus a 150
  bonus for whoever cracks it first. Nobody loses points for missing one.
- The round ends when everyone has solved it or run out of guesses, or when the
  clock runs out. Then everyone's grid goes up side by side with the answer.
- Host settings: 3 / 5 / 7 words, 5 min / 3 min / 90s per word, and a
  **Tricky words** switch. Off, it's everyday words that anyone would use;
  on, it also draws on a harder list.
- Awards at the end for most words cracked, fewest guesses and fastest finger.

Guesses have to be real words (about 8,400 of them are accepted). The answer
never leaves the host's phone — it only ever sends back the tile colours — so
there's nothing to peek at in another tab.

## Donkey derby

A race night. Everyone starts with 100 carrots and backs a donkey each race.

- Five runners a race, drawn from a stable of 24. Tap a donkey, tap a stake
  (5 / 10 / 25 / 50 / all in) and you're on — tap again to change your mind
  while the book is open.
- The race then runs on every phone at once: about twenty seconds, with
  commentary, the odd donkey stopping for a snack, and a photo finish when it's
  close. A winning bet pays the odds plus your stake back.
- **The odds are honest.** Each donkey gets a hidden rating for the meeting, and
  the host's phone simulates each race 600 times to work out every runner's real
  chance of winning; the price on the card comes from that. The favourite wins
  about a third of the time and the outsider about one race in nine, so the form
  on the card is worth reading and a long shot is worth a punt.
- Fields are matched on ability, the way a handicapper would, so races are
  contests rather than processions.
- **Nobody gets knocked out.** Drop below a stake and you get a few carrots to
  keep you going, and the last race is the **Gold Cup** at double odds, so a
  shocking night can still be rescued.
- Awards for most winners picked, the longest-odds win, the biggest haul and
  backing the same donkey to the bitter end.

It's play money throughout — carrots, not cash, and nothing real is staked.

All three games share the same room, scores screen, reactions and "Play again".

## Put it online (one-off, ~2 minutes)

It's a single file: `index.html`. It must be served over **https** (the room
encryption needs a secure page).

**GitHub Pages**
1. Create a new public repo, e.g. `clever-clogs`, and upload the page as `index.html`
   (the delivered file is named `clever-clogs-v3.0.html` — rename it on the way in,
   or use GitHub's "rename" after uploading).
2. Settings → Pages → Build and deployment → Deploy from a branch → `main` / root → Save.
3. After a minute it's live at `https://<your-username>.github.io/clever-clogs/`.

**Or Netlify Drop** — sign in at app.netlify.com/drop and drag the folder containing
`index.html` onto the page. Any other static host works too.

**Which version am I running?** It's printed under the buttons on the home screen
and at the bottom of the ••• menu, and as an HTML comment on the second line of
the file. Updating means replacing `index.html` — the link and room codes don't change.

Send the link to the family once; after that, the host shares invite links
(`…/clever-clogs/#ROOM`) from the lobby's **Share invite** button.

## Playing

1. Host: open the link → **Start a game** → name + mascot → **Open the room**.
2. Tap **Share invite** (WhatsApp etc.) or read out the four-letter code.
3. Everyone else opens the link → name + mascot → **Join the game**.
4. Host picks **Quiz night**, **Word race** or **Donkey derby**, sets it up, and taps start.

The host's phone runs the game, so keep that screen on (the page asks the phone
not to sleep where it can). If it does drop out, everyone sees "Waiting for …'s
phone to reconnect" and the game resumes when the host reopens the page.
Use the ••• menu for sound, **colour-blind colours** (blue/orange tiles instead
of green/amber), skipping a question or word, ending early, leaving or closing
the room.

Quick check before game night: open the link on a laptop and a phone, start a
game on one, join from the other, and play a question. The dot on the ••• button
is green when connected (amber = some relays unreachable, red = none).

## How it works

- **No server of our own.** Phones talk through three free public MQTT brokers
  over secure WebSockets, all at once (EMQX, HiveMQ, Eclipse). If one is down the
  others carry the game; duplicate messages are dropped.
- **Host-authoritative.** The host's browser runs the game engine and publishes
  the game state as a retained message every few seconds and on every change.
  Players send "hello", "answer" and "guess" messages; these are retried until the
  host confirms them. Timing uses relative times, so phone clocks don't matter.
- **Nothing to spoil.** The published state carries the question's options or the
  word race's tile colours — never the right answer or the word itself until the
  reveal. Your own letters are kept on your own phone, so a refresh mid-word puts
  your grid back. In the derby the race is only sent out once the book has closed,
  and who backed what stays private until the off.
- **Private-ish rooms.** The room code is hashed into the topic name and into an
  AES-GCM key, so the relays only ever see scrambled bytes. A four-letter code is
  short, so treat this as keeping casual snoopers out rather than strong security —
  the game only ever sends first names, mascots, answers and tile colours.
- **Resume.** The host's game is saved in the browser (localStorage); players'
  sessions are too, so a refresh drops you straight back in.
- Closing the room clears the retained state from the relays.

### Your own relay

Add `?relay=` with one or more comma-separated WebSocket URLs to use different
brokers, e.g. `https://…/clever-clogs/?relay=wss://my-broker.example/mqtt`
(credentials can go in the URL: `wss://user:pass@host/mqtt`). Everyone must use
the same link, which the invite button preserves.

## Source, building and tests

```
src/questions.js   question bank + categories
src/words.js       word lists: everyday answers, tricky answers, allowed guesses
src/wordgame.js    tile colours, word picking, word-race scoring
src/derby.js       the stable, the race simulation, honest odds, payouts
src/relay.js       tiny MQTT 3.1.1 client, multi-broker relay, room encryption
src/engine.js      all three games: picking, scoring, host state machine
src/app.js         screens, controls, keyboard, sounds, effects
src/styles.css     the look
src/index.html     page template
build.js           inlines everything into dist/index.html
```

The version lives in one place: `const VERSION` at the top of `src/app.js`. The build
stamps it into the page (home screen, ••• menu, an HTML comment) and writes `dist/VERSION`.
Bump it whenever you send a new copy out, so the family can tell what they're on.

- Build: `node build.js`
- Question bank check: `node test/check-questions.js`
- Engine tests: `node test/engine.test.js`
- Word race tests (tile colours, scoring, a whole game): `node test/word.test.js`
- Derby tests (the simulation, that the odds match the real chances): `node test/derby.test.js`
- Relay tests against a strict local broker: `node test/relay.test.js`
- Full browser runs (need Playwright + Chromium):
  `node test/e2e.js` — quiz with host + two players + late joiner, reloads, a broker outage
  `node test/e2e-word.js` — word race with three players, then switching back to the quiz
  `node test/e2e-derby.js` — a race night: betting, the race in sync on three phones, the Gold Cup

### Adding questions

Append to `QUESTION_BANK` in `src/questions.js`:

```js
{c:"wales", d:2, q:"Question text?", a:"Right answer", w:["Wrong 1","Wrong 2","Wrong 3"], f:"Optional fun fact."}
{c:"sci", d:1, t:"tf", q:"A statement.", a:true}
{c:"geo", d:3, t:"num", q:"How tall is …, in metres?", a:1085, u:"metres"}   // u:"year" for years
```

`c` is one of: `gk`, `food`, `screen`, `music`, `geo`, `hist`, `sci`, `nature`,
`sport`, `wales`, `words`, or a decade — `m60`, `m70`, `m80`, `m90`, `m00`, `m10`, `m20`.
`d` is difficulty 1–3. Use curly quotes/apostrophes (’ ‘ “ ”) in text, and never quote
song lyrics — titles, artists, years and facts only. Then run the check script and
`node build.js`. New categories go in `CATEGORIES` and `CATEGORY_GROUPS` at the top
of `src/questions.js`.

### Adding words

`src/words.js` holds three lists as plain concatenated five-letter strings:
`WORD_ANSWERS` (everyday answers), `WORD_TRICKY` (the harder ones) and
`WORD_ALLOWED` (everything accepted as a guess). Keep them uppercase, five
letters, and make sure every answer also appears in the allowed list —
`node test/word.test.js` checks that.

### Adding donkeys

`STABLE` at the top of `src/derby.js` is a list of `{n: "Name", s: "#silks"}`.
Ratings are drawn fresh each meeting, so a new donkey needs nothing but a name
and a colour. `node test/derby.test.js` checks the odds still match reality.

## Versions

- **3.0** — the donkey derby added: betting, a live race on every phone, honest odds.
- **2.0** — the word race added; the host picks the game in the lobby. Colour-blind
  palette in the ••• menu.
- **1.1** — music by decade: ’60s through 2020s, off by default, with All / None shortcuts.
- **1.0** — the quiz: 686 questions, 11 categories, multiplayer over public relays.

## Design

Saturday-night telly studio: deep navy ground, marquee light bulbs (the timer is
a row of bulbs going out), a raffle-ticket room code, chunky A–D answer tiles and
flip-over word tiles. Type: Shrikhand (display), Atkinson Hyperlegible (everything
you read — chosen for legibility at any age), Doto dot-matrix for the big LED
numbers. Fonts load from Google Fonts with system fallbacks. Everything respects
"reduce motion", and the ••• menu has a colour-blind palette for the word tiles.
