# Modular Submission Form
Because Google Forms cannot dynamically add new pages while a user is typing, you must pre-build a fixed maximum number of sections in advance and use conditional branching to decide which sections should be shown to a user.

## Overview
This document explains how to create a Google Form that collects structured data for processing by the modular Google Apps Script. Responses are stored in Google Sheets, where the script transforms them into a format suitable for uploading to Omeka S through the CSV Import module. While the form's questions and sections will vary depending on the project, the underlying design principles remain the same.

## Form Structure
When adapting this form pattern to a unique project, you translate your data hierarchy into a sequential series of form pages. A standard form consists of:

- **Primary Recorded:** Captures the main subject of the submission and is the first section of the form.

- **Secondary Records:** Capture secondary records underneath the primary record, potentially spanning multiple nested layers of relationships. Because Google Forms cannot dynamically generate pages on demand, these repeated blocks must be pre-built in a fixed sequence up to a defined maximum capacity.

- **Dedicated Routing and Transition Sections:** Short, focused sections placed between record blocks whose sole purpose is to handle navigation. These sections direct participants through branching logic to determine whether to add another record at the current level or move forward to a completely separate parent record.

## Conditional Branching Logic
To prevent respondents from navigating through empty or irrelevant pages, use Google Forms conditional navigation ("Go to section based on answer"). After completing any record section, a dedicated routing question directs the user based on where they are in the tree:

- **Move One Level Down:** If the current record can contain child or sub-records, the user is asked if they want to add a nested item under the record they just completed. Choosing **Yes** routes them directly to the first pre-built section of that lower tier.

- **Create Another Record at the Same Level:** If the user answers **No** to the question above and does not want to move down, or has finished adding lower-level items, they are asked if they want to add another entry at the current level. Choosing **Yes** routes them to the next available section at that same level.

- **Move One Level Up:** If the user answers **No** to the question above and has finished adding items at the current level, the logic routes them back up to the transition checkpoint of the parent record one level above. This allows them to either add another parent-level record or continue ascending.

- **Submit the Form:** When the user finishes entering items at the highest level and chooses not to add any further entries, the routing logic bypasses all remaining unused section blocks and directs them straight to the final submission page.


## Adapting This Pattern to Unique Projects

When building or modifying this architecture for a new project:

- Pre-allocate fixed limits to the maximum number of repeated records and nested layers your project will support and create that exact number of section blocks in the form.

- Use dedicated transition sections to manage complex navigation paths clearly, ensuring respondents never get trapped in dead-end pages or forced through irrelevant sections.

- Maintain consistent layouts by ensuring that every repeated record section uses the exact same question order and field types. This consistency allows scripts to reliably read column offsets and block sizes across the data, regardless of whether a respondent filled out every block or skipped certain ones.

