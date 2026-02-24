# Claude Code + Portkey in Practice
**David Blankenship

---

## Real Feature: Issue #1078
**Configurable chat button with mobile responsiveness**

**Quick Links:**
- **Issue:** https://github.com/yalesites-org/YaleSites-Internal/issues/1078
- **PR:** https://github.com/yalesites-org/ai_engine/pull/32

**Complexity:** 7 files, mobile breakpoints, WCAG compliance, database migration, backward compatibility

**Context:** Expedited request

**Status:** ✅ Approved, merged, live in production (PR #32)

| Traditional                | AI-Assisted                 |
|----------------------------|-----------------------------|
| ~6 hours (my own estimate) | **~3 hours**                |
| Docs take longer to write  | **Detailed docs generated** |
| Ad-hoc testing             | **19-point checklist**      |


---

## How: My Actual Workflow (4 Sessions)

**Session 1: Project Requirements Document (PRD) Creation (2 min)**
```
Me: "Read issue #1078, create PRD for icon config"
Claude: Creates CLAUDE_chat_icon_prd.md
```

**Session 2: Icon Config Implementation (38 min)**
```
Me: "Read PRD and implement"
I guide implementation:
  "Add icon select field to form"
  "Update template to use configured icon"
  "Scale icon to match original SVG size"
  "Handle color fill appropriately"
Me: "/commit-conventional" (×2 commits)
```

**Session 3: Mobile Styling PRD + Implementation (2.1 hours)**
```
Me: "Create PRD for mobile responsiveness"
Claude: Creates CLAUDE_1078_widget_styling.md
Me: "Read PRD and plan implementation"

Then I guide iteratively with screenshots:
  "Make it 65px always, pill shape"
  "Swap icon/text, text on left"
  "Center with equal padding"
  "Gap too big" [screenshot]
  "Icon cut off on mobile" [screenshot]
  "Add 0.25em right margin"

Me: "/commit-conventional" (×4 for each change)
```

**Session 4: PR Creation (5 min)**
```
Me: "Write PR description with this template"
Claude: Generates 19-point testing checklist
```

**Total: ~3 hours across 4 sessions (vs ~6 hours traditional)**

**Reality:** I directed every visual decision, Claude typed the code

**Code Quality:** The code Claude generated is essentially what I would have written - same approach, same patterns, just faster

**Important:** This is NOT Claude out-of-the-box:
- Custom configuration files for standards and patterns
- Custom commands for repeatable tasks (commits, reviews, etc.)
- Thoughtful setup investment required

**Key:** Each new session = fresh context, no degradation

---

## Why Portkey (Not Claude Pro)

| Claude Pro ($20/mo) | Portkey (Pay-as-you-go API) |
|---------------------|----------------------|
| ❌ ~45 messages per 5-hour session | ✅ Unlimited messages |
| ❌ 5-hour cooldown between sessions | ✅ No cooldowns |
| ❌ Would hit limit mid-implementation | ✅ Completed all work in one day |
| ❌ Multiple sessions across days | ✅ Continuous workflow |

**Note:** Portkey provides API access without an enterprise agreement - pay-as-you-go pricing

**My Session 3 alone was 2+ hours with many messages** → Claude Pro would force breaks mid-work

---

## Impact on My Work

### Before
- ~6 hours (my own estimate)
- Manual boilerplate typing
- Writing documentation manually
- Time-intensive debugging

### After
- ~3 hours (50% time savings)
- Boilerplate handled by AI
- Documentation generated
- Confident, less stressed

**I still make all decisions, review all code, test everything**

**But freed from repetitive tasks and implementation details**

---

**Questions?** Happy to show Portkey dashboard or discuss details.

**Portkey Dashboard:** https://app.portkey.ai/
