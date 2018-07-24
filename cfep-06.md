
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

We have almost 400 PRs on staged-recipes right now (July 2018). Many of these are stale and will
never be finished by the original author. Some projects auto-close issues and PRs (e.g.,
Kubernetes and Tensorflow). While auto-closing issues/PRs may be useful to some communities, I do
not think that the conda-forge community is interested in auto-closing issues and PRs. Instead,
we define a workflow (state machine) of PR labels in this cfep that will allow humans to make
informed decisions about when to close a PR.

Once we have figured out the workflow, a follow-on cfep can be the implementation of the bot to
manage the issue lifecycle

## Terminology

* Author: The person who opens the pull request against staged-recipes
* Reviewer: Anyone with commit rights into conda-forge/staged-recipes. As of now (July 2018), these
  are the core and staged-recipes teams

## Specification

```
    Merged < 1 < < Awaiting Review < < <
                          ˅             ^
                          2             ^
                          ˅             ^
            > > > Awaiting Author > 3 > ^
            ^             ˅             ^
            ^             4             ^
            ^             ˅             ^
            ^ < < 5 < < Stale > > 6 > > ^
            ^             ˅             ^
            ^             7             ^
            ^             ˅             ^
            ^ < 8 < Can be closed > 9 > ^
                          ˅
                          10
                          ˅
                        Closed
```

### States and Transitions

We have seven proposed states. All but the "closed" and "merged" states should have an associated
github label with them.

* Not ready for review
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
1. **Reviewer** merges the recipe. The recipe is perfect and passes all CI. No updates are
   needed.
   * -"Awaiting Review"
2. A **Reviewer** reviews the recipe and requests changes.
   * -"Awaiting Review"
   * +"Awaiting Author"
3. The **Author** makes any change to the PR.
   * -"Awaiting Author"
   * +"Awaiting Review"
4. The **Author** makes no changes to the PR for 30 days.
   * +"Stale"
5. A **Reviewer** leaves another review requesting changes.
   * -"Stale"
6. The **Author** makes a change to the PR.
   * -"Stale"
   * -"Awaiting Author"
   * +"Awaiting Review"
7. The **Author** makes no changes for 30 additional days.
   * -"Stale"
   * +"Can be closed"
8. A **Reviewer** leaves another review requesting changes.
   * -"Can be closed"
9. The **Author** updates the PR.
   * -"Can be closed"
   * +"Awaiting Review"
   * -"Awaiting Author"
10. A **Reviewer** closes the PR.
   * -"Can be closed"
   * -"Awaiting Author"

There are also manual steps that can be taken by the **Author** and by a **Reviewer**.

Author:
* Can Close the PR.
* Can Re-open the PR.

Reviewer:
* Can Close the PR
* Can Merge the PR
* Can change labels. How a reviewer manually changing labels impacts any automation needs more
  consideration.
* Can Re-open the PR
* Can revert the PR

## Resolution

N/A. Needs to be discussed

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
