# The end-to-end scenario, with Livela as the tracker

Three parts, run in order, after the setup: (a) a ticket goes from Ready to merged and To Deploy; (b) a release cycle ships it and closes it Done; (c) a PR merged to `main` while a cycle is open appears in the next release's notes. It is `spl-asdlc-sandbox`'s scenario, with each tracker gesture made in Livela instead of Linear's API, and each step a person takes made from Livela or from the GitHub page Livela links to. There is no `spl-asdlc` command, no `gh`, no shell on the box.

The bridge is Livela's runner on the box (`livela-runner serve`): for each project that has SPL-ASDLC on, it runs at each tick (every 5 minutes) its `doctor`, the bridge and the GitHub → item sync (Livela's `docs/business-rules.md`, BR-LV-069 and BR-LV-070). It runs the `spl-asdlc` version Livela embeds, and this repository's workflow callers name the same minor line (`@vX.Y`). The method's gestures and what follows each are spl-asdlc's `docs/setup/manual-gestures.md`; the statuses are its `docs/reference/03-vocabulary.md`.

**Every Livela surface is closed** while the bridge is expected to act: the web app, the desktop app, the Mac and the iPhone. The runner on the box is the only Livela process running. A surface is opened only to make a person's gesture, or to look once the expected behaviour should have happened, and is closed again. The agent of part (a) talks to the box's MCP (the local listener, with an agent key: Livela's `docs/REMOTE_MCP.md`), never to a Mac's bundled MCP.

A step whose expected behaviour does not happen is a **finding**. Note it in the run journal, with what Livela and GitHub show, and open a ticket in the project it belongs to: spl-asdlc, or Livela. Link it from SID-1254. The run resumes once the fix is released and on the box.

The names this page uses:

- `<owner>/spl-asdlc-livela-sandbox`: this repository; the product `spl-asdlc-livela-sandbox` (the guided setup's default, from the repository's name). The tags are therefore `spl-asdlc-livela-sandbox-v<X.Y.Z>-rc.<N>` and `spl-asdlc-livela-sandbox-v<X.Y.Z>`.
- `LSB`: the prefix of the Livela project « SPL-ASDLC Livela sandbox ». Its tickets are `LSB-<code>`.
- `vX.Y`: the minor line of the `spl-asdlc` Livela embeds (`livela-runner --version`, and the project's page: « Checked … (spl-asdlc X.Y.Z) »).

## 0. The setup, from Livela

The repository starts with the crate and its CI only: no `.spl-asdlc/`, no workflow caller.

The owner, in Livela (one surface, then closed):

1. **The project.** Create a project « SPL-ASDLC Livela sandbox », prefix `LSB`. « Software development » is on in Settings. Give the project its « GitHub repository »: `<owner>/spl-asdlc-livela-sandbox`.
2. **Its settings.** The project's page now has a « SPL-ASDLC » section. Turn it on. Under « Settings »:
   - test command `cargo fmt --all --check && cargo clippy --all-targets --locked -- -D warnings && cargo test --locked`;
   - owner: the owner's participant; owner's GitHub login: the owner's login;
   - nothing else: the light configuration, no PR review angle, no release QA angle.

**Expected, at the runner's next tick** (surfaces closed):

- The runner prepares the setup PR's files.
- It runs `doctor`, which checks the configuration that PR would add.

Then, on any surface:

- « Checked … (spl-asdlc X.Y.Z) »;
- « Install the GitHub App on the repository » done if the App is already installed on the repository, else « to do » with the install link;
- « Create the repository secrets » not checkable;
- « Create the rulesets » to do;
- « Open the setup PR » to do, with its button;
- « doctor green » to do.

Then the guided steps, in order, each from the link Livela gives:

3. **The App.** « Install the App » opens the App's installation page: add this repository to the installation of the `spl-asdlc` App.
4. **The secrets.** « Repository secrets » opens the repository's Actions secrets: `SPL_ASDLC_APP_ID` (the App's id) and `SPL_ASDLC_APP_PRIVATE_KEY` (its private key, the whole PEM).
5. **The rulesets.** « Repository rulesets » opens the repository's rulesets (spl-asdlc's `docs/setup/repository.md`):
   - branch ruleset `main`: target the default branch; require a pull request; require the status checks `required-check` and `title / conventional-title`; block force pushes; bypass list: the `spl-asdlc` App only;
   - branch ruleset `release`: target `refs/heads/release/**`, the same rules and bypass;
   - tag ruleset `tags`: target `refs/tags/spl-asdlc-livela-sandbox-v*`; restrict updates and deletions; bypass list: the `spl-asdlc` App only.
6. **« Check again »** on the project's page.

   **Expected**, at the next tick: « Install the GitHub App » done, « Create the rulesets » done.
