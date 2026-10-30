# Фобос — Сети связи — Базы: handoff
 
Deliverable: один HTML-файл (~5500 строк, ~450 КБ), опубликован как артефакт (v33). В проект НЕ загружен (дорого по контексту) — при продолжении работы запросить текущий файл из артефакта (Artifact read) или из scratchpad.
 
## Стек
three@0.128.0 + OrbitControls (jsdelivr), один большой IIFE. Единица сцены ≈ 1 км. Меш Фобоса, точный ray/triangle `groundAt`.
 
## Базы и координаты
- STA «Гулливер-1»: X0=13.069, up=+x, sg=1. STB «Терминал»: −XN, поворот π, sg=−1.
- Локальные: lx=sg*(x−ox), ly=y, lz=sg*z; H0=.03. STA.ox/STB.ox ~ строка 3832. STB.pol(r,d) → мир y=r·cos d, z=−r·sin d.
- Навигация: секции комментариев `/* ---------- ... ---------- */` (~350) — используйте как оглавление (grep).
## Модуль астронавта (`var astro={on:false};(function(){…})()`)
heightfield (getHF/hfAt), support(P,step), blockers, A* planPath, planAny (outside/inside/mixed через DOORS), галереи GALS/HALLS, galFind/galClear (полосы: lo=.0006), laneHead/steer, insideRoute (входной waypoint), curTarget + descended(), stuck-guard (окно 15 с, 1-й раз replan только снаружи, 2-й — стоп с подсказкой), сглаженная камера. Концы галерей: 'b' тупик, 'r' рампа, 'h' зал. Роботы идут по v<0, астронавт по v>0.
Spawn Pin: красная стрелка, SP, floorSnap, двойной клик/Confirm/Esc; тесты вызывают `__astro.start()`.
 
## UI
makeInfoWin (отдельные окна), карта базы #amap (mapData/mapDraw), кнопка «Взгляд», карточки небоскрёбов a_tw<i>, подписи (extra defs tier 3).
 
## Производительность
Свет скрыт до затмения, stationGate (110 + горизонт), интерьеры/роботы по дистанции. Загрузка ~13 с в swiftshader, из них компиляция шейдеров ~8.6 с; loader minMs 2400 намеренно.
 
## Тесты (playwright + swiftshader), файлы test_*.js
as12b (ходьба по полосам: in→pad, pad→in, g0→g1), pin4 (пин), roof3 (спуск с крыши), map1 (карта), info (окна info), prog (прогресс загрузки), shots (скриншоты). Запуск: `node test_X.js` с путём к html внутри скрипта (поправить).
 
## Известные проблемы / TODO
- Не воспроизведено: «вид сверху и не приближается», «висит в воздухе» после установки скафандра (подозрение: огромное кольцо цели; масштаб теперь от дистанции).
- Тупики 'b' в вестибюлях корпоративных башен.
- Не сделано: ленивая сборка Терминала/крио/башен/кратеров/Марса, тайлинг/LOD базы.
## Правила работы
Задачи пачками по 3–6 связанных пунктов; баги — отдельно с описанием (где, что делал, что ожидал). Ответы — по-русски, кратко; ссылку на артефакт не вставлять.
 
## Артефакт (единый источник кода)
Актуальный код — опубликованный артефакт: https://claude.ai/artifact/4hn8hux7a5G8TBnMZ5vGuA (читать через Artifact read, обновлять публикацией в тот же URL). Файл общий для всех чатов проекта; в проект он не загружен. Правит один чат за раз; перед началом работы всегда читать свежую версию. Строки ниже — по версии v33, ориентироваться по комментариям `/* ---------- ... ---------- */` (grep), номера уплывают.
 
## Карта тем и секций (v33, номера строк приблизительные)
| Тема (чат) | Секции | Строки | Общие зависимости | Тесты |
|---|---|---|---|---|
| Ядро сцены, Фобос, Марс | utils, scene, Phobos, Mars, helpers, layer groups | 719–1030 | groundAt, X0, камера — используют все | smoke |
| Лифт, реле, башни связи, криосеть | space elevator, elevator pad, relays, towers (blinking tips), cryo network | 1031–1423 | P.L.lift, слои | smoke |
| База Гулливер-1 (STA): здания и территория | Sub-Mars base, spaceport, corporate glass towers, cranes, platform, parking, hangar, solar, tank farm, sci/industrial, domes, dishes, heaps, ring habitat, pipelines, filler, floodlights, piles | 1424–2738 (без галерей 1503–1969) | STA.towers/pads/hub/halls, hall(), pol() | smoke, test_shots |
| База Терминал (STB): здания и территория | Anti-Mars base: carousel, tension, slings, 6 pads, haulers, smelting, warehouses, admin (Т-1…Т-4), science, DSN, solar, accidents, containers | 2739–3864 (без галерей 2818–3113) | STB.halls/named, pol(), sg=−1 | smoke |
| Галереи и внутренние помещения | GDSTA/GDSTB: glazed galleries, interior fit-out, robots in galleries, spacecraft templates; hall(); концы 'b'/'r'/'h' | 1503–1969, 2818–3113 | GALS/HALLS (читает модуль астронавта: galFind, galClear, DOORS), S.interiors/S.rob, stationGate | as12b, pin4, shots, smoke |
| Небо, подписи, тур, корабль | backup cubesats, sky, labels, tour, ship | 3865–4147 | labels extra defs, findObj | smoke |
| UI: окна info, панель баз, объекты, HUD, кнопки | floating info windows, bases panel, extra HUD, v24 UI wiring | 4148–4515, 5524–конец | makeInfoWin, список объектов (a_tw<i>, goTo el), «Взгляд» | test_info, smoke |
| Астронавт: физика и ходьба | astronaut: support, galleries and halls (analytic), blockers, suit model, state, spawn/landing, can-walk, steering, pose, setup, commands, ray, physics | 4516–5341 | GALS/HALLS, DOORS, planPath/planAny, stuck-guard | as12b, roof3, smoke |
| Астронавт: Pin, камера, карта | spawn pin, camera input and picking, base map | 5165–5493 | SP/floorSnap, mapData/mapDraw, #amap | pin4, map1, smoke |
| Главный цикл | loop | 5494–5523 | порядок update всех модулей | smoke |
 
Общие зоны риска (правка тут затрагивает несколько тем): GALS/HALLS и hall(); координаты STA/STB (X0, sg, pol); support/groundAt; stationGate и gating интерьеров. После правок в них — «прогони smoke».
 
## Журнал версий (отдельный артефакт, меняется часто)
История версий v30+ (что менялось и почему), текущее состояние и открытые проблемы ведутся в документе «Фобос — журнал версий»: https://claude.ai/artifact/UWqMvKmCv2s9XoVUVa3HRT . Читать его при старте (Docs read), дописывать новую версию сверху после каждой публикации кода. В проекте этот журнал не дублируется, чтобы не сбрасывать кэш.
