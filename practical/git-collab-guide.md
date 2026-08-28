# XHAW5112-2026 — Group Submission

## Git & GitHub Collaboration Workflow

> **Purpose:** This guide explains how your group will set up, share, develop, review, and merge your website and mobile application projects using Git and GitHub.

---

## 1. Before You Start

For this group submission, your repositories are created through the **Classroom50 assignment process**.

You will work with two repositories:

1. **mobile-app**
2. **website**

Your group will consist of:

| Group Member | Responsibility |
|---|---|
| Student 1 | Initial repository/project setup and collaboration setup |
| Student 2 | Clone repository and contribute through branches |
| Student 3 | Clone repository and contribute through branches |

### Important

**Do not create a new GitHub repository for this assignment.**

The assignment process has already created the repositories for your group.

Your job is to:

1. Accept the assignment/repository access.
2. Set up the initial project.
3. Connect the local project to the provided remote repository.
4. Invite your group members.
5. Collaborate using branches and Pull Requests.

**Note:**
Please remember that when *Student 1* add their fellow group members to the repository > Go to the ****Settings** of the repository and make sure that the group memebers are assigned the **Admin** role as well. This allows full rights to create branches, push, pull requests, etc.  

---

## 2. Understanding the Workflow

Before working on the projects, understand the basic relationship between your computer and GitHub.

```
YOUR COMPUTER
(Local Repository)
       |
       | git push
       v
GITHUB
(Remote Repository)
       |
       | Pull Request
       v
main branch
       |
       | git pull
       v
YOUR COMPUTER
(Updated Local Repository)
```

You will normally work through the following cycle:

1. Update your local main
2. Create a feature branch
3. Make your changes
4. `git add`
5. `git commit`
6. `git push`
7. Create Pull Request
8. Another group member reviews
9. Approve
10. Merge into main
11. Delete completed branch
12. Everyone switches to main
13. Everyone pulls the latest main
14. Start the next task

### The Golden Rule

**Do your development work on your own branch. Keep main as the shared, working version of the project.**

---

## 3. Part A — Website Project

### 3.1 Repository Access

#### Student 1

Student 1 is responsible for the initial repository setup.

- Accept the assignment/invitation for the website repository.
- Confirm that you can access the repository on GitHub.
- Add Students 2 and 3 as collaborators to the repository.

#### Students 2 and 3

Once Student 1 has added you as collaborators:

- Confirm that you can access the repository.
- You will clone the repository after Student 1 has completed the initial project setup.

---

## 4. Website — Create the Initial Project Structure

Student 1 will create the initial project structure on their computer.

The purpose of this stage is to create the starting template for the website.

At this point, you are not completing the website content.

You are creating the files and folders that the group will work with.

Your project should eventually have the following structure:

```
project/
├── index.html
│
├── pages/
│   ├── about.html
│   ├── courses.html
│   ├── single_course.html
│   ├── single_course.html
│   ├── single_course.html
│   ├── single_course.html
│   ├── single_course.html
│   ├── single_course.html
│   ├── single_course.html
│   ├── fees.html
│   └── contact.html
│
├── assets/
│   ├── images/
│   │   ├── image1.jpg
│   │   ├── logo.png
│   │   └── ...
│   │
│   └── style/
│       └── main.css
│
├── js/
│   ├── script.js
│   └── app.js
│
└── README.md
```

> **Note:** The `single_course.html` files represent the seven individual course pages required for the project. Use appropriate filenames if your implementation requires each course page to be uniquely named.

---

## 5. Website — Initialise Git

Open a terminal in the root of your website project.

You should be inside the folder containing:

- `index.html`
- `pages/`
- `assets/`
- `js/`
- `README.md`

Run:

```bash
git init
```

This creates a hidden `.git` directory and turns your project folder into a Git repository.

### 5.1 Add the Initial Files

Stage the files:

```bash
git add .
```

Check what Git is preparing to commit:

```bash
git status
```

You should see your project files listed as changes ready to be committed.

### 5.2 Create the Initial Commit

Create your first commit:

```bash
git commit -m "created empty template files"
```

This records the initial project structure in Git.

### 5.3 Set the Main Branch

