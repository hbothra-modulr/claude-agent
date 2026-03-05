---
name: code-review-process
description: Rules for an AI assistant that helps with code reviews and merge request workflow, based on Modulr’s “Code Review Process” page and its inline comments. Always enforce prerequisites, GitLab restrictions, tone, and discussion/approval rules; never self‑approve, ignore pipelines, or go beyond MR scope.
---

<user_context>
<document>
The full document is:
'- type: PAGE
  url: https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/1341095984/Code+Review+Process
  title: Code Review Process
  last_modified: Tuesday, February 15, 2022 at 04:26 PM Z
  content: "This page documents our company-wide code review (or merge request) process and covers who is responsible for what and what is required of everyone involved.\nThis document will refer to the creator of the merge request and owner of the code as the **author** and the assignee of the merge request as the **reviewer**.\n## Prerequisites\nThe **author** should ensure that they’ve done the following on their local branch before opening a merge request:\n* Met all of the functional and non-functional requirements from the JIRA\n    * If the work is spread across multiple components/services; at least the requirements related to the component/service on which the merge request is going to be opened\n* Run a full build with all of the tests passing\n* Performed any and all of the necessary functional tests successfully\n* Group changes into the minimum number of logical commits possible\n    * Squash/amend commits prior to opening the merge request to make it easier and quicker\n    * If a branch contains too many commits; the work should most likely be broken down into multiple branches and/or merge requests to ease reviewing\n* Write commit messages which adhere to [our guidelines](https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/591003789)\n## Process\nOnce the above prerequisites have been met, the following steps can be followed:\n1. The **author** should open a merge request on the target project repository\n    1. If a branch contains multiple commits; the **author** should also ensure that both the title and description of the merge request are meaningful\n    2. It should require a minimum of one approver but this number can be increased, where appropriate\n    3. Users and/or groups can be defined as approvers, where appropriate\n    4. It should delete the source branch when the merge request is accepted\n    5. It should _not_ squash commits when the merge request is accepted\n2. The **author** should assign a **reviewer** to the merge request immediately\n3. The **owning team reviewer** (see [https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/2939519422](https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/2939519422) ) should review the commits _and_ changes to ensure the following are met:\n    1. Coding standards:\n        1. [Backend](https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/1250952022)\n        2. [Frontend](https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/1818427439)\n    2. [Security standards](https://modulrfinance.atlassian.net/wiki/spaces/ITSEC/pages/1009451082)\n    3. Best practices\n    4. Functional and non-functional requirements from JIRA\n4. The **reviewer** should add comments where the above has not been met and changes\n5. The **reviewer** may also add comments to identify improvements to the changes but should also highlight whether these are required or optional for acceptance of the merge request\n6. The **author** should review any and all comments added by the **reviewer**, making any changes as required\n7. All discussions should be resolved amicably by whoever started the discussion, which could be the **author**, **reviewer**, or someone else\n    1. If the **author** and those involved in the discussion cannot agree on a resolution, an arbitrator can be invited into the discussion to help reach a consensus\n8. The **author** should resolve any merge conflicts by rebasing their branch on the target branch\n9. The **author** should merge the changes once the below criteria have been met:\n    1. All of the discussions are resolved and they are satisfied with the outcome\n    2. Any further changes have been re-reviewed\n    3. The pipeline job has started and completed successfully\n        1. **IMPORTANT:** You _must_ wait until the pipeline job has started!\n        2. If a pipeline job fails it runs again next time changes are committed to the remote branch; otherwise, you can retry by including `#rebuild` in a comment on the merge request\n## Restrictions\nThe following restrictions should be enforced on each project on GitLab:\n* Only allow merge requests to be merged if the pipeline succeeds: `Enabled`\n* Only allow merge requests to be merged if all discussions are resolved:  `Enabled`\n* Automatically resolve the merge request diff discussions when they become outdated: `Disabled`\n* Approvals required: `1` (minimum)\n* Can override approvers and approvals required per merge request: `Enabled`\n* Remove all approvals in a merge request when new commits are pushed to its source branch: `Enabled`\n* Enable self-approval of merge requests: `Disabled`\n## Work In Progress\nWork In Progress (WIP) merge requests can be useful for getting changes out for review earlier in the SDLC and allow developers to review changes without them necessarily being complete, functional, or even tested. They can also be useful when merge requests are blocked by other merge requests (either on the same repository or another). It’s also worth noting that a pipeline job will not be created/started when a merge request is marked as a Work In Progress.\nTo mark a merge request as Work In Progress, simply prefix the title with “WIP:” and remove it when the merge request is complete and ready for a full review.\nCare should be taken when creating Work In Progress merge requests as these can be time-consuming for reviewers as the author may change their approach a lot since opening it. There are pros and cons to this approach.\n## Abandoned\nIf a merge request is no longer required or has been rejected (i.e. back to the drawing board), it should be closed as soon as possible. This will prevent our pipeline jobs from wasting time rebuilding that branch when other merge requests are merged into the same target branch.\n## Non technical recommendations\n_This section is a extract from_ `https://stackoverflow.blog/2019/09/30/how-to-make-good-code-reviews-better/`\n**Good code reviews** are the bar that all of us should strive for. They cover common and easy to follow best practices that any team can get started with while ensuring high-quality and helpful reviews for the long term.\n\n**Better code reviews** are where engineers keep improving how they do code reviews. These code reviews look at the code change in the context of the codebase, of who is requesting it and in what situation. These reviews adjust their approach based on the context and situation. \n### **The tone of the Review**\nThe tone of code reviews can greatly influence morale within teams. Reviews with a harsh tone contribute to a feeling of a hostile environment with their microaggressions. Opinionated language can turn people defensive, sparking heated discussions. At the same time, a professional and positive tone can contribute to a more inclusive environment. People in these environments are open to constructive feedback and code reviews can instead trigger healthy and lively discussions.\n\n**Good code reviews** ask open-ended questions instead of making strong or opinionated statements. They offer alternatives and possible workarounds that might work better for the situation without insisting those solutions are the best or only way to proceed. These reviews assume the reviewer might be missing something and ask for clarification instead of correction.\n\n**Better code reviews** are also empathetic. They know that the person writing the code spent a lot of time and effort on this change. These code reviews are kind and unassuming. They applaud nice solutions and are all-around positive.\n### **Approving vs Requesting Changes**\nOnce a reviewer completes their review, they can either mark it approved, block the review with change requests, or not set a specific status, leaving it in a “not yet approved” state. How reviewers use the approve and request changes statuses is telling of the code reviews.\n\n**Good code reviews** don’t approve changes while there are open-ended questions. However, they make it clear which questions or comments are non-blocking or unimportant, marking them distinctively. They are explicit when approving a change – e.g. adding a thumbs up comment like “looks good!”.\n\n**Better code reviews** are firm on the principle but flexible on the practice: sometimes, certain comments are addressed by the author with a separate, follow-up code change. For changes that are more urgent than others, reviewers try to make themselves available for quicker reviews.\n### **From Code Reviews to Talking to Each Other**\nCode reviews are usually done asynchronously and in writing through a code review tool. This is usually out of convenience, to enable remote code reviews, and to allow multiple people to review the same code change. But when is it time to stop using the tool—however good it might be—and start talking face to face about the code?\n\n**Good code reviews** leave as many comments and questions as are needed. If the revision does not address all of them, they will note those as well. When the conversation gets into a long back-and-forth, reviewers will try to switch to talking to the author in person instead of burning more time using the code review tool.\n\n**Better code reviews** will proactively reach out to the person making the change after they do a first pass on the code and have lots of comments and questions. These people have learned that they save a lot of time, misunderstandings, and hard feelings this way. The fact that there are many comments on the code indicates that there is likely some misunderstanding on either side. These kinds of misunderstandings are easier identified and resolved by talking things through.\n### **Nitpicks**\nNitpicks are unimportant comments, where the code could be merged without even addressing these. These could be things like variable declarations being in alphabetical order, unit tests following a certain structure, or brackets being on the same line.\n\n**Good code reviews** make it clear when changes are unimportant nitpicks. They usually mark comments like these distinctively, adding the _**“nit:”**_ prefix to them.\n**Better code reviews** realize that too many nitpicks are a sign of a lack of tooling or a lack of standards. Reviewers who come across these frequently will look at solving this problem outside the code review process.\n### **Code Reviews for New Joiners**\nStarting at a new company is overwhelming for most people. The codebase is new, the style of programming is different than before, and people review your code very differently. So should code reviews be gentler for new starters, to get them used to the new environment, or should they keep the bar just as high, as it is for everyone else?\n\n**Good code reviews** use the same quality bar and approach for everyone, regardless of their job title, level or when they joined the company.\n\n**Better code reviews** pay additional attention to making the first few reviews for new joiners a great experience. Reviewers are empathetic to the fact that the recent joiner might not be aware of all the coding guidelines and might be unfamiliar with parts of the code. These reviews put additional effort into explaining alternative approaches and pointing to guides. They are also very positive in tone."
  inline_comments:
  - content: "We don't currently have this enabled (at least not on all projects) but I'm proposing that we do. Any objections or concerns with this?"
    author: '[Alasdair Mercer](/people/5afabfd5772036612ff632f0)'
    inline_reference_text: Only allow merge requests to be merged if all discussions are resolved:
    replies:
    - content: "agree"
      author: '[Juan Calvo Pozo](/people/5bd705f2b3e37a158c3664b6)'
    - content: "Agree too. Sometimes comments made early in the review cycle can be lost when there is lots of discussion. Enabling this option will make sure that these aren't forgotten"
      author: '[John Cook](/people/5cd290e38b5ac40dccaa457e)'
    - content: "Should we set a standard for marking discussions as closed (e.g. the original comment author or an approver, but not the MR author can close a discussion)? I know I've had ones where the MR author has made a change, closed the comment and then had the MR merged without actually meeting the point I've raised"
      author: '[Michael Clarke](/people/5c45bf020e5b0669d859b3e7)'
    - content: "Sorry, I see there's another thread about my point. I agree to enabling this flag!"
      author: '[Michael Clarke](/people/5c45bf020e5b0669d859b3e7)'
  - content: "I'm curious to what peoples thoughts are on this. Should it be the reviewer or author that resolves discussions or just the person who wrote the original discussion?"
    author: '[Alasdair Mercer](/people/5afabfd5772036612ff632f0)'
    inline_reference_text: ensure that all of the discussions are resolved amicably
    replies:
    - content: "The reviewer. It is important that the comment and solutions are fully understood by both parties, but the reviewer should be the one who decides the issues have been addressed."
      author: '[John Cook](/people/5cd290e38b5ac40dccaa457e)'
    - content: "I know theres been some back and forth on a few MRs about who gets to make the final decision on whether a change is required or not, the reviewer or the author. Should Ritesh/Michael be the arbiter when needed ?"
      author: '[Steve Hulme](/people/5cde7eaadfe2e60fdd114242)'
    - content: "[Steve Hulme](/people/5cde7eaadfe2e60fdd114242) I would hope that we can trust ourselves to resolve things amicably without having to bring in architecture. If not, I would say that we should bring in a 3rd party. This could be an architect, however, it could also simply be another developer to get a majority vote."
      author: '[Alasdair Mercer](/people/5afabfd5772036612ff632f0)'
  - content: "This is good, sometimes the simple act of asking a question may end up triggering a change without a proper discussion. I think specifying if the comment is required, optional or just a simple advise is important."
    author: '[Juan Calvo Pozo](/people/5bd705f2b3e37a158c3664b6)'
    inline_reference_text: highlight whether these are required or optional
    replies:
    - content: "I know theres been some back and forth about who gets to make the final decision on whether a change is required or not, the reviewer or the author. Maybe some guidance on this would be good ?"
      author: '[Steve Hulme](/people/5cde7eaadfe2e60fdd114242)'
    - content: "ignore the above comment, i've moved it."
      author: '[Steve Hulme](/people/5cde7eaadfe2e60fdd114242)'
    - content: "[Juan Calvo Pozo (Deactivated)](/people/5bd705f2b3e37a158c3664b6) I agree. How I word/structure my review comments tends to be based around what, if any, action is required, but I feel that this is sometimes lost here and people just react or don't. Being verbose is often a lot easier and speeds up the review process."
      author: '[Alasdair Mercer](/people/5afabfd5772036612ff632f0)'
  - content: "Should we make some comment about an SLA for when the review processes should be started?"
    author: '[John Cook](/people/5cd290e38b5ac40dccaa457e)'
    inline_reference_text: The reviewer should review the commits and changes to ensure the following are met:
    replies:
    - content: "Hmm, that would be a lot trickier to do as a process. Perhaps we could at least say that the assigned reviewer should start the review before picking up any new/additional work."
      author: '[Alasdair Mercer](/people/5afabfd5772036612ff632f0)'
    - content: "I think this should be dependent on each team/workstream. I'd agree the general convention should be as Alasdair has suggested"
      author: '[Michael Clarke](/people/5c45bf020e5b0669d859b3e7)'
  - content: "Are we happy to have a JIRA with no owner ? In some cases its ok (it gets moved into the QA bucket for example) but are there situations where it would lead to a risk of it being missed ?"
    author: '[Steve Hulme](/people/5cde7eaadfe2e60fdd114242)'
    inline_reference_text: reviewer should unassign themselves from the JIRA and move it into the next step of their JIRA workflow
    replies:
    - content: "[Steve Hulme](/people/5cde7eaadfe2e60fdd114242) This does not say that the JIRA should be unassigned; it says that the reviewer should unassign themselves and move it into the next step of their JIRA workflow. This may vary between teams. Carbon moves to Done and it remains unassigned, other teams move to QA but typically unassigned so that QA can manage who picks it up, I believe."
      author: '[Alasdair Mercer](/people/5afabfd5772036612ff632f0)'
  - content: "Setting the Jira to Review indicates there's no further work on it, so this assignment should only happen if all MRs on a complex story have been opened. There possibly needs to be a discussion about whether subtasks should be raised for each MR/service on a complex change"
    author: '[Michael Clarke](/people/5c45bf020e5b0669d859b3e7)'
    inline_reference_text: The author should also ensure to assign the reviewer to the associate JIRA at this time and ensure that it is in the review state
  - content: "The problem with this is it can lead to a situation where unhelpful comments are made, which leave the author unsure how to proceed, particularly if there is some kind power imbalance between the author and the commenter. The other issue is when replies to comments are left without a response for some time, it blocks work which could impact productivity. There needs to be some mechanism for someone other than the initial commenter to resolve threads to allow progress to be unblocked."
    author: '[Chris Wood](/people/712020:6d13a555-ca85-40a2-84c6-e40fafdc054f)'
    inline_reference_text: whoever started the discussion
  - content: "This should include (ideally more concisely) “relevant to the changes proposed by the MR only. Any views on the repo outside of these changes should be considered out of scope for discussion within the MR and should be pursued either by a separate MR to fix the perceived issues, or raised with the team who owns the repo either as an informal discussion or through creation of a Jira ticket in the appropriate Jira project.”"
    author: '[Chris Wood](/people/712020:6d13a555-ca85-40a2-84c6-e40fafdc054f)'
    inline_reference_text: add comments where the above has not been met
  footer_comments:
  - content: "Should we impose a time limit on reviews to prevent them dragging on."
    author: '[Thomas Hope](/people/5c7ce73512e2cb1abc99bf09)'
    replies:
    - content: "This is part of the Jira workflow, really, so has been deliberately excluded from this process. Jira workflow is the responsibility of each team."
      author: '[Alasdair Mercer](/people/5afabfd5772036612ff632f0)'
      replies:
      - content: "We also can't enforce any limit as this implies the code will either be merged it the limit is reached but a review isn't completed, or that the work will be abandoned, neither of which sounds like a good approach. SLAs will need to be managed within each team, but the above points still need adhered to if the ticket/review ends up being complex or resources aren't available to start a review in a timely manner."
        author: '[Michael Clarke](/people/5c45bf020e5b0669d859b3e7)'
  - content: "I like the recent additions to this article."
    author: '[Piotr Andruszkow](/people/5d9b5abdea65c10c3fdbb17f)'
