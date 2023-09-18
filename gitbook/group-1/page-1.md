#Normal hotfix from last released on production
Go to the last remote branch released on production that should be a name like `origin/releases/production-YYYYMMDD_XX` (eg `origin/releases/production-20220214_01`).
1. Usually at that commit we should create two branch names:
   - releases/production-YYYYMMDD_XX-hotfix (eg. `releases/production-20220214_01-hotfix`). 
     - This branch will be the PR destination branch of the hotfix. 
     - You only need to be sure that is created at the correct commit and push it to origin (only push it, not make a PR at this point). 
     - The branches creates in the release folder has a policy that is not allowed removed by default (you have to ask for it at a project administrator as José Manuel or Nico). 
     - And also the policy forces to accept the new commits by PR.
   - hotfix/production-YYYYMMDD_XX-hotfix (eg. hotfix/production-20220214_01-hotfix). 
     - This branch will be the PR origin branch of the hotfix. 
     - You should created also at the same commit. 
     - Now you can cherry-pick all the PR commits that you need to add to the hotfix. Be careful with the conflicts (if any happens) and check with the PR commits creators if have any doubts. 
     - After joining all the commits run on your visual studio all the tests on release mode is good practice. 
1. Creates the PR of the branch hotfix/production-YYYYMMDD_XX-hotfix into releases/production-YYYYMMDD_XX-hotfix. 
   - For that remember changes the destination branch instead of master
   - For the PR title put: "Hotfix over production YYYYMMDD_XX"
   - Add the commit messages to the PR description 
   - Tag the PR with the tag Hotfix branch
   - See the example: ![image.png](/.attachments/image-97c33d34-a515-435e-944b-63f44bb40f8c.png)
1. Ask for the review of the PR and wait until it's completed
1. Launch the builds pipelines to make the release:
   - Go to Pipelines/Pipelines
   - Run the build pipeline for the frontend: "Atlantida - Frontend" (the name of the correct pipeline at the time of writing this)
     - Be sure that you select the correct branch for running the pipeline releases/production-YYYYMMDD_XX-hotfix instead of the default branch master. ![image.png](/.attachments/image-5731c5d5-c463-4505-a723-6769ecaf5fb5.png)    
   - Run the build pipeline for the API: "Atlanida - API" (the name of the correct pipeline at the time of writing this. Not to be confused with the pipeline called "
Atlantida - PR API") 
     - Be sure that you select the correct branch for running the pipeline releases/production-YYYYMMDD_XX-hotfix instead of the default branch master. ![image.png](/.attachments/image-408829fa-5ee2-41ca-be74-d56c6fefb590.png) 
     - If the hotfix is very urgent you can consider marking the check IsHotfix
   - Keep the name of the builds to choose the correct one later in the release step:
      - ![image.png](/.attachments/image-754b9f53-86bd-4830-93ea-0cd8c555d286.png)
      - ![image.png](/.attachments/image-521d87f9-8f3a-434c-b6e8-a52e464c564e.png)
1. Create the release:
   - Go to Pipelines/Releases and use the release pipeline "Pre & Pro" (the name of the correct pipeline at the time of writing this)
   - Create a release select the artifacts of frontend and API for this release using the name of the builds of the previous step. ![image.png](/.attachments/image-6ed434de-d0ff-4b96-97b8-a0e68283ef57.png) 
   - It's good practice to check that current release artifacts are from the correct branch:![image.png](/.attachments/image-946244fa-32ee-4899-9f12-3c815df8cb63.png)

At this point is like any other release to the production environment:
  
1. Deploy first in the stage "HA - Staging - Prod". Obviously, skip the "Atlantida - Pre" stage. (The name of the stages at the time of writing this)
   - This deploy in the two regions and deploy in the staging stage of the production environment. But these staging slots is only for the app services, it's mean the application (frontend & API). Every other thing such web jobs, azure batch or the afi export app are deployed at this time
1. Now you can access to staging application to check the new changes. You can use the URL of the staging app-service and the token that you can get from Braulio access to the local environment. To check the new changes in production you must remember that you can not create a proposal or a test for any customer. **IMPORTANT: This step makes any database migration was launched. That makes this a mandatory step to be done in case a new migration have to be applied to the database.** 
1. Be sure to apply any other database script or upload any file to storage (if needed). Do it at this time to use the swap for refreshing the server state. This avoids a later restart of the production server.
1. Approve, finally, the swap into the "HA - Prod" stage. (The name of the stages at the time of writing this)![image.png](/.attachments/image-6d5d2fac-5945-41de-b9fc-e3603bb9aee3.png)
1. Verify the deployment & report about it (if needed). ![image.png](/.attachments/image-4016c477-a3ef-4bab-bf41-6de468f512eb.png)
  
# Hotfix based on a recent release on pre
Sometimes, we need to make a partial update of the master branch, instead of a pure hotfix over the last branch released. Not the last version of master nor one of the previous releases to pre. I mean, based on one of the previous releases of pre plus a specific set of PR committed to master after that release. This allows us to have a better synchronization between the master branch and the production environment.
In this case, we need to proceed in a similar way to the previous case: We get the names of the branch as a hotfix of the last production releases but create the branches at the commit of the base release on pre (or commit on the master branch). As an example see https://afi-dev.visualstudio.com/Atlantida/_git/Atlantida/pullrequest/17085
![image.png](/.attachments/image-b977ff4b-06cb-4025-a992-55ac24bbaba1.png)
- The last branch releases on pro at this time had been `releases/production-20220120_01` but the hotfix would be based on `releases/pre-20220112_01`. 
- Anyway, the two branches were created at `releases/pre-20220112_01` with names `hotfix/production-20220120_01-hotfix` & `releases/production-20220120_01-hotfix`
- The PR title explains this: "Hotfix over production 20220120_01 (really after rebased pre-20220112_01)"
![image.png](/.attachments/image-f0ab7c7f-87b3-4798-8f53-26088aa991b5.png)