Rename the current branch to main:

```bash
git branch -M main
```

Your local repository now has a `main` branch.

---

## 6. Connect the Local Repository to the Assignment Repository

Your assignment repository already exists on GitHub.

Copy the repository URL from GitHub.

It will look similar to:

```
https://github.com/ORGANISATION/REPOSITORY-NAME.git
```

Add the remote repository:

```bash
git remote add origin https://github.com/ORGANISATION/REPOSITORY-NAME.git
```

You can check that the remote was added:

```bash
git remote -v
```

You should see `origin` pointing to your assignment repository.

---

## 7. Synchronise With the Remote Repository

In many cases, the assignment repository will be completely empty.

If the remote repository contains an initial commit, however, your local repository and the remote repository may have separate Git histories.

In that situation, Git may need permission to combine the two histories.

Run:

```bash
git pull origin main --allow-unrelated-histories
```

#### Why are we doing this?

Your local project and the remote repository may have started independently.

For example:

```
LOCAL REPOSITORY              REMOTE REPOSITORY

Initial local commit          Initial remote commit
        |                              |
        +------------+-----------------+
                     |
              Git combines
              the histories
```

The `--allow-unrelated-histories` option tells Git that you understand that these two histories started independently and that they should be combined.

> **Important:** If the remote repository is completely empty and Git does not require this step, you may not need it. Follow any instructions provided by your lecturer if the repository behaves differently.

---

## 8. Push the Initial Project

Push your local `main` branch to GitHub:

```bash
git push -u origin main
```

The initial website project should now be available in the shared GitHub repository.

---

## 9. Add the Other Group Members

Student 1 must now add Students 2 and 3 as collaborators.

On GitHub:

1. Open the repository.
2. Open **Settings**.
3. Find the repository access/collaborator settings.
4. Add Student 2.
5. Add Student 3.
6. Confirm that they have the required access.

> The exact GitHub menu wording may change over time. Look for the repository's **Collaborators** / **Access** / **Manage access** settings.

---

## 10. Students 2 and 3 — Clone the Website Repository

Once Student 1 has pushed the initial project and added you as collaborators, Students 2 and 3 should clone the repository.

Open a terminal and navigate to the folder where you keep your projects.

For example:

```bash
cd Projects
```

Clone the repository:

```bash
git clone https://github.com/ORGANISATION/REPOSITORY-NAME.git
```

Enter the project:

```bash
cd REPOSITORY-NAME
```

Check the branch:

```bash
git branch
```

You should be working from the `main` branch.

---

## 11. Open the Website in VS Code

Open the project in Visual Studio Code.

You can also open it through:

```
VS Code
→ File
→ Open Folder
→ Select your project folder
```

Then open a terminal:

```
Terminal
→ New Terminal
```

You should now be working inside the cloned Git repository.

---

## 12. The Collaborative Development Workflow

From this point onwards, all group members follow the same workflow.

Each student should work on a separate task using a separate branch.

### Step 1 — Make Sure main Is Current

Before starting a new task:

```bash
git checkout main
```

Then:

```bash
git pull origin main
```

This ensures that your local `main` branch contains the latest work that has already been merged.

**Do not skip this step when starting a new task.**

---

## 13. Step 2 — Create Your Feature Branch

Create a branch for the task you are going to complete.

For example:

```bash
git checkout -b edit/firstaid-page
```

Other examples:

```bash
git checkout -b edit/about-page
git checkout -b edit/courses-page
git checkout -b edit/contact-page
```

### Branch naming rule

Use:

```
edit/description-of-task
```

For example:

- `edit/about-page`
- `edit/courses-page`
- `edit/contact-page`
- `edit/firstaid-page`

**One task = one branch.**

Your branch should describe the work you are doing.

---

## 14. Step 3 — Make Your Changes

Now work on the files assigned to you.

For example, you may:

- Add content to an HTML page.
- Create a new page.
- Add images.
- Add CSS.
- Add JavaScript functionality.
- Improve an existing component.
- Correct an existing problem.

Work normally in VS Code.

Save your files regularly.

---

## 15. Step 4 — Check Your Changes

