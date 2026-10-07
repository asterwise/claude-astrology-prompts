# Give Claude a real birth chart: a prompt kit for the Asterwise MCP server

Claude can talk about astrology fluently, but it cannot compute where the planets were. Ask it for a birth chart on its own and it produces a plausible chart from memory that is wrong in ways you cannot see. Connect the [Asterwise MCP server](https://asterwise.com/mcp/) and the same question makes Claude call a real ephemeris: every position comes from the Swiss Ephemeris, [checked against NASA JPL Horizons](https://asterwise.com/accuracy/), and Claude reasons over verified numbers.

These prompts are the ones that reliably trigger the right tool. Copy them, replace the birth details, and keep the phrasing; the wording is what steers Claude to the tool rather than to its memory.

Tutorial: [asterwise.com/blog/give-claude-a-real-birth-chart](https://asterwise.com/blog/give-claude-a-real-birth-chart/)

## Connect first

- **Claude.ai and the Claude apps**: Settings, Connectors, Add custom connector, URL `https://mcp.asterwise.com/mcp`. Sign in with your Asterwise account when the browser opens and approve. Free tier: 500 calls a month, no card.
- **Claude Desktop with a config file**, **Cursor**, **Windsurf** and other MCP clients: the JSON snippets are on [asterwise.com/mcp](https://asterwise.com/mcp/).

## The honesty test

Run this prompt before connecting, then again after. Same birth, same words.

```
Cast the Vedic birth chart for someone born on 12 November 1985 at 06:45 in Mumbai, India.
Give me the ascendant, the Moon's nakshatra, and the sign and degree of Saturn.
```

The correct answer, published with the full working at [asterwise.com/proof](https://asterwise.com/proof/): ascendant Libra 25.30°, Moon in Swati, Saturn in Scorpio 5.75° (Lahiri ayanamsa). Before connecting, Claude's answer is a guess and will usually differ. After connecting, it calls `asterwise_get_natal_chart` and matches.

## Core prompts

Each prompt names the tool Claude calls, so you can ask it to show the raw result at any point.

**1. Natal chart with interpretation** — `asterwise_get_natal_chart`
```
Give me the full Vedic birth chart for [name], born [date] at [time] in [city, country], with interpretation. Then explain the three strongest placements in plain language.
```

**2. Where am I in my dasha** — `asterwise_get_dasha`
```
For the same birth, which Vimshottari mahadasha and antardasha am I in right now, when does each end, and what does the current antardasha lord suggest for the next twelve months?
```

**3. Compatibility, with the veto shown separately** — `asterwise_get_compatibility`
```
Check Vedic marriage compatibility between [person 1: name, date, time, city] and [person 2: name, date, time, city]. Give me the Ashtakoot score out of 36, and separately tell me whether Rajju or Vedha applies and what that means.
```
Asking for the veto separately matters: in the classical method Rajju or Vedha stops a match regardless of the score, and the tool returns them as separate fields so Claude cannot blend them into the points.

**4. Today's panchanga and Rahu Kaal** — `asterwise_get_panchanga`, `asterwise_get_rahu_kaal`
```
What is today's panchanga for [city], and when is Rahu Kaal today so I can avoid it?
```

**5. Picking a date** — `asterwise_get_muhurta`
```
Find auspicious muhurtas for a [housewarming / wedding / business opening] in [city] between [date] and [date]. Rank the best three and say why.
```

**6. Transits right now** — `asterwise_get_gochar`, `asterwise_check_sade_sati`
```
What are the current transits over my chart, born [date, time, city]? Tell me which one matters most this month, and whether I am in Sade Sati.
```

**7. The year ahead** — `asterwise_get_varshaphal`
```
Cast my Varshaphal (solar return) for [year], born [date, time, city]. Who is the year lord, and what does the Muntha say?
```

**8. Western reader** — `asterwise_get_western_natal`, `asterwise_get_western_transits_weekly`
```
Give me the Western natal chart with Placidus houses for [date, time, city], then this week's transits to it. Keep it tropical.
```

**9. Divisional chart** — `asterwise_get_divisional_chart`
```
Show me the Navamsa (D9) for [date, time, city] and explain how it changes the reading of the seventh house compared with the birth chart.
```

**10. Numerology and tarot** — `asterwise_get_numerology_profile`, `asterwise_get_tarot_three_card_spread`
```
What is my life path number, born [date] with the name [full name]? Then draw a three-card spread for the question: should I change jobs this year?
```

## Two prompts about trust

**11. Show your working**
```
Which Asterwise tool did you just call, and what did it return before you interpreted it? Show me the raw numbers.
```

**12. How accurate is this?**
```
How accurate are the planetary positions you are using, and how can I check them myself?
```
Claude answers from the server's own description and points to the published accuracy check.

## For astrologers

The same prompts work for client charts: replace the birth details, ask for the chart first, then ask follow-up questions in your own words. Claude keeps the chart in context, so "now the D10" or "when does this antardasha end" needs no repetition. For a session with a client, run prompt 1, then 2, then 6, and keep prompt 11 handy when you want to see the numbers behind an interpretation.

MIT licensed. The prompts are yours to adapt.
