# Reflections

## What has been surprising for you?

Regarding the DoD's I detected that, when you do not introduce some kind of variations to the initial indications 
(in this case, the examples) the copilot (Claude Code, in this occasion) does not apply the "creative mode".
That is, some kind of hint (given through different DoD texts) should be given in the prompt in order to spur the creative 
mode for the IA. This is not happening in the case of the "technical context" sections.

In some of the issued scenarios, such as in Story 1, it does not reflect the eventual cases, and corresponding responses, when 
the data inserted is NULL. Afterward, applying the "poke-holes" pattern, I realized that the possible, more specific scenarios 
could be discovered that way.

It does reflect all the use cases, from the "user" view (role), but I miss the role of the "server": I would like to see 
the use case for the 500 error, and the eventual response.

It correctly marks with "assumed" the implicit affirmations: even when I state that in the prompt, the IA make the corresponding comparison 
with the PRD.md, and tags my own hint as "assumed". Impressive.

It implicitly defines the mandatory fields when creating the calendar event, and lists the rest of optional attributes, 
as seen in Story 2 (the first and second scenarios reflect this fact).

As final minor general observation, it does not mention a possible pagination in the returning data. It mentions limits per page, 
but at the same time it does not talk about pagination.

## What has failed? What did you have to fix?

As mentioned before, the prediction of the DoD's did not properly work. However, I think it could be corrected once I 
introduce in those specific indications some more variability, in number of definitions and text. Otherwise, I saw it 
tends to repeat what I state.

Specifically, the DoD:

- DoD
  [ ] Endpoint implemented following the existing pattern
  [ ] Functional tests covering all GWT acceptance criteria
  [ ] Auto-generated OpenAPI documentation
  [ ] OpenSpec change archived after merge

Is repeated in all the use cases.

## Did the poke-holes pattern find anything you haven't seen?

Definitely, yes. It covered all the case for Story 6. Since initially it described the most generic cases, 
the "poke-holes" pattern discovered and unveiled more fine-grained cases, such as reopening (change status from "archived" to "pending", 
or "completed" to "pending") or editing an already-archived or already-completed task.