Before committing, check the state of your repository:

```bash
git status
```

This helps you see which files have been changed.

You can also review the differences:

```bash
git diff
```

Make sure the changes are yours and that you have not accidentally modified unrelated files.

---

## 16. Step 5 — Stage Your Changes

Stage your changes:

```bash
git add .
```

Or stage a specific file:

```bash
git add pages/about.html
```

Then check:

```bash
git status
```

Your changes should now be ready to commit.

---

## 17. Step 6 — Commit Your Changes

Create a descriptive commit:

```bash
git commit -m "added content for firstaid page"
```

Your commit message should describe what you actually changed.

Examples:

```bash
git commit -m "added content for about page"
git commit -m "added course information"
git commit -m "updated website navigation"
```

**Avoid messages such as:**

- `changes`
- `stuff`
- `update`
- `my work`
- `test`

A good commit message tells your group what changed.

---

## 18. Step 7 — Push Your Branch

Push your branch to GitHub:

```bash
git push -u origin edit/firstaid-page
```

Replace the branch name with your actual branch.

For example:

```bash
git push -u origin edit/about-page
```

or:

```bash
git push -u origin edit/courses-page
```

Your branch is now available on GitHub.

---

## 19. Step 8 — Create a Pull Request

Go to the GitHub repository.

Open:

```
Pull Requests
→ New Pull Request
```

Make sure the Pull Request is:

- **base:** `main`
- **compare:** `your-branch`

For example:

```
main  ←  edit/firstaid-page
```

### Important

You are asking GitHub to merge:

```
YOUR BRANCH
     ↓
   main
```

You are not merging `main` into your feature branch through this Pull Request.

Give the Pull Request a useful title and description.

Explain:

- What you changed.
- Which page/file you worked on.
- Anything the reviewer should check.
- Any problems or questions.

---

## 20. Step 9 — Have Another Group Member Review

Where possible, another group member should review your Pull Request.

Do not make the entire process:

```
Student 1
   ↓
Student 1
   ↓
Student 1
```

The purpose of the Pull Request is to practise collaboration and code review.

A reviewer should:

1. Open the Pull Request.
2. Read the description.
3. Select **Files changed**.
4. Review the changes.
5. Check that the work is appropriate.
6. Add constructive comments if necessary.
7. Approve the Pull Request when satisfied.

---

## 21. If Changes Are Required

If the reviewer identifies a problem:

1. Read the review comments.
2. Return to your local branch.
3. Make the requested changes.
4. Save the files.
5. Stage the changes:

```bash
git add .
```

6. Commit:

```bash
git commit -m "updated page based on review"
```

7. Push the branch again:

```bash
git push
```

The existing Pull Request will automatically update with the new commit.

The reviewer can then review the changes again.

---

## 22. Step 10 — Approve and Merge

Once the reviewer is satisfied:

1. Approve the Pull Request.
2. Merge the Pull Request into `main`.
3. Confirm the merge.

The changes from your feature branch are now part of `main`.

```
edit/firstaid-page
        |
        | Pull Request
        ↓
      main
```

---

## 23. Step 11 — Delete the Completed Branch

After the Pull Request has been successfully merged, delete the completed branch on GitHub.

For example:

```
edit/firstaid-page
```

can now be deleted.

### Why?

Because the work has already been merged into:

```
main
```

The branch has completed its purpose.

**Only delete the branch after you have confirmed that the Pull Request has been merged successfully.**

---

## 24. Step 12 — Everyone Updates Their Local main

After a Pull Request has been merged, everyone in the group must update their local repository.

First:

```bash
git checkout main
```

Then:

```bash
git pull origin main
```

Your local `main` branch now contains the newly merged work.

```
GitHub main
    |
    | git pull
    ↓
Your local main
```

---

## 25. Starting the Next Task

When you are ready to begin another task, repeat the process.

```bash
git checkout main
git pull origin main
```

Then create a new branch:

```bash
git checkout -b edit/next-task
```

Work on the task:

```
EDIT
  ↓
git add .
  ↓
git commit
  ↓
git push
  ↓
Pull Request
  ↓
Review
  ↓
Approve
  ↓
Merge
  ↓
Delete branch
  ↓
checkout main
  ↓
git pull
```

