
<table>
<tr><td> cfep-06 </td><td> staged-recipes code review lifecycle </td>
<tr><td> Status </td><td> **Draft** | Proposed | Accepted | Rejected | Deferred | Implemented </td></tr>
<tr><td> Author(s) </td><td> Eric Dill &lt;edill@anaconda.com&gt;</td></tr>
<tr><td> Created </td><td> Jul 24, 2018</td></tr>
<tr><td> Updated </td><td> Jul 24, 2018</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA if not availble </td></tr>
</table>

## Motivation

On today's dev meeting (2018-07-24) we talked about automatically closing pull-requests (PRs) on
staged-recipes to keep the number of open PRs manageable for the @conda-forge/core and
@conda-forge/staged-recipes teams. Right now we have almost 400 open PRs. We are somewhere
between "many" and "most" of those never being merged. I think it is time to take action and
clean up our stale PRs on staged-recipes.

Some on the call today were positive about having an automated system for closing stale PRs while
others were not. I think it is worth specifically calling out what kind of an automated-close
policy is undesired. There is one community, specifically, that has a very aggressive system of
auto-closing issues after 14 days if they have not been updated by *anyone*. This is undesirable
because it means that an issue that has not been responded to by anyone will get automatically
closed in two weeks without the issue being resolved. We would like to avoid these situations in
the conda-forge community but we recognize that we need a better system for managing our
pull-request backlog against staged-recipes.

Our compromise on the call was that we should have an automated system for *labeling* PRs and let
humans ultimately decide *when* to close the PR. The conversation was sufficiently complete that
we thought a CFEP was appropriate so that all of @conda-forge/core and
@conda-forge/staged-recipes had an opportunity to weigh in before we implement anything. The
thinking is that by defining which **labels** we should have and what triggers a **transition**
between labels we can clearly signify (1) whether the **Reviewers** or the **Authors** are
supposed to take action and (2) whether or not we have given the **Author** sufficient time to
address the review that we can close the PR as it has become stale and inactive.

The desired outcome of this CFEP is an explicit definition of what a "stale" PR is such that we
can all feel okay with closing someone elses hard work. We will produce this explicit definition
of what "stale" means by codifying the code-review process into **labels** and **transitions**
between these labels. After this has been defined and agreed up on we can implement a bot based
on this CFEP.

## Terminology

* **Author**: The person who opens the pull request against staged-recipes
* **Reviewer**: Anyone with commit rights into conda-forge/staged-recipes. As of now (July 2018), these
  are the core and staged-recipes teams
* **labels**: Nodes in the state-machine. Not all nodes will have a label (merged and closed, for
  example)
* **transition**: The specific action that triggers moving between nodes in the state machine

## Specification

```
(Open new PR) > 0 > [Awaiting Review] < < <
                            ˅             ^
                            1             ^
                            ˅             ^
            > > > [Awaiting Author] > 2 > ^
            ^               ˅             ^
            ^               3             ^
            ^               ˅             ^
            ^ < < 4 < < [Stale] > > 5 > > ^
            ^               ˅             ^
            ^               6             ^
            ^               ˅             ^
            ^ < 7 < [Can be closed] > 8 > ^
                           ˅
                           9
                           ˅
                        [Closed]
```

### States and Transitions

We have six proposed states. All but the "closed" and "merged" states should have an associated
github label with them. Merged can be reached from any state other than "Closed" so it is not
worth putting on to this state machine diagram.

* Awaiting Review
* Awaiting Author
* Stale
* Can be closed
* Closed
* Merged

We have 10 proposed transitions. The syntax here is `+` or `-` followed by a label. This indicates
that a specific label is added or removed.

0. **Author** submits pull-request to staged-recipes
    * +"Awaiting Review"
1. A **Reviewer** reviews the recipe and requests changes.
   * -"Awaiting Review"
   * +"Awaiting Author"
2. The **Author** makes any change to the PR.
   * -"Awaiting Author"
   * +"Awaiting Review"
3. The **Author** makes no changes to the PR for 30 days.
   * +"Stale"
4. A **Reviewer** leaves another review requesting changes.
   * -"Stale"
5. The **Author** makes a change to the PR.
   * -"Stale"
   * -"Awaiting Author"
   * +"Awaiting Review"
6. The **Author** makes no changes for 30 additional days.
   * -"Stale"
   * +"Can be closed"
7. A **Reviewer** leaves another review requesting changes.
   * -"Can be closed"
8. The **Author** updates the PR.
   * -"Can be closed"
   * +"Awaiting Review"
   * -"Awaiting Author"
9. A **Reviewer** closes the PR.
   * -"Can be closed"
   * -"Awaiting Author"

There are also manual steps that can be taken by the **Author** and by a **Reviewer**. It is not
immediately clear what impact manually changing labels would have on any label automation.

Author:
* Can Close the PR.
* Can Re-open the PR.

Reviewer:
* Can Close the PR
* Can Merge the PR
* Can change labels.
* Can Re-open the PR
* Can revert the PR

## Specific questions to be answered

* How long should we wait before declaring an issue "stale"?
    * 30 days
* How long should we wait before declaring an issue "Ready to be closed"?
    * 30 days

## Resolution

N/A. Needs to be discussed

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