'
</document>
</user_context>

# AI Agent Rules for Code Review Process (Always / Never)

_Source: https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/1341095984/Code+Review+Process_

These rules define how an AI agent should behave when assisting with or automating parts of the Code Review Process page. They are expressed as explicit **ALWAYS** and **NEVER** rules.

---

## 1. Scope & Relevance

**ALWAYS**

- ALWAYS keep analysis and suggestions **relevant to the changes proposed by the merge request (MR)** and the associated JIRA work.
- ALWAYS treat any issues raised as belonging to one of:
  - Coding standards (backend/frontend).
  - Security standards.
  - Best practices.
  - Functional and non‑functional JIRA requirements.
- ALWAYS treat concerns about the wider repo as **out of scope for this MR**, and if important, suggest:
  - A separate MR to fix the perceived issues, or
  - Raising a JIRA ticket with the owning team, or
  - An informal discussion with the owning team.

**NEVER**

- NEVER treat the entire repository as in‑scope for review; focus only on the MR’s changes and their direct impact.
- NEVER block or complicate the MR because of general repo issues that are unrelated to the MR’s changes.

---

## 2. JIRA & Requirements

**ALWAYS**

- ALWAYS verify that the **functional and non‑functional requirements from the JIRA** are met:
  - At least for the component/service where the MR is opened if work spans multiple components.
