# RP Onboarding - Swagger review workflow 

This document is intended for swagger reviewers to undestand the different flows that are possible for swagger reviews and how each flow looks like.

There are four flows that can be followed when an RP updates or creates a swagger specification. These flows are described below.
## New RP/New File ##
This flow is followed when a new team wants to onboard to ARM and hence create a new Resource providers. The below steps are specific only to the swagger creation process. It does not include some other steps needed for ARM onboarding. 

1.	Contact ARM team to review RP design and get general feedback
2.	Submit PR to private rest api specs repo
3.	CI is triggered 
    - CI adds label (WaitForARMFeedback) since this is a new RP/new file
    - This will help avoid re-review from the SDK side if there are major changes proposed by ARM during initial review
4.	All owners are notified 
5.	If label=WaitForARMFeedback, ARM provides manual feedback (SDK team does not start manual review as the APIs might change or get removed)
6.	RP updates PR to address feedback
    - After APIs are in good shape (meets basic requirements etc.), ARM ensures all errors (SDK, RPC) reported by the tool are fixed. But if due to time constraints they can’t fix all errors, ARM can allow onboard to prod. See step b. c. and d. below. 
    - RP can be onboarded to prod at this stage behind a feature flag with the understanding that all errors (SDK, RPC) will need to be fixed before feature flag is removed or PR can be merged to public repo. 
    - ARM still ensures critical SDK issues are also fixed as much as possible.
    - Also, there might be feedback based on meeting with Azure API review board which will need to be addressed.
    - After step#6, RP can be onboarded to prod behind a feature flag.
7.	ARM removes the "waitForARMFeedback" label (and may be adds a new one if needed)
    - Can removing label or adding a new label trigger notification/assignment for SDK team to review?
8.	SDK team provides manual feedback
9/ RP meets with Azure API review board for sign off
10.	RP updates to address feedback
    - We don’t want the CI to re-add the label in step 3. Is it possible? 
    - Or we can base it on the new label. So, in step 7, ARM can add a new label “SDKReview”, which can be checked by CI. CI does not add the “WaitForARMFeedback” if this label is already present.
11.	Either all errors are fixed or suppressed
    - If all errors have been fixed and CI is green, owners can sign off.
b.	If some errors need suppression, RP follows the process to get an exception.
12.	SDK/ARM owners sign off 
    - The sign-off enables the PR to be merged to preview branch or main branch depending on the scenario.

## Existing RP/New File ##
This flow is specific to teams which are already onboarded to ARM but are creating a swagger for the first time.

1.	Submit a PR to public repo
2.	CI is triggered
    - CI adds label (WaitForARMFeedback) since this is a new RP/new file
3.	All owners are notified
4.	Add/remove label
    - Remove label manually since this is an old RP
5.	Since label != WaitForARMFeedback, SDK and ARM owners provide manual feedback
6.	RP updates PR to address feedback
    - We don’t want the CI to re-add the label in step 3. Is it possible? 
    - Or we can base it on the new label. So, in step 8, ARM can add a new label “SDKReview”, which can be checked by CI. CI does not add the “WaitForARMFeedback” if this label is already present.
7.	Either all errors are fixed or suppressed
    - If all errors have been fixed and CI is green, great and move to step 6
    - If some errors need suppression, RP follows the process to get an exception
8.	SDK/ARM owners sign off 
    - The sign-off enables the PR to be merged to preview branch or main branch depending on the scenario
    - No Azure API review board sign off needed

## Existing RP/Update Existing File (API version updated, new types added) ##
This flow will be followed by teams when they want to add new types to their service or have made a breaking change to their service.

1.	Submit a PR to private repo
2.	CI is triggered
    - CI does not add any label since its existing file 
    - Option 1 - Can we add a label if the API version is changed or new paths are added to swagger?
    - Option 2- The PR with new types, new API version always goes to private repo.
      In private repo, label is always added and so ARM verifies first
    - If both b. and c. are not possible we can fall back on manually adding the label as in step 4.
3.	All owners are notified
4.	Add/remove label
    - Add "waitForARMFeedback" label manually if new types/paths were added or new API version
5.	If label=WaitForARMFeedback, ARM provides manual feedback
6.	RP updates PR to address feedback
    - After APIs are in good shape (meets basic requirements etc.), ARM ensures all errors (SDK, RPC) reported by the tool are fixed. But if due to time constraints they can’t fix all errors, ARM can allow onboard to prod. See step b., c. and d. below. 
    - RP can be onboarded to prod at this stage behind a feature flag with the understanding that all errors (SDK, RPC) will need to be fixed before feature flag is removed or PR can be merged to public repo. 
    - ARM still ensures critical SDK issues are also fixed as much as possible.
    - Also, there might be feedback based on meeting with Azure API review board which will need to be addressed.
    - After step#6, RP can be onboarded to prod behind a feature flag.
7.	ARM removes the "waitForARMFeedback" label (and may be adds a new one if needed)
    - Can removing label or adding a new label trigger notification/assignment for SDK team to review?
8.	SDK team provides manual feedback
9.	RP updates to address feedback
10.	Either all errors are fixed or suppressed
    - If all errors have been fixed and CI is green, great and move to step 6
    - If some errors need suppression, RP follows the process to get an exception
11.	SDK/ARM/Azure API review board owners sign off 
    - The sign-off enables the PR to be merged to preview branch or main branch depending on the scenario


## Existing RP/Update Existing File (No breaking change – no new API version, no new type) ##
This flow is followed by teams when they are making non-breaking changes to the swagger. 

1.	Submit a PR to public repo
2.	CI is triggered
    - CI does not add any label since its existing file 
3.	All owners are notified
4.	If label!=WaitForARMFeedback, SDK owners provide manual feedback
5.	RP updates PR to address feedback
6.	Either all errors are fixed or suppressed
    - If all errors have been fixed and CI is green, great and move to step 6
    - If some errors need suppression, RP follows the process to get an exception
7.	Steps 2-6 are repeated till CI is green.
8.	SDK ARM owners sign off 
    - The sign-off enables the PR to be merged to preview branch or main branch depending on the scenario
    - No ARM sign off or Azure API review board sign off needed

