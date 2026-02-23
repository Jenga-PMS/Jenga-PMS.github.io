These Examples are exclusively run on the real **Agent** with Gemini 2.5-flash. 

## Create Ticket

**User**: Can you create a ticket for me for this mail? Create title and description as you see fit. There is a new Bug in the Test1 Project. The Navbar is broken ever since the new IOS update. It wont scroll anymore. The priority is 'High'
**Agent**: I am about to create a new ticket in project 'Test1'.\nTitle: Navbar Broken on iOS after Update\nDescription: The Navbar in the Test1 project is no longer scrolling after the latest iOS update.\nPriority: HIGH\n\nIs this correct?
**User**: yes
**Agent**: The ticket 'Navbar Broken on iOS after Update' has been successfully created in the 'Test1' project with ID Test1-109.

Resulting Ticket:
``` JSON
{
	"id": 995,
	"ticketNumber": 109,
	"title": "Navbar Broken on iOS after Update",
	"description": "The Navbar in the Test1 project is no longer scrolling after the latest iOS update.",
	"projectName": "Test",
	"priority": "HIGH",
	"size": null,
	"status": "OPEN",
	"createDate": "2026-02-19T20:48:56.169202",
	"modifyDate": "2026-02-19T20:48:56.169202",
	"reporter": "admin",
	"assignee": "admin",
	"labels": [],
	"acceptanceCriteria": [],
	"relatedTicketsIds": [],
	"blockingTicketIds": [],
	"blockedTicketIds": []
}
```

## Edit Ticket

**User**: "Can you change the title to Navbar broken on IOS and Android after Update?"
**Agent**: "I am about to update ticket 'Test-109' (Navbar Broken on iOS after Update) to change the title to 'Navbar broken on IOS and Android after Update'.\n\nIs this correct?"
**User**: "yes"
**Agent**: "The title for ticket 109 has been successfully updated to 'Navbar broken on IOS and Android after Update'."

Resulting Ticket:
``` JSON
{
	"id": 995,
	"ticketNumber": 109,
	"title": "Navbar broken on IOS and Android after Update",
	"description": "The Navbar in the Test1 project is no longer scrolling after the latest iOS update.",
	"projectName": "Test",
	"priority": "HIGH",
	"size": null,
	"status": "OPEN",
	"createDate": "2026-02-19T20:48:56.169202",
	"modifyDate": "2026-02-19T21:08:20.167881",
	"reporter": "admin",
	"assignee": "admin",
	"labels": [],
	"acceptanceCriteria": [],
	"relatedTicketsIds": [],
	"blockingTicketIds": [],
	"blockedTicketIds": []
}
```

## Summarize

**User**: Summarize the Ticket with the id 1014 for me
**Agent**: The ticket with ID 1014, titled 'Improve Codequality AI', is CLOSED. It is unassigned. The description mentions: \"Relations: I haven't found any FetchType specifications - is that intentional and if so, why? throw new RuntimeException(\"Ticket not found\"); assignee.equalsIgnoreCase(\"unassigned\") better reverse: \"unassigned\" is a string constant that is certainly not \"null\", so there is one less source of error: \"unassigned\".equalsIgnoreCase(assignee)\".

Referenced Ticket:
``` JSON
{
		"id": 1122,
		"ticketNumber": 113,
		"title": "Improve Codequality AI",
		"description": "Relationen: FetchType-Angaben habe ich nicht entdeckt - ist das Absicht und wenn ja, warum? \nthrow new RuntimeException(\"Ticket not found\");\n\nassignee.equalsIgnoreCase(\"unassigned\") besser umdrehen: \"unassigned\" ist eine String-Konstante, die sicher nicht \"null\" ist, damit ist eine Fehlerquelle weniger vorhanden: \"unassigned\".equalsIgnoreCase(assignee)",
		"projectName": "Test",
		"priority": null,
		"size": null,
		"status": "CLOSED",
		"createDate": "2026-02-20T19:22:08.874723",
		"modifyDate": "2026-02-20T19:22:08.874723",
		"reporter": "admin",
		"assignee": null,
		"labels": [
			"AI",
			"Backend"
		],
		"acceptanceCriteria": [],
		"relatedTicketsIds": [],
		"blockingTicketIds": [],
		"blockedTicketIds": []
	},
```

## Delete

**User**: delete it please
**Agent**: I am about to permanently delete ticket 1014. Should I proceed?
**User**: Yes
**Agent**: Ticket 1014 has been successfully deleted.

## Search for Ticket

**User**: Im looking for a Ticket about System.err and cant find it. Can you find it for me and tell me its contents?
**Agent**: I found a ticket titled \"Replace System.err/System.out with Logger\" (ID: 1026) in the 'Test' project. It is currently in 'CLOSED' status. The description is "System.err due to ratelimit".\n\nIs this the ticket you were looking for?

Referenced Ticket:
``` JSON
	{
		"id": 1026,
		"ticketNumber": 17,
		"title": "Replace System.err/System.out with Logger",
		"description": "System.err due to ratelimit",
		"projectName": "Test",
		"priority": null,
		"size": null,
		"status": "CLOSED",
		"createDate": "2026-02-20T19:08:05.598118",
		"modifyDate": "2026-02-20T19:19:31.849456",
		"reporter": "admin",
		"assignee": null,
		"labels": [
			"Backend",
			"MVP",
			"SonarQube"
		],
		"acceptanceCriteria": [],
		"relatedTicketsIds": [],
		"blockingTicketIds": [],
		"blockedTicketIds": []
	},
```