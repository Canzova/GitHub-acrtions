# CI/CD Notes for Beginners (with GitHub Actions)

---

## 1. What is CI/CD?

CI/CD stands for **Continuous Integration** and **Continuous Delivery/Deployment**.

Think of it like an **automatic assembly line** for your code. Every time you write code and save it (push it to GitHub), a robot (the pipeline) automatically:
1. Checks if your code works.
2. Tests it.
3. Packages it.
4. Sends it live (deploys it) — if you want.

You don't have to do these steps by hand every single time.

### 1.1 Continuous Integration (CI)

- "Integration" means combining your code with the main project code.
- **CI = automatically building and testing your code every time you push changes.**
- Goal: catch bugs early, before they pile up.

**Example:** You write a function. You push it to GitHub. CI automatically runs your tests and tells you "Pass ✅" or "Fail ❌" — instead of you manually running tests every time.

### 1.2 Continuous Delivery (CD)

- Once code passes tests, it's automatically packaged and made **ready to deploy** (but a human still clicks the final "go live" button).

### 1.3 Continuous Deployment (also CD)

- Same as above, but **no human needed** — if tests pass, it goes live automatically.

> Simple way to remember:
> - **CI** = Test automatically
> - **Continuous Delivery** = Prepare automatically, deploy manually
> - **Continuous Deployment** = Deploy automatically too

---

## 2. Why do we use CI/CD?

Imagine 5 developers working on the same app.

**Without CI/CD:**
- Everyone writes code, then at the end of the week they try to combine it all → tons of conflicts and bugs.
- Someone has to manually test everything.
- Someone has to manually upload the app to the server.
- Slow, stressful, error-prone.

**With CI/CD:**
- Every small change is tested immediately.
- Bugs are found early (cheaper and easier to fix).
- Code is combined (integrated) constantly, so there's no "big scary merge" at the end.
- Deployment becomes a routine, boring, safe process instead of a scary event.
- Team ships new features faster and more confidently.

**In short — CI/CD gives you:**
- ✅ Fewer bugs in production
- ✅ Faster releases
- ✅ Less manual, repetitive work
- ✅ Confidence that "if it passes the pipeline, it works"

---

## 3. Basic Pipeline Concepts (Vocabulary)

| Term | Meaning |
|---|---|
| **Pipeline** | The full automated process (test → build → deploy) |
| **Stage / Job** | A step group in the pipeline (e.g., "Test" stage, "Build" stage) |
| **Step** | A single action inside a job (e.g., "run npm install") |
| **Trigger** | What starts the pipeline (e.g., a `git push`) |
| **Runner** | The machine (virtual computer) that actually executes your pipeline |
| **Artifact** | A file produced by the pipeline (e.g., a built app, a .zip) |
| **Workflow** | (GitHub Actions term) another name for the whole pipeline definition |

A typical simple pipeline looks like:

```
Push code → Install dependencies → Run tests → Build → Deploy
```

---

## 4. Popular CI/CD Tools

You don't need to learn all of these — just know they exist and what they're for.

