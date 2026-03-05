---
name: fuel-tracking
description: "MANDATORY skill for trip tracking. Use IMMEDIATELY when user says 'їду', 'виїжджаю', 'поїхав', 'приїхав', 'повернувся', 'на місці', 'доїхав'. Ask for odometer reading, write to fuel-log.md. Never suggest travel tips — only ask odometer."
metadata:
  {
    "openclaw":
      {
        "emoji": "⛽",
      },
  }
---

# Fuel Tracking — Облік поїздок

Record work trips to a markdown log file.

## Log file

All trip data is appended to: `/data/.openclaw/workspace/fuel-log.md`

## Constants

- Base location (office): **Сенатор**
- Fuel rate: 11 l/100km
- Fuel price: 68.99 UAH/l

## Scenario 1: DEPARTURE — user says "їду на...", "виїжджаю", "поїхав", "еду"

When user says they are going somewhere:

1. Ask: "Який зараз показник одометра?"
2. After receiving odometer, append to `/data/.openclaw/workspace/fuel-log.md`:

```
## <TODAY_DATE> | Виїзд
- Куди: <DESTINATION>
- Звідки: Сенатор
- Одометр старт: <ODOMETER>
- Статус: в дорозі
```

3. Reply: "Записав! Одометр: XXXXX, маршрут: Сенатор → [destination]. Гарної дороги! 🚗"

## Scenario 2: ARRIVAL — user says "приїхав", "повернувся", "на місці", "доїхав"

When user says they arrived:

1. Ask: "Який зараз одометр?"
2. Ask: "Яка була мета поїздки?"
3. Read `/data/.openclaw/workspace/fuel-log.md` to find the last trip with status "в дорозі"
4. Calculate:
   - distance = end_odometer - start_odometer
   - fuel = distance * 11 / 100
   - compensation = fuel * 68.99
5. Update the trip entry in fuel-log.md, change status to "завершено" and add:

```
- Одометр кінець: <END_ODOMETER>
- Назад: Сенатор
- Мета: <PURPOSE>
- Пробіг: <DISTANCE> км
- Паливо: <FUEL> л
- Компенсація: <COMPENSATION> грн
- Статус: завершено
```

6. Reply: "Записав! Пробіг: XX км, паливо: X,XX л, компенсація: XXX,XX грн 📝"

## Scenario 3: REPORT — user says "звіт", "звіт за місяць", "покажи поїздки"

Read `/data/.openclaw/workspace/fuel-log.md` and format a summary table.

## CRITICAL RULES

- ALWAYS ask for odometer, NEVER invent values
- Use Ukrainian decimal format: "9,46" not "9.46"
- If user says "їду" without destination — ask where
- Base location = "Сенатор"
- DO NOT suggest weather, taxi, checklists or travel tips
- ONLY ask for odometer when user mentions a trip