---

## 26. Website — Complete Workflow

The complete workflow can be summarised as:

```
CLASSROOM50
    │
    ├── Assignment repository created
    │
    ↓
STUDENT 1
    │
    ├── Accept assignment
    ├── Create project structure
    ├── git init
    ├── git add .
    ├── git commit
    ├── git branch -M main
    ├── git remote add origin
    ├── git pull origin main --allow-unrelated-histories
    ├── git push -u origin main
    │
    ↓
ADD STUDENTS 2 & 3
    │
    ↓
STUDENTS 2 & 3
    │
    └── git clone
    │
    ↓
ALL STUDENTS
    │
    ├── git checkout main
    ├── git pull origin main
    ├── git checkout -b edit/your-task
    ├── Make changes
    ├── git add .
    ├── git commit
    ├── git push
    │
    ↓
GITHUB
    │
    ├── Create Pull Request
    ├── Another member reviews
    ├── Approve
    ├── Merge
    └── Delete branch
    │
    ↓
ALL STUDENTS
    │
    ├── git checkout main
    └── git pull origin main
    │
    ↓
NEXT TASK
```

---

## 27. Part B — Mobile Application

The mobile application follows the same Git collaboration workflow.

The main difference is that the initial project is created using Android Studio rather than manually creating an HTML project.

---

## 28. Android Project — Initial Setup

Student 1 is responsible for creating the initial Android project.

Open Android Studio.

Select:

```
File
→ New Project
```

Select:

```
Empty Views Activity
```

Create the project using the required:

- Project name
- Package/namespace
- Save location
- Minimum SDK
- Other project settings specified for the assignment

Click:

```
Create
```

Android Studio will generate the initial Android project.

---

## 29. Create the Required Activities

The project requires a minimum of 12 screens/activities.

The initial project provides the first activity.

Create the additional activities required by the project.

For example:

- `MainActivity.kt`
- `AboutActivity.kt`
- `CoursesActivity.kt`
- `SingleCourseActivity.kt`
- ...
- `FeesActivity.kt`
- `ContactActivity.kt`

You should have the required number of screens before the group begins the main development process.

Your Android project will contain files such as:

```
app/
├── src/
│   └── main/
│       ├── java/
│       │   └── .../
│       │       ├── MainActivity.kt
│       │       ├── AboutActivity.kt
│       │       ├── CoursesActivity.kt
│       │       ├── SingleCourseActivity.kt
│       │       ├── ...
│       │       ├── FeesActivity.kt
│       │       └── ContactActivity.kt
│       │
│       └── res/
│           ├── layout/
│           │   ├── activity_main.xml
│           │   └── ...
│           │
│           └── values/
│               └── strings.xml
│
└── ...
```

> The exact Android Studio project structure may differ slightly depending on the Android Studio version and project configuration.

---

## 30. Android Project — Initialise Git

Open the Android Studio terminal or a terminal in the project root.

The project root is the folder containing the Android project files.

Run:

```bash
git init
```

Stage the project:

```bash
git add .
```

Create the initial commit:

```bash
git commit -m "created empty template files"
```

Set the branch to main:

```bash
git branch -M main
```

---

## 31. Connect the Android Project to the Assignment Repository

Add the remote repository provided through the assignment:

```bash
git remote add origin https://github.com/ORGANISATION/REPOSITORY-NAME.git
```

Check it:

```bash
git remote -v
```

If the remote repository contains an initial commit, synchronise the histories:

```bash
git pull origin main --allow-unrelated-histories
```

Then push the initial project:

```bash
git push -u origin main
```

---

## 32. Add the Other Group Members

Student 1 must add Students 2 and 3 as collaborators to the `mobile-app` repository.

Repeat the same process used for the website repository.

Students 2 and 3 should then confirm that they can access the repository.

---

## 33. Students 2 and 3 — Clone the Android Project

Open a terminal in the folder where you store Android projects.

Clone the repository:

```bash
git clone https://github.com/ORGANISATION/REPOSITORY-NAME.git
```

Enter the project:

```bash
cd REPOSITORY-NAME
```

