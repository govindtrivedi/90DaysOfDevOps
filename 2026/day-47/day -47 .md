Day 47 – Short Answers
Task 1: Pull Request Events

Triggers:

opened → PR created
synchronize → new commits pushed
reopened → PR reopened
closed → PR closed

Useful variables:

${{ github.event.action }}
${{ github.event.pull_request.title }}
${{ github.event.pull_request.user.login }}
${{ github.head_ref }} → source
${{ github.base_ref }} → target

Merged-only step:

if: github.event.pull_request.merged == true
Task 2: PR Validation

Three checks:

File size: fail if any PR file > 1 MB.
Branch name: allow feature/*, fix/*, docs/*.
PR body: warn if description is empty.
Task 3: Scheduled Workflows
30 2 * * 1 → Monday 2:30 AM UTC
0 */6 * * * → every 6 hours
Weekdays at 9 AM IST → 30 3 * * 1-5
First day monthly at midnight → 0 0 1 * *

workflow_dispatch allows manual testing.

Scheduled jobs can be delayed/skipped because GitHub may throttle scheduled workflows, especially in inactive repositories.

Task 4: Path & Branch Filters
paths → run only when specified paths change.
paths-ignore → skip when changes are only in ignored paths.
Branch filters restrict workflows to branches such as main and release/*.
Task 5: workflow_run
Push → Tests workflow → Success → Deploy workflow
                    ↓
                  Failure → No deploy

Use:

if: github.event.workflow_run.conclusion == 'success'
Task 6: repository_dispatch

Used to trigger workflows from external systems, such as monitoring tools, bots, or deployment systems.

Example payload:

environment: production

Workflow reads:

${{ github.event.client_payload.environment }}

Key takeaway: Day 47 covers event-based automation—PR events, schedules, path filters, workflow chaining, and external triggers.
