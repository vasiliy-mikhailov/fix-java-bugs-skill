# fix-java-bugs-skill

Portable [Agent Skills](https://agentskills.io/) that make a coding agent **find and fix real bugs in a Java
(Maven or Gradle) project, one file at a time**: read a source file, prove each suspected bug with a test that
**fails on the current code**, then fix the **root cause** so the test passes — **never a workaround**, and
under **the JDK the project actually needs**. Standard tools only (the build, `git`); no project-specific
scripts, since the skills are a hand manual your agent reads and follows.

Reading code for bugs is cheap and unreliable; a failing test that then goes green on your fix is proof. These
skills turn a suspicion into a *proven, fixed* bug backed by a regression test.

## The three skills

- **`detect-java-version`**: determine which JDK the project actually needs to *build* (not just its declared
  bytecode target, since codegen / annotation processors can require a newer JDK), and run every command under
  it. A forked test JVM crashes under the wrong JDK.
- **`detect-unit-testing-framework`**: determine which unit-testing framework the project uses (JUnit 4, 5, 6,
  TestNG) and how it is wired into the build, so the proving test is written in the right style.
  `fix-java-bugs` depends on this.
- **`fix-java-bugs`**: read one source file, flag concrete suspected bugs (wrong comparison, off-by-one,
  copy-paste slip, broken contract, resource leak, mishandled edge case), **prove** each with a `@Test` that
  asserts the correct behaviour and so fails on the current code, then **fix the root cause** (never a
  guard / try-catch / workaround) until the test is green and the module's tests still pass. Includes a
  not-a-bug discipline and a `0.9^penalty` line-counted mergeability rubric so the change is clean enough to
  upstream.

## Install

**Claude Code**: add this repo as a plugin marketplace, then install:
```
/plugin marketplace add vasiliy-mikhailov/fix-java-bugs-skill
/plugin install fix-java-bugs
```

**OpenHands**: copy the skills where it reads them:
```
mkdir -p <your-project>/.openhands/skills
cp -r skills/detect-java-version skills/detect-unit-testing-framework skills/fix-java-bugs <your-project>/.openhands/skills/
```

**opencode**: drop the skills in and reference them from your `AGENTS.md`.

**Kilo Code**: copy the skills in, or install from the Kilo Marketplace.

**Any agent**: point it at the files: *"follow `skills/detect-java-version/SKILL.md`, then
`skills/detect-unit-testing-framework/SKILL.md`, then `skills/fix-java-bugs/SKILL.md` to find and fix the bugs
in this file."*

## How it works

1. **Detect** the JDK the project needs and run under it.
2. **Read** one source file in full (the entrance point); explore outward only as a bug needs.
3. **Find** concrete suspected bugs — things you can point at, not style nits.
4. **Prove** each with a `@Test` that asserts the correct behaviour and so fails on the current code (or drop it as not-a-bug).
5. **Fix** the root cause — never a workaround — until the test is green and the module stays green.
6. **Open a PR** showing the bug as a failing test, the root cause in the real code, and red -> green.

## How it's built

The skills are written as a self-contained procedure and validated by running them with different coding
agents on the same model, so the instructions (not one agent's quirks) carry the result. The procedure and its
gotcha catalogue are hardened as new failure modes surface.

## License

MIT, see [LICENSE](LICENSE).