Open the project in Android Studio.

```
Android Studio
→ File
→ Open
→ Select the cloned project
```

Allow Android Studio to complete any required project synchronisation before starting development.

---

## 34. Android — Collaborative Development

From this point onward, the Git workflow is the same as the website workflow.

Before starting a task:

```bash
git checkout main
git pull origin main
```

Create a branch:

```bash
git checkout -b edit/your-task
```

For example:

```bash
git checkout -b edit/about-screen
```

Make your changes in Android Studio.

Then:

```bash
git status
```

Stage:

```bash
git add .
```

Commit:

```bash
git commit -m "added content for about screen"
```

Push:

```bash
git push -u origin edit/about-screen
```

---

## 35. Android — Pull Request Workflow

On GitHub:

1. Open the repository.
2. Open **Pull Requests**.
3. Create a new Pull Request.
4. Set the base branch to: `main`
5. Set the compare branch to your feature branch.
6. Describe your changes.
7. Ask another group member to review the work.
8. The group member reviews the changes.
9. Approve the Pull Request when satisfied.
10. Merge the Pull Request.
11. Delete the completed feature branch.

---

## 36. Android — Update Your Local Project

After the Pull Request has been merged:

```bash
git checkout main
```

Then:

```bash
git pull origin main
```

Your local Android project now contains the latest merged changes.

Before starting your next task:

```bash
git checkout main
git pull origin main
git checkout -b edit/next-task
```

---

## 37. Important Git Rules for the Group

### Rule 1 — Do not work directly on main

Use:

```bash
git checkout -b edit/your-task
```

instead.

### Rule 2 — Pull before starting a new task

Always update your local main:

```bash
git checkout main
git pull origin main
```

Then create your new branch.

### Rule 3 — One task = one branch

Use descriptive branch names:

- `edit/about-page`
- `edit/courses-page`
- `edit/contact-page`
- `edit/about-screen`
- `edit/navigation`
- `edit/login-screen`

### Rule 4 — Commit regularly

A commit should represent a meaningful piece of completed work.

For example:

```bash
git commit -m "added content for courses page"
```

### Rule 5 — Push your branch

Your work is not available to the group until your branch has been pushed:

```bash
git push -u origin edit/your-task
```

### Rule 6 — Use Pull Requests

Do not simply push everything directly into `main`.

Use:

```
Branch
   ↓
Push
   ↓
Pull Request
   ↓
Review
   ↓
Approve
   ↓
Merge
```

### Rule 7 — Review someone else's work

The purpose of the Pull Request process is collaboration.

Where possible, have another group member review your work before it is merged.

### Rule 8 — Delete completed branches

Once a branch has been successfully merged:

- Delete the branch

This keeps the repository organised.

### Rule 9 — Pull after a merge

After another group member's work has been merged:

```bash
git checkout main
git pull origin main
```

Otherwise, your local project may not contain the latest version.

---

## 38. Common Mistakes

### Mistake 1 — Creating another repository

Do not do this.

Your assignment repository has already been created.

### Mistake 2 — Working on main

If you accidentally start working on `main`, stop and check with your lecturer before pushing.

The normal workflow is:

```bash
git checkout -b edit/your-task
```

### Mistake 3 — Creating a branch from an outdated main

Before creating a new branch:

```bash
git checkout main
git pull origin main
```

Then:

```bash
git checkout -b edit/your-task
```

### Mistake 4 — Forgetting to commit

Running:

```bash
git add .
```

does not create a commit.

You must also run:

```bash
git commit -m "describe your changes"
```

### Mistake 5 — Forgetting to push

A commit exists locally until you push it.

Use:

```bash
git push
```

or, for the first push of a new branch:

```bash
git push -u origin edit/your-task
```

### Mistake 6 — Creating the Pull Request in the wrong direction

Your Pull Request should be:

```
main  ←  your feature branch
```

Your feature branch contains your changes.

You are asking GitHub to merge those changes into `main`.

### Mistake 7 — Forgetting to pull after someone else's merge

If Student 2's Pull Request has been merged, Student 1 and Student 3 will not automatically have those changes in their local projects.

Run:

