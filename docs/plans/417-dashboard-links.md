# Plan 417: Dashboard link support in TUI

**Branch:** `dashboard-links`

## Problem

PagerDuty alerts from rhobs_hcp and appsre services include Grafana
dashboard URLs. The `DashboardLink` field is already parsed and populated
on `NormalizedAlert` by the alert normalization engine, but is never
surfaced in the TUI — users must manually find the dashboard URL in the
raw alert data.

## Solution

Mirror the existing SOP link flow:

1. **Key binding:** `d` opens the dashboard link in a browser (alongside
   `s` for SOP).
2. **Extraction:** `getDashboardLink()` reads `normalized.DashboardLink`,
   falls back to raw `"dashboard"` detail field.
3. **View:** Alert tab template shows `* Dashboard:` line with a clickable
   link (or `_none_`).
4. **Handlers:** `openDashboardMsg` in tui.go, key dispatch in both queue
   and incident detail focus modes in msgHandlers.go.

## Files changed

| File | Change |
|------|--------|
| `pkg/tui/keymap.go` | `Dashboard` key binding on `d` |
| `pkg/tui/commands.go` | `getDashboardLink()`, `openDashboardMsg` type |
| `pkg/tui/views.go` | `DashboardLink` on `alertSummary`, template line |
| `pkg/tui/tui.go` | `openDashboardMsg` handler |
| `pkg/tui/msgHandlers.go` | `d` key dispatch in queue + incident focus |
| `pkg/tui/browser_sop_test.go` | Dashboard handler tests |
| `pkg/tui/commands_test.go` | `getDashboardLink` tests |

## Testing

- Unit tests for `getDashboardLink` (has link, no link, empty, nil, multiple alerts)
- Handler tests for `openDashboardMsg` (nil incident, no link, happy path)
- All existing tests pass unchanged
