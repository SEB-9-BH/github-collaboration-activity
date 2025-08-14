# Git Branching & Merging…with a Paired Activity

*A step‑by‑step lesson*

---

## Outcomes

* Create and switch branches.
* Open pull requests…review them…merge them.
* Use a clean feature branch workflow.
* Avoid merge conflicts by owning files.
* Use GitHub History and Blame to see contributions.
* Understand that instructors can verify who did what.

---

## Mental Model

Branches are parallel lanes on a highway…you and your partner build in separate lanes…a pull request is the on‑ramp back to Main where traffic must be clean and safe.

---

## What you need

* Git installed.
* Node.js ≥ 18.
* GitHub accounts for both partners.
* VS Code or similar.

---

## Key Terms

* **Branch**…an independent line of commits.
* **Fork**…your own copy of someone else’s repo on GitHub.
* **Clone**…a local copy of a GitHub repo.
* **Origin**…the default remote for *your* repo.
* **Upstream**…the original repo you forked from.
* **Pull Request (PR)**…ask to merge commits into another branch.
* **Merge**…combine histories.
* **Merge conflict**…two commits change the same lines…Git needs you to decide.

---

## Ground Rules for Today

* One person edits a file at a time…announce file ownership before you start.
* Small, frequent commits with clear messages.
* Every change goes through a PR…no direct pushes to `main`.
* Communicate before you pull or push.

---

# Review

### Git Refresher
So far, you have been using git to get code (pull) from a remote repository (on github), writing your own code, tracking it with git, and moving (push) the code from your computer (local version) to github.

When using git locally (on your computer), you have been running the commands in Terminal (Command line).

A git command has a minimum of 1 argument.

Git commands are always executed by first typing `git`

The first argument is the command (or verb), like
- `git init` (initialize a new git repository)
- `git push` (send the code to a remote location)

The second(+) argument gives the first argument context (when needed)
- `git add .` (add all files in this directory)
- `git pull origin main` (get all files from the url that has an alias of `origin`, from the branch `main`)

Lastly, flags can be added
- `git remote -v` (git show remote(s) and be verbose(give more detail))

Here is a table of our commonly used git commands that we've used in this course so far:

| git | Argument | Flag(s)/Additional arguments | Description |
|:---:|:-----------:|:-------:|:-----------:|
| git | init |  |  Initializes a new repository|
| git | add | `.` or filename | Takes untracked files and adds them to the staging area so that they can be committed   |
| git | commit | -m 'some message'  |  Takes a snapshot of files in the staging area/ saves this version of them as a commit|
| git | remote | -v |  Shows the remote repositories associated with the local repository. Most repositories have an alias for their urls like `origin` or `upstream`|
| git | pull | upstream main |  Gets files from a url with an alias of `upstream` from its branch `main`|
| git | push | origin dev |  Sends files to a url with an alias of `origin` to its branch `dev`|
| git | log| --oneline |  Shows a log of commits of a repo (--oneline shows a truncated message)_`q` to exit_|
| git | status |  |  Shows the state of files in a repo (untracked, modified, staged)|