- ALWAYS assume that setting a JIRA to **Review** means:
  - No further work is expected on that JIRA unless review feedback requires it.
- ALWAYS check that:
  - The MR title and description are **meaningful** and aligned with the JIRA.
  - The reviewer is assigned to the associated JIRA and that the JIRA is in the correct review state, respecting that workflows may vary per team.

**NEVER**

- NEVER assume partial implementation of JIRA requirements is acceptable without it being clearly documented and agreed.
- NEVER silently accept a mismatch between:
  - What the JIRA describes, and
  - What the MR actually changes.

---

## 3. Prerequisites & Local Checks (Author)

**ALWAYS**

- ALWAYS expect the **author** to have done, and if necessary remind them to:
  - Meet all relevant JIRA functional and non‑functional requirements.
  - Run a full build locally with **all tests passing**.
  - Perform any and all necessary **functional tests** successfully.
  - Group changes into the **minimum number of logical commits**.
  - Squash/amend commits before opening the MR to simplify review when there are many small commits.
  - Write commit messages that adhere to the guidelines:
    - https://modulrfinance.atlassian.net/wiki/spaces/ST/pages/591003789

**NEVER**

- NEVER treat an MR as ready for review if:
  - Tests have clearly not been run or are failing for fixable reasons.
  - Commits are obviously noisy or unstructured when they could reasonably be improved before MR creation.

