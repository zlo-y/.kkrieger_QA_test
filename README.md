# ТЗ: QA Testing - .kkrieger (v1.0 beta)

"Привет! Меня зовут Рафаэль, я подготовил этот репозиторий, чтобы наглядно показать результаты тестирования .kkrieger. Ниже — разбор полетов по заданиям."

---

## Задание 1
1. **Counter-Strike 2**
2. **Resident Evil 4 (Remake)**
3. **Resident Evil 7 Biohazard**

**Любимчик:** Resident Evil 4.
**Почему:** Это пример игры, которая работает почти идеально. Меня как C#-разработчика подкупает техническая чистота: в RE4 практически невозможно найти «кривых» коллизий или сломанных скриптов. Всё — от того, как враги реагируют на выстрел в колено, до менеджмента ресурсов — построено очень грамотно. Это тот уровень качества и внимания к деталям, который я хочу видеть в проектах, которые буду тестировать.

---

## Задание 2: Список найденных дефектов
В ходе тестирования было выявлено 10 ключевых дефектов:

| № | Описание | Приоритет |
|---|----------|-----------|
| 1 | Телепортация или вылет за карту при прыжке в узких проемах (между колонной и стеной). | **High** |
| 2 | Застревание NPC (врагов и боссов) в углах и колоннах. | Medium |
| 3 | Прохождение персонажа сквозь текстуры дверей до их открытия. | Medium |
| 4 | Визуальное зависание снарядов в воздухе при стрельбе в упор. | Low |
| 5 | Бесконечный респавн (дюп) ресурсов через определенные промежутки времени. | **High** (Exploit) |
| 6 | Зависание NPC на месте после нанесения урона игроку. | Medium |
| 7 | Баги анимации/моделей при зависании игровых объектов. | Low |
| 8 | Clipping камеры сквозь стены при определенных углах обзора. | Low |
| 9 | Ошибки поиска пути: враги упираются в стены и продолжают идти в них. | Medium |
| 10 | Застревание игрока в текстурах пола при прыжке в зонах рядом с колоннами. | **High** |

---

## Задание 3: Подробный баг-репорт (Jira Style)

### [RU] Телепортация/Застревание в узких проемах
* **Summary:** [Physics] Застревание или телепортация на верхнюю часть колонны при прыжке в узком пространстве между колонной и стеной.
* **Priority:** High
* **Environment:** Windows 11, Compatibility Mode (Win XP SP2), DX9.
* **Description:** Физический движок некорректно рассчитывает коллизии игрока в узких «карманах». Это приводит к Hard-lock (застреванию) или телепортации на колонну (недалеко от места прыжка).
* **Steps to Reproduce:**
    1. Найти узкий проем между любой колонной и стеной.
    2. Зайти в проем и совершить 3-5 прыжков (Space).
    3. Удерживать клавиши движения (W/A/S/D) в сторону препятствий.
* **Actual Result:** Игрок застревает внутри текстуры или мгновенно телепортируется наверх рядом стоящей колонны (может сопровождаться «серфингом» по текстуре).
* **Expected Result:** Игрок должен оставаться в пределах игровой зоны; коллизии должны блокировать прохождение сквозь объекты.
* **Attachments:** ![Скриншоты <img width="1030" height="540" alt="5442901210164828209" src="https://github.com/user-attachments/assets/5f62fafc-515c-40c8-8d52-bc31d9e6e7d2" />
, <img width="1061" height="526" alt="5442901210164828208" src="https://github.com/user-attachments/assets/56057d97-3de8-4ce2-9275-74c2011b2d0b" />
]
  
[
Видео процесса телепортации!
https://github.com/user-attachments/assets/baa44151-978b-4ad7-9a38-40591118ba37
]

---

### [EN] Player clipping or OOB displacement when jumping in narrow gaps
* **Summary:** [Physics] Player clipping or out-of-bounds displacement when jumping in narrow gaps.
* **Priority:** High
* **Description:** Physics engine fails to resolve collision detection when jumping in narrow geometric gaps, causing the player to get stuck in textures or teleport to the top of the asset.
* **Steps to Reproduce:**
    1. Locate a narrow gap between any pillar and a wall.
    2. Enter the gap and perform 3-5 consecutive jumps.
    3. Keep pressing movement keys (WASD) against the obstacles.
* **Actual Result:** Player model gets stuck inside the pillar's collision box or instantly teleports to the top of the pillar/out of bounds.
* **Expected Result:** Character should remain within the playable area; collision boundaries must prevent clipping or forced displacement.

---

## Задание 4: Test Case (English)

**ID:** TC-002  
**Title:** Verify Resource One-Time Pickup Logic  
**Pre-conditions:** Player inventory is not full (ammo/health used).

| Step | Action | Expected Result |
| :--- | :--- | :--- |
| 1 | Locate an ammo pack and pick it up. | Resource disappears from the level; inventory count increases. |
| 2 | Stay at the pickup location and wait for 10 seconds. | The resource should NOT reappear (one-time pickup logic). |
| 3 | Observe the pickup location after the waiting period. | **Actual:** Resource respawns, allowing for infinite farming (**FAIL**). |

**Post-conditions:** Resource consumption remains balanced; infinite farming via respawn is impossible.
