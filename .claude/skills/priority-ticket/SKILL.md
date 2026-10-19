---
name: priority-ticket
description: Fetch the highest-priority Jira ticket assigned to me in "To Do", summarize its acceptance criteria, enter plan mode to propose an implementation, and move it to "In Progress" once the plan is approved.
disable-model-invocation: true
---

# Priority ticket

Work on the Jira project `FLOW` through the Atlassian MCP server (`atlassian`).

## Steps

1. Resolve the Atlassian cloud ID with `getAccessibleAtlassianResources`.
2. Find the ticket with `searchJiraIssuesUsingJql` using:
   `project = FLOW AND assignee = currentUser() AND status = "To Do" ORDER BY priority DESC, created ASC`
   Take the first result only. If there is none, say so and stop.
3. Read it in full with `getJiraIssue` (description and acceptance criteria).
4. Summarize for the user: key, title, and the acceptance criteria as a short checklist. Do not invent criteria that are not in the ticket.
5. Call `EnterPlanMode`, explore the relevant code, and propose how to implement the ticket. List the files you would touch and flag anything the ticket does not specify (for example, fields the backend validators require).
6. Do not implement anything and do not transition the ticket until the user approves the plan.
7. Once the plan is approved, move the ticket to "In Progress": get the available transitions with `getTransitionsForJiraIssue`, then apply the one named "In Progress" with `transitionJiraIssue`.
8. Report the ticket key and its new status in one line.
