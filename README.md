# ShowMeOnMap Agent Skills

Skills that teach AI agents to make **cited, data-driven maps** with the
[ShowMeOnMap](https://showmeonmap.com) MCP server — earthquakes, wildfires,
statistics by region, transit, parks, spatial analysis — and to talk about
those maps without inventing a single number.

Works with Claude (claude.ai, Claude Desktop, Claude Code) and any agent that
reads `SKILL.md` files. The skills only describe how to use the public MCP
tools well; they contain no ShowMeOnMap source code.

> **Status: preview (0.1.0).** The setup and core skills are complete;
> `compare-regions`, `neighbourhood-profile` and `network-and-transit` are
> outlines and will be filled in.

## Install

**Claude Code** — installs the skills *and* registers the MCP connector:

```bash
/plugin marketplace add FrankAIUser/showmeonmap-agent-skills
/plugin install showmeonmap@showmeonmap
```

**claude.ai / Claude Desktop** — add `https://showmeonmap.com/api/mcp` as a
custom connector, then upload any skill folder under Settings → Capabilities →
Skills.

**Other agents** — copy `skills/<name>/SKILL.md` into your agent's skill or
rules directory. `catalog.json` lists every skill with its trigger phrases.

## The skills

Three layers. Start at the top; recipes assume the core skills.

| Layer | Skill | Use it when |
|---|---|---|
| Setup | [`connect-showmeonmap`](skills/connect-showmeonmap/SKILL.md) | First use; the tools are missing; sign-in or credit errors |
| Core | [`build-and-refine-a-map`](skills/build-and-refine-a-map/SKILL.md) | Any map request, and every follow-up change to that map |
| Core | [`grounded-map-answers`](skills/grounded-map-answers/SKILL.md) | Writing the text that goes with a map |
| Core | [`run-spatial-analysis`](skills/run-spatial-analysis/SKILL.md) | Hot spots, per-zone totals, Census enrichment, joins, nearest neighbour |
| Core | [`recurring-map-workflows`](skills/recurring-map-workflows/SKILL.md) | "Every week", "for each of these 12 districts", saved map recipes |
| Core | [`share-and-export`](skills/share-and-export/SKILL.md) | Links, PNG/PDF, GeoJSON/CSV out |
| Recipe | [`hazard-watch`](skills/hazard-watch/SKILL.md) | Earthquakes, wildfires, storms, floods, volcanoes near a place |
| Recipe | [`compare-regions`](skills/compare-regions/SKILL.md) | "X by country / state / region", two-variable maps |
| Recipe | [`neighbourhood-profile`](skills/neighbourhood-profile/SKILL.md) | What is around a place, and who lives there |
| Recipe | [`network-and-transit`](skills/network-and-transit/SKILL.md) | Railways, transit lines, power plants, roads |

## Costs

Building or changing a map spends the user's prepaid ShowMeOnMap credits;
reading state and exporting are free, and failed operations are refunded.
Every skill that spends credits says so before the agent acts.

## License

MIT. Map data carries its own licences, cited on every map.