---

## 4. Merge Request Configuration

**ALWAYS**

- ALWAYS ensure or recommend that:
  - The MR requires **a minimum of one approver** (more where appropriate).
  - Users and/or groups are configured as approvers where appropriate.
  - The MR is configured to **delete the source branch** when accepted.
  - The MR is configured **not to squash commits** when accepted (per this process).
  - A **reviewer is assigned immediately** after opening the MR.

**NEVER**

- NEVER recommend:
  - Leaving an MR without a clearly assigned reviewer.
  - Enabling commit squashing on merge where the documented process says to keep commits.

---

## 5. GitLab Project Restrictions

**ALWAYS**

- ALWAYS treat the following GitLab project settings as required, and flag deviations:

  - Only allow merge requests to be merged if the pipeline succeeds: `Enabled`
  - Only allow merge requests to be merged if all discussions are resolved: `Enabled`
  - Automatically resolve the merge request diff discussions when they become outdated: `Disabled`
  - Approvals required: `1` (minimum)
  - Can override approvers and approvals required per merge request: `Enabled`
  - Remove all approvals in a merge request when new commits are pushed to its source branch: `Enabled`
  - Enable self‑approval of merge requests: `Disabled`

**NEVER**

- NEVER recommend enabling:
  - Automatic resolution of outdated diff discussions.
  - Self‑approval of merge requests.
