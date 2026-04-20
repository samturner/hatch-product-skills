# Priority rubric

The team's standard rubric for prioritising Linear issues. Single source of truth, referenced by the `ticket` and `triage-review` skills. If you change the rubric here, both skills pick it up automatically.

## Levels

| Priority | When to use | Examples |
|----------|-------------|----------|
| **Urgent** | Users blocked, no workaround. Needs immediate attention. | App crash, broken auth, data loss, payment failure |
| **High** | Significant impact on a core flow or key metric. Workaround exists but is painful. | Broken core flow with manual workaround, sync failures hitting multiple customers |
| **Medium** | Meaningful improvement to UX or internal efficiency. Not blocking anyone. | UI inconsistency in a common flow, missing validation, perf issue |
| **Low** | Nice-to-have. Cosmetic, minor friction, or rare edge case. | Copy tweak, minor styling, rare edge case |

When in doubt, bias toward **Medium**. Don't invent urgency.

## Pressure-test the call

- **One-time flow** (setup, onboarding) vs **recurring**? One-time skews lower.
- Actually reported in **production**, or only internal testing? Testing-only skews lower.
- **Manual workaround** available? Yes skews lower.
- Customer-facing visual polish: don't default to Low reflexively. Visible surface area matters. Broken windows affect product perception.
