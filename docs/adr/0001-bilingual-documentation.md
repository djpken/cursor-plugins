# Keep Traditional Chinese documentation beside the upstream files

This fork uses `zh-TW` as its default branch and carries standard written Taiwan Traditional Chinese documentation in `*.zh-TW.md` sibling files. English files remain the source of truth. The only English README edit is a navigation banner linking to the Chinese README and document index.

Translate the README files, every `SKILL.md`, their Markdown reference documents, and reader-facing guides. Preserve frontmatter keys other than `description`, including `name`, byte for byte. Preserve code blocks, inline code, commands, paths, URLs, and plugin manifests. Do not translate `AGENTS.md`, `CLAUDE.md`, `RELEASE-NOTES.md`, or changelogs.

Sibling files keep the executable skill sources stable and make upstream merges easier. Reference links retain their English destinations for source comparison; the Chinese index links directly to translated documents. Chinese headings retain explicit anchors for the original English headings so existing fragment links continue to work.

`harness/pstack` is generated. Translate the corresponding `pstack` sources and run `node scripts/generate-pstack-harness-trees.mjs`; do not maintain independent translations in the generated tree or change the generator to support localization.

Translations are machine-assisted. Verify source coverage, invocation names, frontmatter, code, link destinations, and Taiwan written register before publishing. Update a sibling whenever its English source changes; the English source governs if their meanings differ.