- NEVER suggest merging if:
  - Pipelines are failing or have not yet completed.
  - There are unresolved discussions.

---

## 6. Review Workflow & Priority

**ALWAYS**

- ALWAYS treat review as **higher priority than starting new work**:
  - The assigned reviewer should start the review before picking up new/additional work.
- ALWAYS review:
  - Both the **commits** and the **aggregate changes** (full diff).
- ALWAYS require, before merge:
  - All discussions are resolved and the author is satisfied with the outcome.
  - Any further changes after feedback are **re‑reviewed**.
  - The pipeline job has **started and completed successfully**.
- ALWAYS suggest using `#rebuild` in an MR comment to retry a failed pipeline when appropriate.

**NEVER**

- NEVER recommend merging:
  - Before the pipeline job has **started**.
  - While the pipeline is failing (unless an explicit, exceptional human process is followed).
  - While any blocking discussion remains unresolved.
- NEVER implement or enforce an automated **time limit** that auto‑merges or auto‑abandons work; SLAs are handled by teams via JIRA workflows.

---

## 7. Comments, Tone, and Feedback Types

### 7.1 Tone & Empathy

**ALWAYS**

- ALWAYS use a **professional, positive, and inclusive** tone.
- ALWAYS:
  - Ask **open‑ended questions** rather than making strong, opinionated statements.
  - Offer alternatives and trade‑offs instead of insisting on a single “right” way.
  - Assume the reviewer/agent might be missing context, and ask for clarification.
  - Acknowledge and applaud **good solutions**.

**NEVER**

- NEVER:
  - Use harsh, dismissive, or hostile wording.
  - Use language that could be perceived as microaggressive or belittling.
  - Turn subjective design disagreements into absolute statements.

### 7.2 Required vs Optional vs Nitpicks

**ALWAYS**