![git workflow from git about page](https://i.imgur.com/MXiZRI0.png)

## Lesson…Git Basics in 10 Steps

1. **Identify yourself locally**

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

2. **Create a new GitHub repo**
   Name it `posts-workshop`. Add a README and a Node `.gitignore`. Make `main` the default branch.

3. **Clone it**

```bash
git clone https://github.com/<OwnerUsername>/posts-workshop.git
cd posts-workshop
```

4. **Branching 101**

```bash
git checkout -b feature/my-first-branch
# work…add files
git add .
git commit -m "feat: initial work"
git push -u origin feature/my-first-branch
```

5. **Open a Pull Request**
   On GitHub…Compare & pull request…write a clear title and description…link an Issue if you made one.

6. **Review a PR**
   Leave comments…request changes or approve.

7. **Merge a PR**
   Use “Squash and merge” for clean history…delete the branch on GitHub after merge.

8. **Sync your local main**

```bash
git checkout main
git pull --rebase origin main
```

9. **Fork vs Clone**

* Clone when you are a collaborator on the same repo.
* Fork when you are contributing *to someone else’s* repo.

10. **History and Blame**

* GitHub file view → **History** shows commits over time.
* GitHub file view → **Blame** shows who last touched each line.
* CLI:

```bash
git log --oneline --graph --decorate --all
git blame path/to/file.js
```

> **Reality check:** Instructors can see commits, PR authors, reviews, timestamps…we can tell who actually contributed.

---

## Paired Activity…Round 1

*Role A is the Repo Owner. Role B is the Contributor via Fork.*
*Goal…ship a tiny Express app with REST Index…New…Create routes for Posts. No merge conflicts.*

### Architecture you’ll build

```
posts-workshop/
  server.js                 # owned by A
  controllers/
    postController.js       # owned by B
  models/
    postModel.js            # owned by B
  package.json
```

Ownership prevents conflicts…A touches only `server.js`…B touches only `models/*` and `controllers/*`.

---

### Step‑by‑Step

#### 0) Owner A…create the repo skeleton

```bash
# in posts-workshop
npm init -y
npm i express
echo "node_modules" >> .gitignore
```

**package.json**…add a start script.

```json
{
  "name": "posts-workshop",
  "version": "1.0.0",
  "main": "server.js",
  "type": "commonjs",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.19.2"
  }
}
```

Create a README with the app goal and roles. Commit to `main`.

```bash
git add .
git commit -m "chore: init Node project…add express and scripts"
git push origin main
```

> Optional for best practice…A turns on branch protection for `main` on GitHub…require PRs.

---

#### 1) Owner A…make the server branch and **only** create `server.js`

```bash
git checkout -b feature/server
```

**server.js**

> A wires the shell of the server and mounts a controller path that will exist after B’s PR. It will run after merge…that’s fine.

```js
// server.js
const express = require('express');
const app = express();

// Built-in body parser for form posts
app.use(express.urlencoded({ extended: true }));

// Basic home page
app.get('/', (req, res) => {
  res.send(`
    <h1>Posts App</h1>
    <p><a href="/posts">View Posts</a> … <a href="/posts/new">New Post</a></p>
  `);
});

// Mount posts controller router…file will arrive from B's PR
const postsRouter = require('./controllers/postController');
app.use('/posts', postsRouter);

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(\`http://localhost:\${PORT}\`));
```

Commit and push.

```bash
git add server.js
git commit -m "feat: server shell…mount /posts router"
git push -u origin feature/server
```

Open a PR to `main` named **“feat: server shell”**. Assign B as reviewer. Keep it open for now or merge it…either order works because files do not overlap.

---

#### 2) Contributor B…fork and clone

On GitHub…B clicks **Fork** on A’s repo. Then:

```bash
git clone https://github.com/<BUsername>/posts-workshop.git
cd posts-workshop
git remote add upstream https://github.com/<OwnerUsername>/posts-workshop.git
git remote -v
```

Sync with upstream main.

```bash
git fetch upstream
git checkout main
git pull --rebase upstream main
git push origin main
```

---

#### 3) Contributor B…create feature branch and **only** create model and controller

```bash
git checkout -b feature/post-model-controller
mkdir -p models controllers
```

**models/postModel.js**

```js
// Simple in-memory store…resets when server restarts
const posts = [];

function all() {
  return posts;
}

function create({ title, body }) {
  const post = { id: Date.now(), title, body };
  posts.push(post);
  return post;
}

module.exports = { all, create };
```

**controllers/postController.js**

```js
const express = require('express');
const Post = require('../models/postModel');

const router = express.Router();

// INDEX … GET /posts
router.get('/', (req, res) => {
  const items = Post.all();
  const list = items.map(p => `<li><strong>${p.title}</strong><br/>${p.body}</li>`).join('');
  res.send(`
    <h1>All Posts</h1>
    <p><a href="/posts/new">New Post</a> … <a href="/">Home</a></p>
    <ul>${list || '<em>No posts yet</em>'}</ul>
  `);
});

// NEW … GET /posts/new
router.get('/new', (req, res) => {
  res.send(`
    <h1>New Post</h1>
    <form method="POST" action="/posts">
      <p><label>Title <input name="title" required /></label></p>
      <p><label>Body <textarea name="body" required></textarea></label></p>
      <button type="submit">Create</button>
    </form>
    <p><a href="/posts">Back</a></p>
  `);
});

// CREATE … POST /posts
router.post('/', (req, res) => {
  const { title, body } = req.body || {};
  Post.create({ title, body });
  res.redirect('/posts');
});

module.exports = router;
```

Commit and push to your fork.

```bash
git add .
git commit -m "feat: in-memory Post model and controller with Index…New…Create"
git push -u origin feature/post-model-controller
```

Open a PR from your fork to A’s `main`. Title it **“feat: Post model + controller”**.

---

#### 4) Review and merge without conflicts

* A reviews B’s PR…checks files and runs locally if desired.

```bash
git checkout main
git pull --rebase origin main
npm install
npm start
# After B's PR merges, the server will run…http://localhost:3000
```

* A merges B’s PR.
* A merges A’s **server** PR if still open.
* Delete merged branches on GitHub.

No conflicts because file ownership was clean.

---

#### 5) Both partners…sync and test locally

**A**

```bash
git checkout main
git pull --rebase origin main
npm install
npm start
```

**B**

```bash
git checkout main
git fetch upstream
git pull --rebase upstream main
git push origin main
npm install
npm start
```

Visit:

* `/` home.
* `/posts` index.
* `/posts/new` create form.
  Create a post…see it on index.

---

#### 6) Show receipts…History and Blame

* On GitHub open `server.js`…click **History**…see A’s commits.
* Open `controllers/postController.js`…click **Blame**…see B’s lines and commit hashes.
* CLI quick views:

```bash
git log --oneline --graph --decorate --all
git blame controllers/postController.js
```

Your instructors use the same tools…we can see who actually participated.

---

## Paired Activity…Round 2

*Swap roles. B is Owner…A is Contributor.*
Repeat the process quickly to lock it in.

**Fast path checklist**

1. B creates new repo `posts-workshop-2` with README and `.gitignore`.
2. B creates `feature/server` and writes `server.js`. PR open.
3. A forks B’s repo…clones…adds `upstream`.
4. A creates `feature/post-model-controller`…adds model and controller. PR open.
5. B merges PRs…both sync and test.
6. Demonstrate History and Blame again.

---

## What you built…Routes

* `GET /posts` … Index.
* `GET /posts/new` … New.
* `POST /posts` … Create.

No database…the model is in memory…simple and perfect for practicing collaboration.

---

## Workflow Cheatsheet

```bash
# Create a branch
git checkout -b feature/scope

# Stage and commit
git add <files>
git commit -m "feat: clear message"

# Push and open PR
git push -u origin feature/scope

# Update local main after merges
git checkout main
git pull --rebase origin main

# For forked contributors…track upstream
git fetch upstream
git pull --rebase upstream main
git push origin main

# Visualize history
git log --oneline --graph --decorate --all

# See who changed what
git blame path/to/file
```

---

## Best Practices…Pros and Cons

**Feature branches**

* **Pros:** Isolate work…clean PRs…easy reviews…safer main.
* **Cons:** Can drift if you avoid rebasing…requires discipline to keep small.

**Fork workflow**

* **Pros:** Great for open source…permissionless…clear ownership.
* **Cons:** Two remotes to manage…slightly more overhead for syncing.

**One person per file at a time**

* **Pros:** Eliminates most conflicts…faster merges.
* **Cons:** Requires coordination…sometimes blocks parallel edits.

**Squash merge**

* **Pros:** Clean main history…one commit per PR.
* **Cons:** Harder to bisect within a PR’s internal commits.

---

## Assessment…Definition of Done

* Two repos created…each partner owned one.
* At least two PRs merged per round.
* App runs locally…can create and display posts.
* GitHub History and Blame verified by both.
* No merge conflicts occurred.

---

## Stretch Goals

* Add an `Edit`…`Show` route on a new branch.
* Intentionally cause a conflict on a practice branch…resolve it together.
* Add EJS templates and static CSS on separate feature branches.
* Turn on required reviews for `main` and test the rule.

---

## Key Takeaways

* Branches let you build in parallel…PRs safely merge lanes back into traffic.
* Clear file ownership and small PRs prevent conflicts.
* Forks add an upstream…sync early and often.
* History and Blame tell the story…participation is visible and verified.
* Disciplined branching is a superpower for teams.
