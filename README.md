# Heti Kvíz 4.0

## Indítás

Node.js 22.12 vagy újabb verzió ajánlott. Telepítsd a függőségeket az
`npm ci` paranccsal. Helyi fejlesztéshez futtasd az `npm run dev` parancsot.
A fejlesztői szerver alapértelmezésben csak a saját gépről érhető el.
A kiadáshoz futtasd az `npm run build`, majd az `npm start` parancsot.
Az alkalmazás alapértelmezett portja 3000.

A tanári belépéshez a szerveren állíts be egy egyedi, legalább 16 karakteres
`TEACHER_PASSWORD` jelszót. Az alapértelmezett AI-szolgáltató a Gemini,
amelyhez `GEMINI_API_KEY` kell. A `GEMINI_MODEL` alapértéke
`gemini-flash-latest`. A `GEMINI_THINKING_LEVEL=LOW` csak olyan
modellhez állítandó be, amely támogatja ezt az értéket. Átmeneti Gemini-hibánál, időtúllépésnél
vagy hiányzó modellnél a rendszer a `gemini-3.1-flash-lite`, majd a
`gemini-flash-latest` modellt próbálja, ismétlés nélkül. A négy heti
kvíz egymással párhuzamosan készül. Részleges siker esetén az elkészült
tervezetek megnyithatók, a hibás szint külön újrapróbálható. Az OpenAI választható az `AI_PROVIDER=openai`
beállítással és `OPENAI_API_KEY` kulccsal. Helyben a Gitből kizárt
`.env` fájl használható. Élesben az üzemeltető
titoktárolóját, HTTPS-t és `NODE_ENV=production` beállítást használd.

A tanári munkamenet nyolc óráig tart. Helyi memóriatárolóval szerverindításkor
megszűnik, Redis használatakor túléli a szerver újraindítását. Percenként
10 belépési kísérlet, 12 kvíznyi generálás és 5 forráslekérés engedélyezett.
Egy négyszintes csomag négy generálásnak számít.

## Kiadás előtti ellenőrzés

- Futtasd az `npm run lint` és az `npm run build` parancsot.
- Diákként nyiss meg egy heti és egy archivált kvízt. Ellenőrizd a pontozást,
  a kiejtést, a hibák újragyakorlását és a 20 kérdéses szintfelmérőt.
- Tanárként lépj be, frissítsd az oldalt, majd lépj ki. Próbáld ki a forrásnézetet,
  a ZIP-letöltést és a generálást belépve.
- A mentett kvízek, kitűzések és eredmények az adott böngészőben és az adott
  webcímen érhetők el. A korábbi adatok ellenőrzéséhez használd ugyanazt a
  böngészőprofilt és webcímet. Egy külön tesztoldal saját böngészőtárolót kap.

## Kvízek közzététele

A Tanári Stúdióban a generált kvíz tervezetként jelenik meg. A kérdések
szerkeszthetők, az alkalmazás ellenőrzi a kérdésszámot, a feladatformát,
a megoldókulcsot, az ismétlődéseket, a magyarázatot és a fordítást. A külön
AI-ellenőrzés a beállított AI-szolgáltató kulcsával kérhető. A közzétételhez tanári jóváhagyás kell.
A heti csomagban szintenként végezhető el az ellenőrzés és a közzététel.

Ha az automatikus generálás éppen lassú, a Tanári Stúdióban válaszd a
„Kvíz importálása JSON-ból” lehetőséget. Másold a megadott mintát az AI
Studióba, majd a kapott JSON-választ illeszd vissza a kvízoldalra.
A rendszer a kiválasztott szintet, témát és hetet használja. Az importált
tervezet a szokásos szerkesztéssel és tanári jóváhagyással tehető közzé.
Az importálás és a kézi jóváhagyás nem indít AI API-hívást.

Önálló kiszolgálón Redis helyett használható a `PUBLISHED_QUIZ_DIR` változó.
Ez egy írható, tartós lemezkötet mappájára mutasson. Helyben az
alapértelmezett mappa a Gitből kizárt `.quiz-data`. Minden publikálás
önálló pillanatképet ad. Egy javított változat új linket kap, a korábban
megosztott link ugyanarra a változatra mutat. A helyi mentés törlése csak
a böngészőben lévő példányt érinti.

## Saját gyakorlás

A heti kvíz eredményoldalán a „Mentés gyakorláshoz” művelettel jelölheted
meg a kérdéseket. A fejléc „Saját gyakorlás” nézetében szint és témakör
szerint szűrhetsz, és elindíthatod a kijelölt kérdések gyakorlását.
A mentések az adott böngészőben és webcímen maradnak.

## Vercel Preview

A `vercel.json` ugyanazon a webcímen teszi elérhetővé a Vite-tal összeállított
felületet és az Express API-t. A `hetikviz-v4` tároló kiadási ága a `main`. A Vercel-projektben ezt az ágat használd. Node.js 22.x ajánlott. A repó
`npm ci`, `npx vite build` és `dist` beállításait használd.

1. A Vercel Marketplace-ben adj Redis adatbázist a tesztprojekthez.
   A **Settings → Environment Variables** részen ellenőrizd, hogy a
   **Preview** környezetben szerepel a `KV_REST_API_URL` és a
   `KV_REST_API_TOKEN`. Kézi Upstash-bekötéshez az
   `UPSTASH_REDIS_REST_URL` és `UPSTASH_REDIS_REST_TOKEN` páros is
   használható. A tokennek írási jogosultságra van szüksége. A Redisben
   vannak a tanári munkamenetek, a kéréskorlátok és a közzétett kvízek.
2. Ugyanebben a környezetben a `TEACHER_PASSWORD` értéke legyen egyedi,
   legalább 16 karakteres jelszó. Az AI-kvízkészítéshez és ellenőrzéshez
   alapértelmezetten a `GEMINI_API_KEY` kell. A titkos változók neve ne kezdődjön
   `VITE_` előtaggal.
3. Indíts új Preview telepítést az aktuális committal. Nyisd meg a
   `/api/teacher/session` útvonalat a tesztcímen. Kijelentkezve az
   elvárt válasz `{"authenticated":false}`. Ezután lépj be, készíts és
   hagyj jóvá egy próbakvízt, majd nyisd meg a linket privát ablakban.

A Redis kulcsai alapból a Vercel környezet nevét használják előtagként.
Ha több projekt ugyanazt az adatbázist használja, projektenként és
környezetenként adj külön, tartós `QUIZ_STORE_PREFIX` értéket. A már
megosztott linkek elérhetőségéhez ugyanazt az adatbázist és előtagot
használd a későbbi telepítéseknél is.