7. **The setup PR.**
   - On a surface where the owner is connected to GitHub (Settings › GitHub connection, the Livela GitHub App), « Open the setup PR » shows the files and opens the PR.
   - Livela records its address and shows it in the step.
   - The owner checks the PR on GitHub. Its checks run on a PR from a person, not from the bridge (no ticket to name: a setup PR is the method's bootstrap). The owner merges it on GitHub once `required-check` and the title check are green.

   **Expected, at the next tick after the merge**: `doctor` runs again on the merged configuration. The project's page then shows every step done but the secrets (not checkable), and « doctor is green: the runner moves this project ».
8. **On a second surface** (the iPhone if the first was the web, or the reverse): the same green `doctor`, read from the facts the runner wrote.

Record in the journal: when each step was done, what each « Check again » showed, the setup PR's number.

## (a) A ticket, its PR, To Deploy

### 1. Write the ticket and make it Ready

In Livela, a new item of type Ticket in the project `LSB`, titled « The sandbox says goodbye ». Its description is the ticket contract (spl-asdlc's `docs/setup/templates/ticket.md`):

```markdown
## Goal
The sandbox can say goodbye: `farewell(name)` mirrors `greet`.

## Non-goals
- Any other change to `greet`.

## Acceptance criteria
- [ ] `farewell("sandbox")` returns `Goodbye, sandbox!`, with a unit test.
- [ ] A blank name says goodbye to the world, with a unit test.

## Business rules
None.

## Release note
The sandbox says goodbye.
```

Livela shows it « Refined » (a goal, a criterion, a release note). Move it to **Ready**, leave it unassigned, and close the surface.

**Expected**: nothing from the bridge (Ready is refinement's status, not the bridge's).

### 2. An agent implements it from « what's next »

An agent session (Claude Code, on any machine), connected to the box's MCP:

- `name_session`, then `whats_next`. The ticket is served (Ready, in the active group, unassigned or assigned to it) and locked.
- The agent moves it **In Progress**, as the process page asks (`.spl-asdlc/process.md`, the implementer's verb).
- It implements it in a clone of this repository, on a branch named after the ticket (`claude/lsb-<code>-goodbye`):

  ```rust
  /// The farewell for `name`; a blank name says goodbye to the world.
  pub fn farewell(name: &str) -> String {
      let name = name.trim();
      if name.is_empty() {
          "Goodbye, world!".to_string()
      } else {
          format!("Goodbye, {name}!")
      }
  }

  #[cfg(test)]
  mod farewell_tests {
      use super::farewell;

      #[test]
      fn says_goodbye_to_a_name() {
          assert_eq!(farewell("sandbox"), "Goodbye, sandbox!");
      }

      #[test]
      fn a_blank_name_says_goodbye_to_the_world() {
          assert_eq!(farewell(" "), "Goodbye, world!");
      }
  }
  ```

- It runs the three checks.
- It commits `feat: the sandbox says goodbye (LSB-<code>)` and opens a **draft** PR with that title. The body names the item as `livela://item/LSB-<code>` (process page, step 5).
- No one opens the ticket in Livela.

**Expected, at the next ticks**, every surface closed:

- The runner's GitHub sync **links the PR to the ticket**: a web link to the PR, laid by the `spl-asdlc` participant.
- It records the PR's status on that link (`draft`).
- The PR is a draft, so it is out of the bridge's flow: the ticket stays **In Progress**, where its implementer put it.
- No `pr` job runs on a draft; `pr-title` passes.

Check, by opening a surface afterwards:

- the ticket's activity: the link, signed `spl-asdlc`, and no status change by the bridge;
- its « Linked PRs », or its links on a device not connected to GitHub, showing the status the runner recorded.

### 3. Tick the boxes, mark the PR ready

The contract gates the merge: every box ticked. The agent:

- ticks both acceptance criteria in the ticket's description, through the MCP (`update_item`);
- marks the PR ready for review on GitHub.

**Expected, at the next ticks** (no PR review, `review.angles = []`):

- The ticket goes **In Review** at the first tick that sees the PR ready.
- Once `required-check` is green, the bridge **merges the PR**: a squash pinned to the head SHA, as the App, the branch deleted, with its comment (green, no review angle, merged with no PR review).
- The ticket goes **To Deploy**: a release cycle is configured, so a merged ticket waits for a release; it is never Done here.
- The sync records the PR's status `merged`.

Check afterwards, in Livela:

- the ticket To Deploy;
- its activity: In Review then To Deploy, each written once, by `spl-asdlc`;
- the link's status « merged, not released ».

On GitHub: the PR merged by the App.

## (b) A release cycle

### 4. Launch the release from Livela

On the project's page, « Releases » › « Next release » lists the ticket with its PR and its commit, and the version the cut would compute: indicative, a `feat` bumps the minor before 1.0.

« Launch the release », confirmed, dispatches the cut workflow (`release-cut.yml`, `open`) on `main`. Close the surface.

**Expected on GitHub, with no gesture**:

- `release-cut` downloads the published `spl-asdlc` binary of `vX.Y`, creates `release/<YYYY-MM-DD-HH-MM>-utc` and prepares the version:
  - `Cargo.toml` and `Cargo.lock`;
  - `CHANGELOG.md`, created by this first release;
  - the commit `chore(release): v<version>`;
  - the first candidate `spl-asdlc-livela-sandbox-v<version>-rc.1`.
- The tag starts `release-gate`: the suite (the test command) on the exact candidate. Green.

**Expected from the bridge, next ticks**:

- The **release item**:
  - of type Release, in the project `LSB`;
  - titled with the product and the version;
  - the release marker in its description;
  - its tag `release/spl-asdlc-livela-sandbox-v<version>`, also put on the embarked ticket.
- The candidate recorded on it.
- Once the gate is green, the brief, and the release item's `## Release note` filled in.

Check, by opening a surface afterwards:

- the release item in « Releases » (the left menu) and on the project's page;
- its « Release cycle »: cut done, release CI done, QA skipped (no QA angle), GO waiting or done;
- the ticket still To Deploy, with the release's tag.

### 5. GO: the bridge tags and publishes

**Expected, next tick** once the gate is green:

- The GO is computed: gate green, no blocker, no QA angle, no `hold` label.
- The bridge creates the annotated tag `spl-asdlc-livela-sandbox-v<version>` on the candidate's commit.
- It publishes the GitHub release, its notes the version's CHANGELOG section.
- `release-publish` runs:
  - **verify**;
  - **publish**: prints the version;
  - **close**: the merge-back PR into `main`, merged as a merge commit by the App, the release branch deleted.

Nobody writes a status, person or agent: the release item's steps are what GitHub shows, recorded by Livela.

Check afterwards:

- the release view: every step done, the publication run's jobs as the project's own steps;
- on GitHub: the release, its tag on the candidate's commit, the merge-back PR merged.

### 6. Closure

**Expected, next tick** after the publication and the merge back:

- The embarked ticket, To Deploy with every box ticked, goes **Done**.
- The release note is posted.
- The release item is closed **Done** with its summary, which says who published the release (the App).
- The sync records `released:spl-asdlc-livela-sandbox-v<version>` on the PR's link.
- A second tick writes nothing: no new fact on the ticket nor on the release item.

Check afterwards:

- the ticket Done;
- the release item Done, kept in « Releases » as history;
- the link's status « released in spl-asdlc-livela-sandbox-v<version> »;
- the project's page: « Next release » has nothing new.

## (c) A PR merged to main during a cycle is in the next release's notes

The cut prepares the notes along the git graph, never by date: a PR merged into `main` while a cycle is open is not in that cycle's release, and is in the next one's.

### 7. While the cycle is open, merge a second ticket into main

Between step 4 and step 5 (the release branch exists, its release is not published), run steps 1 to 3 again with a second ticket of the same shape: « The sandbox waves », `wave(name)` returning `Hi, <name>!` and `Hi, world!` for a blank name, with its unit tests.

**Expected**:

- The bridge merges its PR into `main` as in step 3, while the cycle runs on.
- The ticket goes **To Deploy**.
- It is not embarked in the open cycle: no release tag on it, and the release item's brief does not list it.
- The open cycle goes on through steps 5 and 6 untouched.
- At that cycle's closure, the second ticket stays To Deploy.

Check: the « Next release » of the project's page lists the second ticket as going in the release after the one in flight.

### 8. The next cycle lists it

After step 6, launch the next release from Livela as in step 4.

**Expected**:

- The cut prepares `v<next>` from the commits since `v<version>`, which hold the second PR's squash commit.
- The new CHANGELOG section lists that PR under **Features**, and nothing `v<version>` shipped.
- The bridge creates the next release item and puts its tag on the second ticket.

Then finish the cycle as steps 5 and 6: the second ticket goes **Done** at its closure.

## Cleanup

A green run leaves nothing open: no PR, no release branch, the tickets and the release items Done.

- The tags and the GitHub releases stay: the tag ruleset makes them immutable, and the next run releases the next version.
- A later run takes another small `feat` or `fix` ticket of the same shape: a function and its unit tests.

A run stopped midway:

- Close an open delivery PR on GitHub, with its branch.
- Cancel its ticket in Livela.
- A cycle in flight is abandoned with spl-asdlc's `release abandon` on the bridge's host. Livela's release view gives the command. It is the one gesture of the method still on the host: a finding if the scenario ever needs it.

## Run journal

One line per step, in SID-1254 (or here, under this heading):

| Step | Date (UTC) | Gesture (who, where) | What was seen (surface, after closing all) | Finding |
|---|---|---|---|---|
