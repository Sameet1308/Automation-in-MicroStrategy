# MicroStrategy: Dashboard Prompt Visibility - Web vs Library

**Date**: January 8, 2026  
**Category**: Troubleshooting, Administration  
**Tags**: `microstrategy` `library` `web` `prompts` `caching` `dossier`

---

## Problem Statement

A prompted dashboard displays prompts correctly when run in **MicroStrategy Web**, but prompts are **hidden/not visible** when opened in **MicroStrategy Library**. However, when the **Re-Prompt** button is clicked in Library, the prompts become visible.

## Root Cause

This is **not a bug** but a **design feature** related to how MicroStrategy Library handles dashboard state and cached prompt answers.

### Key Differences: Web vs Library

| Aspect | MicroStrategy Web | MicroStrategy Library |
|--------|------------------|---------------------|
| **Default Behavior** | Always displays prompts on run | Uses cached/saved prompt answers |
| **Execution Model** | Requires user input before execution | Pre-executes with last saved answers |
| **Prompt Display** | Shows prompt page upfront | Hidden if answers are cached |
| **State Management** | Stateless per session | Retains last viewed/saved state |
| **Use Case** | Ad-hoc analysis, initial runs | Continuous monitoring, saved views |

## Technical Explanation

### How Library Caching Works

1. **Initial Web Run**: User answers prompts → Dashboard executes → Prompt answers are saved with dashboard state
2. **Library Load**: Library retrieves dashboard with cached prompt answers → Pre-executes dashboard with saved values → Prompts hidden (already "answered")
3. **Re-Prompt Action**: Clears cached state → Forces prompt page display → User provides new answers → New answers become cached state

### Why Prompts Are Hidden

When Library opens a dashboard:
- It checks for **saved prompt answers** from previous executions
- If answers exist, Library applies them automatically and executes the dashboard
- Since prompts are already "answered" by cached values, the prompt page is skipped
- Users see the dashboard results directly with pre-applied filters

### Why Re-Prompt Shows Prompts

The **Re-Prompt** button explicitly:
- Clears the cached prompt answer state
- Forces the prompt interface to display
- Allows users to change filter values
- Saves new answers as the latest cached state

## Configuration & Solutions

### Author-Side Configuration (Web Authoring)

When saving a dashboard, configure prompt behavior:

**Option 1: Always Display Prompts**
- Set dashboard property: **"Display prompt"** = Enabled
- Ensures prompts show on Library load regardless of cached answers

**Option 2: Discard Current Answers**
- Before saving: Select **"Discard current answers"**
- Prevents Library from pre-executing with author's saved prompt values

**Option 3: Default Open State** (MicroStrategy 2021 Update 1+)
Configure at three levels (most granular takes priority):
- **iServer level**: System-wide default
- **Project level**: Project-specific behavior
- **Content level**: Individual dashboard configuration

Settings:
- `Last Viewed State` → Opens with user's last prompt selections
- `Last Saved State` → Opens with author's saved prompt values

### End-User Workaround

For end users experiencing hidden prompts:
1. Click the **Re-Prompt** button in Library
2. Prompts will display for modification
3. New selections persist as cached state for future opens

### Administrator Configuration

Check dashboard properties in Web Authoring:
```
Dashboard Properties → Prompt Settings
- [ ] Retain current prompt answers
- [x] Display prompt on load
- [ ] Use saved answers as default
```

## Best Practices

### For Dashboard Authors

1. **Decide on prompt behavior** before publishing:
   - Always prompt users → Enable "Display prompt"
   - Use last selections → Enable "Retain current answers"
   - Fresh start each time → Disable answer retention

2. **Test in both interfaces**:
   - Run in Web → Verify prompt display
   - Open in Library → Confirm expected behavior
   - Click Re-Prompt → Validate prompt accessibility

3. **Document expected behavior** in dashboard description

### For BI Administrators

1. **Standardize prompt configuration** across projects
2. **Communicate Library behavior** to end users
3. **Configure default open state** based on user feedback
4. **Monitor cache performance** for prompted dashboards

## Related Concepts

- **Personal Answers**: User-specific saved prompt values
- **Dashboard Caching**: Performance optimization that stores executed results
- **Prompt State Retention**: Library feature to preserve user selections
- **Re-execution vs Cache Load**: When Library generates new data vs serves cached data

## Interview Talking Points

When discussing MicroStrategy Library behavior:

✅ **Understand the design intent**: Library optimizes UX by retaining state  
✅ **Know the difference**: Web = stateless, Library = stateful  
✅ **Caching implications**: Prompt answers affect cache keys and performance  
✅ **Configuration hierarchy**: iServer > Project > Content level settings  
✅ **User experience trade-offs**: Convenience vs control  

## Troubleshooting Checklist

- [ ] Verify dashboard has been run at least once (prompts answered)
- [ ] Check if saved prompt answers exist in dashboard definition
- [ ] Review dashboard properties for prompt display settings
- [ ] Test Re-Prompt functionality to confirm prompts are accessible
- [ ] Check user permissions for prompt modification
- [ ] Verify Library version supports prompt retention (2020+)
- [ ] Review default open state configuration at iServer/Project level

## Additional Resources

- MicroStrategy Docs: "Re-Prompt a Dashboard"
- MicroStrategy Docs: "Saving and Re-using Prompt Answers"
- KB441304: How Prompts Work with Dossiers
- MicroStrategy 2021 Update 1: Default Open State Feature

---

## Version History

| Date | Change |
|------|--------|
| 2026-01-08 | Initial documentation - Library prompt visibility behavior |

---

**Author Notes**: This is expected behavior, not a defect. Understanding the difference between Web and Library execution models is crucial for proper dashboard design and user training.