```bash
git checkout main
git pull origin main
```

---

## 39. The Git Commands You Need Most

You do not need to memorise every Git command.

For this project, these are the commands you will use most often:

| Action | Command |
|--------|---------|
| Check your current branch | `git branch` |
| Check repository status | `git status` |
| Switch to main | `git checkout main` |
| Get the latest main | `git pull origin main` |
| Create a new branch | `git checkout -b edit/your-task` |
| Stage changes | `git add .` |
| Commit changes | `git commit -m "describe your changes"` |
| Push your branch | `git push -u origin edit/your-task` |
| See the remote repository | `git remote -v` |

---

## 40. Quick Reference — New Task

Whenever you begin a new piece of work, use:

```bash
git checkout main
git pull origin main
git checkout -b edit/your-task
```

Then:

```
Make changes
```

Then:

```bash
git add .
git commit -m "describe your changes"
git push -u origin edit/your-task
```

Then on GitHub:

```
Create Pull Request
        ↓
Another member reviews
        ↓
Approve
        ↓
Merge
        ↓
Delete branch
```

Then everyone:

```bash
git checkout main
git pull origin main
```

---

## 41. Complete Group Workflow

```
                    CLASSROOM50
                         ↓
                Assignment Repository
                         ↓
                  Student 1 accepts
                         ↓
              Student 1 creates project
                         ↓
                    git init
                         ↓
                    git add .
                         ↓
                      commit
                         ↓
                  branch → main
                         ↓
                  add remote origin
                         ↓
        pull --allow-unrelated-histories
                         ↓
                    push main
                         ↓
             Add Students 2 and 3
                         ↓
                Students clone
                         ↓
              ┌──────────┴──────────┐
              ↓                     ↓
        Student 1 branch      Student 2 branch
              ↓                     ↓
        Make changes          Make changes
              ↓                     ↓
          add/commit             add/commit
              ↓                     ↓
            push                  push
              │                     │
              └──────────┬──────────┘
                         ↓
                  Pull Requests
                         ↓
                      Review
                         ↓
                      Approve
                         ↓
                       Merge
                         ↓
                   Delete branch
                         ↓
                    Everyone:
                  checkout main
                         ↓
                  git pull origin main
                         ↓
                   Next task
                         ↓
                       REPEAT
```

---

## 42. Final Checklist

Before considering your group Git workflow complete, make sure:

- [ ] Student 1 accepted the assignment/repository access.
- [ ] Student 1 created the initial project structure.
- [ ] Student 1 initialised Git.
- [ ] The initial project was committed.
- [ ] The local branch was renamed to `main`.
- [ ] The assignment repository was added as `origin`.
- [ ] The local and remote repositories were synchronised if required.
- [ ] The initial `main` branch was pushed.
- [ ] Students 2 and 3 were added as collaborators.
- [ ] Students 2 and 3 cloned the repository.
- [ ] All group members can open the project successfully.
- [ ] Each task is developed on a separate branch.
- [ ] Changes are staged with `git add`.
- [ ] Changes are committed with a meaningful message.
- [ ] Branches are pushed to GitHub.
- [ ] Pull Requests are created from the feature branch into `main`.
- [ ] Another group member reviews the work where possible.
- [ ] Pull Requests are approved.
- [ ] Pull Requests are merged.
- [ ] Completed branches are deleted.
- [ ] Everyone switches back to `main`.
- [ ] Everyone runs `git pull origin main`.
- [ ] The group starts the next task from the updated `main`.

---

## 43. Remember

The most important concept is not memorising Git commands.

It is understanding the collaboration cycle:

```
UPDATE
  ↓
BRANCH
  ↓
WORK
  ↓
ADD
  ↓
COMMIT
  ↓
PUSH
  ↓
PULL REQUEST
  ↓
REVIEW
  ↓
APPROVE
  ↓
MERGE
  ↓
DELETE BRANCH
  ↓
UPDATE MAIN
  ↓
REPEAT
```

This is the workflow you will use throughout the group project.

**
- Keep main stable.   
- Work on branches.   
- Review each other's work.  
- Merge through Pull Requests.  
- Pull the latest main before starting the next task.**