# Jenga Features

Jenga is a powerful, open-source project management tool designed to be simple, fast, and intelligent. Here are some of the key features that help you and your team build better, faster.

## The Jenga AI Agent

At the heart of Jenga is a powerful AI co-pilot, built directly into the platform. This agent understands your projects and can help you find information, take action, and automate tasks, all from a simple chat interface.



### Ask Anything, Find Everything

The AI has complete, real-time knowledge of your projects, allowing you to ask questions in plain English instead of searching through menus.

**Find Specific Tickets**
Stop hunting for ticket numbers. Just describe what you're looking for, and the AI will find it.

* **Use Case:** You remember a bug you filed but can't find it.
* **Example:** "What's the status of the login bug I reported last week?"
* **AI:** "I found ticket #42, 'Login button crashes on Safari'. The status is 'In Progress' and it's assigned to John."

**Summarize Tickets**
Get up to speed on a ticket without reading its entire history. The AI can read the description and all the comments to give you a quick summary.

* **Use Case:** You're being added to a complex bug report and need to catch up quickly.
* **Example:** "summarize ticket #42 for me"
* **AI:** "Ticket #42 is a high-priority bug where the login button crashes on Safari. It was reported by Sarah, and John is currently investigating. It seems to be related to a recent security patch."

**Get Project Overviews**
Get a high-level summary of any project, user, or ticket list.

* **Use Case:** You're a project manager preparing for a status update.
* **Example:** "How many tickets are open in the 'Jenga' project?"
* **AI:** "There are currently 12 open tickets in the 'Jenga' project. 3 are 'High Priority' and 9 are 'Medium Priority'."

**Search the Web**
Your agent can also search the public web to help with technical problems, research, or finding information that isn't inside Jenga.

* **Use Case:** You hit a new error in development and need a quick solution.
* **Example:** "search the web for 'java.lang.NullPointerException on startup in Quarkus'"
* **AI:** "I found a few top results. This is often caused by... [AI provides a summary]."

### Your Hands-On Assistant

The agent can also perform tasks for you, acting as a true assistant.

> **Safe By Design**
> The AI will **never** change your data (create, edit, or delete) without your permission. For any action, it will first tell you what it plans to do and ask for your confirmation.

**Create Tickets**
Quickly create new tickets without ever leaving the chat. The AI will ask for the details it needs.

* **Use Case:** A teammate reports a bug in a chat message.
* **Example:** "Can you create a new bug in the 'Frontend' project? The title is 'Navbar is broken on mobile' and the priority is 'High'."
* **AI:** "I'm ready to create a new 'High' priority bug in the 'Frontend' project titled 'Navbar is broken on mobile'. Should I proceed?"

**Generate Acceptance Criteria**
Save time when writing new stories. The AI can read a ticket's description and propose a set of acceptance criteria.

* **Use Case:** You've just written a new user story and want to quickly define the "done" criteria.
* **Example:** "generate acceptance criteria for ticket #51"
* **AI:** "Based on the description 'As a user, I want to reset my password', here are some suggested acceptance criteria:
    * Given I am on the login page
    * When I click 'Forgot Password'
    * Then I see a field to enter my email
    * ...
    Shall I add these to the ticket description?"

**Edit and Update Tickets**
Make quick changes to any ticket using natural language.

* **Use Case:** You want to re-assign a ticket and add a note.
* **Example:** "assign the 'login bug' to me and add a comment that I'm starting work on it now"
* **AI:** "I will assign ticket #42 to you and add the comment 'I'm starting work on it now'. Is this correct?"

**Delete Tickets**
Clean up duplicate or irrelevant tickets safely.

* **Use Case:** You created a test ticket and no longer need it.
* **Example:** "delete the ticket 'test ticket pls ignore'"
* **AI:** "I found ticket #51, 'test ticket pls ignore'. Are you sure you want to delete it?"

### Smart and Context-Aware

**Conversational Memory**
The agent remembers what you're talking about. You don't have to repeat yourself, and you can ask follow-up questions.

* **Use Case:** You ask a question, and then ask a follow-up.
* **Example:**
    * **User:** "Which tickets are assigned to me?"
    * **AI:** "You have three open tickets: #42, #45, and #50."
    * **User:** "Okay, change the priority of the last one to 'Low'."
    * **AI:** "Got it. I will change the priority of ticket #50 to 'Low'. Is that correct?"