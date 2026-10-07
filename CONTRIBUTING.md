# Contribution Guidelines

Please note that this project is released with a [Contributor Code of Conduct](https://github.com/sindresorhus/awesome/blob/main/code-of-conduct.md). By participating in this project you agree to abide by its terms.

## What belongs here

A project fits the list when all of the following are true:

- It has an implementation under an [OSI-approved license](https://opensource.org/licenses). An open client SDK for a closed service does not qualify.
- It documents a configuration in which object storage holds authoritative state, retained records, indexes, or transactional datasets used for serving or recovery.
- The documented capability is released. Announced or roadmap features can be mentioned in the comparison, but do not qualify a project on their own.
- It is maintained and documented. Early or alpha projects can be listed if their status is stated; projects with explicit support or validation gaps go in [EXPERIMENTAL.md](EXPERIMENTAL.md).

Backup-only tools and general-purpose tools that read or write files on object storage belong under Related Tools. Papers, talks, and design write-ups belong under Articles and References.

## Adding a project

1. Search the list and the open issues and pull requests to avoid duplicates.
2. Add one line to the most relevant section of `README.md`, at the position that keeps the section in alphabetical order:

   ```md
   - [Project Name](https://github.com/owner/repo) - Short description of what it does.
   ```

   The description starts with an uppercase letter, ends with a period, does not repeat the project name, and avoids marketing language.

3. Add a row to the matching table in `COMPARISON.md`, with:
   - Backends the project itself documents or implements. Do not copy the backend list of a storage library it depends on.
   - The SPDX license identifier of the named implementation and its license type. Note any open-core or per-directory boundaries.
   - What the project keeps in object storage.
   - Required services and acknowledgment caveats, when documented. Write "not established" rather than guessing.
   - Release status and links to primary sources: the repository, documentation, or source code.
4. Run `npm install` and `npm run lint`, and fix any errors.
5. Open a pull request with one project per pull request, explaining why it belongs on the list.

You can also [open an issue](https://github.com/elyase/awesome-object-storage-native/issues/new?template=proposal.md) to propose a project.

## Updating or removing a project

Corrections are welcome. Link the source that shows the change, such as a new license, a renamed or archived repository, a released feature, or a changed backend list.
