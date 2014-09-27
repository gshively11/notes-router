# Branches

## Pros
More comfortable for most developers.
More isolation, changes don't need to be merged to master until fully tested. It's rare for a safe merge to master to manifest bugs that didn't exist in the branch alone, as long as it's at HEAD with master.

## Cons
To be effective in an environment with lots of devs, you need a deployment system that can run multiple versions.  Devs and QA will need to share the same servers for testing.
Running multiple versions of an app leads to a more complicated architecture, which requires effort to build, learn, maintain.

# Toggles

## Pros
Removes merge issues with long lived feature branches (e.g. two feature branches merge close to eachother.  constant merges from master won't prevent this)
Simplifies the deployment pipeline and system architecture.  No longer need to run multiple versions simultaneously.


## Cons
Code gets messier with lots of toggles everywhere.
Requires discipline to clean up old toggles (maybe a way to write sunset tests?)
Increased risk and chance of bottlenecks if bad code gets merged to master, can't deploy until it's fixed
Requires developer discipline (intelligent toggle usage, cleaning up old toggles)

## Thoughts

Still create branches?
  * Mechanism for pull requests.  Pull requests increase code quality while sacrificing speed of integration.  A fair trade.
  * Allow you to push code to remote for safe keeping without it getting into master
  * CI server can run tests on branch as you commit (extra safety pre-merge)
  * Risk developers getting comfortable with branch and not merging to master

No branches, just master
  * Same risk?  They just won't push until they are ready.
  * Lose out on PRs
  * Lose out on CI server running tests before merge
  * More risk push happens accidentally

Need to encourage daily merges to master.  That will result in a lot of PRs.  But they should be small, quick to review.

How do we handle changes that can't be hidden by a toggle e.g. dependency upgrades?