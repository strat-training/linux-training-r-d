# Templates with sample output for creating learning materials

## Prerequisites
1. set up nexus on local device
2. Claude or OpenCode installed
3. Ultimate Agents on claude

## Replication Steps
### To be completed in Clause Web application
1. Run product-designer to describe the context of the requirements. Use produc-designer.md as reference for the prompt.
2. Use the output from product-designer as input to run solution-architect to create arch-docs. Save the arch-docs in nexus project under /docs directory.

### To be completed in Claude Code
1. Run /scaffold command. Use /knowledge/prompts/dev/scafold-prompt.md as reference to create the curriculum modules. Read the output and implement EPAV. For better result, add document reference or research files in prompts/dev/references
2. To create the training to do list that can be added in a git project, run /dev-tasks-planner.md
3. To create the training rubricks run /create-rubrics. For sample prompt see /knowledge/prompts/dev/grading-rubric-prompt.md
4.
