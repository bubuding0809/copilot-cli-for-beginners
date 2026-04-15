# Capstone Plugin Template

Fork this directory as the starting point for your capstone artifact.

## Fork it

    cp -r samples/capstone-plugin-template .github/plugins/my-<name>

## Customize

1. Rename `skills/example-skill/` to your skill name.
2. Edit `skills/<your-skill>/SKILL.md`:
   - Rewrite `description` — this is what the AI matches against the user's prompt.
   - Replace the three `REPLACE` steps with your team's actual checklist.
   - Adjust Inputs and Output Format.
3. Edit `plugin.json`:
   - Rename `"name"` to your plugin name.
   - Update `"description"` and `"keywords"`.
   - Update the path in `"skills"` to match your renamed directory.

## Test

In `copilot`, use a natural-language prompt that matches your skill's description.
The skill should auto-load and guide the work.

If it does not trigger, iterate on the skill's `description` — that is almost
always the fix.
