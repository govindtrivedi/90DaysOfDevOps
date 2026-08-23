Day 46 – Short Answers
Task 1: workflow_call
Reusable workflow: A workflow that can be called by other workflows.
workflow_call: Trigger that allows a workflow to be reused.
Reusable workflow vs Action: A reusable workflow can contain multiple jobs; an action is normally used as a step.
Location: .github/workflows/
Task 2–4: Reusable Workflow
Inputs: app_name, environment
Secret: docker_token
Caller passes inputs/secrets using with: and secrets:.
Outputs can pass values such as build_version to the caller.

Flow:

Caller → Reusable Workflow → Build
                         ↓
                   build_version
                         ↓
                    Next Job
Task 5: Composite Action

Location:

.github/actions/setup-and-greet/action.yml

It:

Accepts name and language
Prints greeting/date/OS
Returns greeted: true
Is called with:
uses: ./.github/actions/setup-and-greet
Task 6: Comparison
	Reusable Workflow	Composite Action
Triggered by	workflow_call	uses: in a step
Jobs?	✅ Yes	❌ No
Multiple steps?	✅ Yes	✅ Yes
Location	.github/workflows/	.github/actions/.../action.yml
Secrets directly?	✅ Yes	Via inputs/env
Best for	Reusing complete pipelines	Reusing common steps

Key point: Reusable workflow = reusable jobs/pipeline; Composite Action = reusable steps.