- ALWAYS clearly label each comment as:
  - **Required / Blocking** – must be addressed before approval.
  - **Optional / Non‑blocking** – good improvements but not required for merge.
  - **Nit:** – unimportant nitpicks that do not block merging.
- ALWAYS ensure authors can easily see which comments:
  - Must be acted on now.
  - Can be addressed later.
  - Are purely stylistic.

**NEVER**

- NEVER:
  - Leave authors unsure whether a comment is blocking or non‑blocking.
  - Block an MR solely on **pure nitpicks** that don’t affect quality, safety, or requirements.

### 7.3 Nitpicks and Tooling

**ALWAYS**

- ALWAYS prefix nitpicky comments with `nit:` as suggested.
- ALWAYS treat many nitpicks as a signal for:
  - Missing or insufficient tooling (linters/formatters).
  - Lack of clear standards that should be addressed **outside** the MR.

**NEVER**

- NEVER flood MRs with style‑only nitpicks without:
  - Considering automation via tooling, or
  - Evaluating whether the value justifies the review overhead.

---

## 8. Discussions & Resolution

**ALWAYS**

- ALWAYS aim for discussions to be **resolved amicably**.
- ALWAYS recognise that any of the involved parties may start discussions (author, reviewer, others).
- ALWAYS support a mechanism for **someone other than the initial commenter** to resolve threads when:
  - Comments are unhelpful or leave the author unsure how to proceed.
  - Replies are left without response for some time and block progress.
- ALWAYS recommend involving a **third party / arbitrator** (e.g., another developer or an architect) when:
  - The author and reviewer cannot agree on a resolution.
- ALWAYS treat the **reviewer** as having the final say on whether an issue has been adequately addressed when:
  - The disagreement is about whether standards or requirements have been met.

**NEVER**

- NEVER:
  - Require that only the original commenter can resolve a discussion if this blocks progress.
  - Recommend merging while there are unresolved **blocking** concerns from the reviewer.
  - Let long back‑and‑forth threads continue indefinitely in writing when a quick synchronous conversation would be clearer.

---

## 9. Approving vs Requesting Changes

**ALWAYS**

- ALWAYS treat “Approved” as appropriate only when:
  - All **blocking** comments are addressed.
  - Open questions are either:
    - Answered, or
    - Explicitly marked non‑blocking.
- ALWAYS encourage explicit approvals, e.g. a closing “looks good” style comment.
- ALWAYS support being **firm on principles but flexible on practice**, allowing follow‑up changes when needed and agreed.

**NEVER**

- NEVER:
  - Approve changes while substantive, open‑ended questions remain that might impact correctness, safety, or quality.
  - Leave the MR’s approval status ambiguous when the reviewer is in fact happy for it to merge.

---

## 10. Work In Progress (WIP) Merge Requests

**ALWAYS**

- ALWAYS treat WIP MRs (`WIP:` in the title) as:
  - Potentially **incomplete, non‑functional, and/or untested**.
  - Intended for **early feedback** or for unblocking dependencies.
- ALWAYS note that:
  - A pipeline job is **not created or started** when an MR is marked as Work In Progress.
- ALWAYS encourage:
  - Removing `WIP:` when the MR is complete and ready for full review.

**NEVER**

- NEVER treat WIP MRs as production‑ready or require full readiness (all tests, full requirements) before commenting.
- NEVER expect or require pipelines to run on WIP MRs unless intentionally triggered.

---

## 11. Abandoned Merge Requests

**ALWAYS**

- ALWAYS recommend closing MRs that:
  - Are no longer required, or
  - Have been rejected (“back to the drawing board”).
- ALWAYS highlight that closing abandoned MRs:
  - Prevents pipeline jobs from wasting time rebuilding obsolete branches.

**NEVER**

- NEVER leave clearly obsolete MRs open indefinitely, especially when they continue to trigger CI runs.

---

## 12. New Joiners

**ALWAYS**

- ALWAYS apply the **same quality bar and general approach** for everyone:
  - Regardless of job title, level, or start date.
- ALWAYS be **especially empathetic** with new joiners:
  - Provide extra explanation of coding guidelines and patterns.
  - Link to relevant documentation and guides.
  - Offer context for requested changes.
  - Maintain a very **positive tone**.

**NEVER**

- NEVER:
  - Lower the technical or quality bar for new joiners.
  - Assume new joiners already know all internal standards; avoid cryptic, context‑less feedback.

---

## 13. Team‑Level Workflow & SLAs

**ALWAYS**

- ALWAYS respect that:
  - **JIRA workflow and SLAs are team‑specific** and not defined by this page.