| Tool | Notes |
|---|---|
| **GitHub Actions** | Built into GitHub. Easiest to start with if your code is already on GitHub. (We'll learn this one!) |
| **GitLab CI/CD** | Built into GitLab, similar idea to GitHub Actions. |
| **Jenkins** | Very old, very powerful, open-source. Needs to be self-hosted/configured — steeper learning curve. |
| **CircleCI** | Cloud-based, popular for fast builds. |
| **Travis CI** | Was very popular, now less commonly used. |
| **Azure DevOps Pipelines** | Microsoft's CI/CD tool, common in enterprise/Azure-based teams. |
| **AWS CodePipeline** | AWS's native CI/CD tool. |
| **ArgoCD / Spinnaker** | More advanced, used for deployments (especially Kubernetes). |

**Why start with GitHub Actions?**
- It's free for public repos (and has a generous free tier for private ones).
- No extra setup — it lives right inside your GitHub repository.
- You write pipelines in simple YAML files.
- Huge community + marketplace of ready-made actions.

---

## 5. What is GitHub Actions?

**GitHub Actions** is GitHub's built-in automation tool. It lets you run scripts automatically when something happens in your repository — like pushing code, opening a pull request, or on a schedule.

- You define a **workflow** using a `.yml` (YAML) file.
- You place that file inside a special folder: **`.github/workflows/`** in your repo.
- GitHub reads that file and runs it on GitHub's own servers ("runners") — you don't need your own server.

### 5.1 Key Building Blocks

| Concept | What it means |
|---|---|
| **Workflow** | The whole automation defined in one `.yml` file |
| **Event / Trigger** | What causes the workflow to run (e.g., `push`, `pull_request`, `schedule`) |
| **Job** | A set of steps that run together on one runner |
| **Step** | A single command or action inside a job |
| **Action** | A reusable piece of code (e.g., `actions/checkout` downloads your code) |
| **Runner** | The virtual machine that runs your jobs (GitHub provides free ones: Ubuntu, Windows, macOS) |

---

## 6. How to Use GitHub Actions (Step-by-Step)

### Step 1: Have a GitHub repository
Create or use an existing repo — for example, one with a simple Python or Node.js project.

### Step 2: Create the workflows folder
Inside your repo, create this exact folder structure:

```
your-repo/
└── .github/
    └── workflows/
        └── main.yml
```

### Step 3: Write a workflow file (`main.yml`)

This is where the magic happens. GitHub automatically detects any `.yml` file inside `.github/workflows/`.

### Step 4: Push to GitHub

Once you push this file, go to your repo → **Actions** tab → you'll see it running automatically!

---

## 7. Your First Simple Pipeline (Example)

Let's build a **super simple pipeline**: every time you push code, it will print "Hello, CI/CD!" — just to prove the concept works.

```yaml
name: My First Pipeline          # Name shown in the Actions tab

on: push                         # Trigger: run this every time someone pushes code

jobs:
  say-hello:                     # Job name (you can call it anything)
    runs-on: ubuntu-latest       # The virtual machine to run on

    steps:
      - name: Print a greeting
        run: echo "Hello, CI/CD! 🎉 The pipeline is working!"
```

**What's happening here, line by line:**
- `name:` → just a label for the workflow, shown in GitHub's UI.
- `on: push` → this pipeline runs every time you `git push` to the repo.
- `jobs:` → a workflow has one or more jobs.
- `say-hello:` → the name of this particular job.
- `runs-on: ubuntu-latest` → GitHub spins up a free Ubuntu Linux virtual machine to run your job.
- `steps:` → the list of commands to run.
- `run: echo "..."` → runs a shell command (just like in your terminal).

Save this as `.github/workflows/main.yml`, push it, then go to the **Actions** tab of your repo on GitHub — you'll see it run and show the printed message in the logs. 🎉

---

## 8. A Slightly More Realistic Example (Spring Boot app)

Let's say you have a simple Spring Boot (Java) project built with Maven, and you want to automatically build it and run its tests on every push.

```yaml
name: Spring Boot CI

on: push

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
      - name: Get the code
        uses: actions/checkout@v4       # Downloads your repo's code onto the runner

      - name: Set up Java
        uses: actions/setup-java@v4     # Installs Java (JDK) on the runner
        with:
          distribution: 'temurin'
          java-version: '17'

      - name: Build with Maven
        run: mvn clean install

      - name: Run tests
        run: mvn test
```

**New concepts here:**
- `uses:` → instead of running a raw command, this uses a **pre-made Action** someone else built (from the GitHub Actions Marketplace).
  - `actions/checkout@v4` — brings your repo's files onto the runner (without this, the runner has nothing to work with!).
  - `actions/setup-java@v4` — installs a specific JDK version (Spring Boot needs Java to run).
- `with:` → passes extra settings/parameters to an action (here, which JDK distribution and Java version to use).
- `mvn clean install` → Maven command that compiles your code, packages it, and runs tests as part of the build.
- `mvn test` → runs just the test suite (useful as a separate, explicit step so failures are easy to spot in the logs).

> **Using Gradle instead of Maven?** Just swap the run commands:
> ```yaml
>       - name: Build with Gradle
>         run: ./gradlew build
> ```

This same pattern (checkout → setup language → install deps/build → test) works for almost **any** language — just swap `setup-java` for `setup-node`, `setup-python`, etc.

---

## 9. Example: Trigger on Pull Requests too

You can trigger workflows on more than just pushes:

```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
```

This means: run the pipeline whenever someone pushes to `main`, **or** opens a pull request targeting `main`. Very useful — it checks code *before* it's even merged.

---

## 10. Where to See Your Pipeline Run

1. Go to your repository on GitHub.
2. Click the **"Actions"** tab at the top.
3. You'll see a list of workflow runs.
4. Click on one to see logs for each step — great for debugging if something fails (red ❌) or succeeds (green ✅).

---

## 11. Simple Roadmap to Learn CI/CD with GitHub Actions

1. ✅ Write a basic "Hello World" workflow (like Section 7) — just to see it run.
2. ✅ Add a real project (Node.js, Python, etc.) and auto-run its tests on push.
3. ✅ Learn to trigger on `pull_request` as well as `push`.
4. ✅ Use `uses:` to explore the [GitHub Actions Marketplace](https://github.com/marketplace?type=actions) (linting, formatting, security checks, etc.)
5. ✅ Add a build step (e.g., `npm run build`, or Docker build).
6. ✅ Try deploying somewhere simple (e.g., GitHub Pages, or a free host like Render/Vercel) using an action.
7. ✅ Learn about **secrets** (`Settings → Secrets and variables`) to safely store API keys/passwords used in your pipeline.
8. ✅ Explore matrix builds (running tests on multiple OS/versions at once) — more advanced.

---

## 12. Quick Cheat Sheet

```yaml
name: Workflow Name          # Label

on: push                     # Trigger (push, pull_request, schedule, workflow_dispatch, etc.)

jobs:
  job-id:                    # Any name you choose
    runs-on: ubuntu-latest   # OS: ubuntu-latest / windows-latest / macos-latest
    steps:
      - name: Step label
        uses: some/action@v1 # Use a pre-built action
        with:
          key: value          # Options for that action

      - name: Another step
        run: echo "Or run raw shell commands"
```

**Common triggers:**
- `push` → on every push
- `pull_request` → on PR open/update
- `schedule` → run on a timer (like a cron job)
- `workflow_dispatch` → lets you manually click "Run workflow" in GitHub UI

---

## 13. Summary (TL;DR)

- **CI/CD = automation for testing, building, and deploying code.**
- **CI** catches bugs early by testing every change automatically.
- **CD** automatically prepares (or fully deploys) your app once it passes tests.
- **GitHub Actions** is GitHub's free, built-in CI/CD tool — just add a `.yml` file inside `.github/workflows/`.
- A workflow = triggers (`on`) + jobs + steps.
- Start simple ("Hello World" pipeline), then grow into real testing and deployment pipelines.

---

*Happy learning! The best way to really get this is to make a tiny GitHub repo right now and try the Section 7 example yourself.*