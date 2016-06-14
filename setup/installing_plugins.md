# Installing plugins

Currently there is only a limited number of plugins available, as the core API is subject to change and it'd be a lot of work to continuously migrate them.

The following is a list of the "official" plugins.

**Please note** that as of v0.8 you need to re-run `hive ui-build` after you install or remove a plugin in addition to re-starting the hive server for the changes to take effect.

## hive-plugin-authorship-markers-ckeditor
A plugin for the CKeditor editor package that adds a thin pane to the left of the editing area where sections are attributed to authors, indicated by colored lines. Integrates with hive-plugin-presence to display users' colors and allow changing your own color.

Install with `npm i hive-plugin-authorship-markers-ckeditor`

## hive-plugin-cursor-broadcast-ckeditor
A plugin for the CKeditor editor package that displays the caret/selection of other users currently editing the same document.

Install with `npm i hive-plugin-cursor-broadcast-ckeditor`

## hive-plugin-cursor-broadcast-codemirror
A plugin for the codemirror editor package that displays the caret/selection of other users currently editing the same document.

Install with `npm i hive-plugin-cursor-broadcast-ckeditor`

## hive-plugin-chat
A plugin for the editor page that allows users to communicate via chat.

Install with `npm i hive-plugin-chat`

## hive-plugin-presence
A plugin for the editor page that allows users to see who else is viewing the current document.

Install with `npm i hive-plugin-presence`

## hive-plugin-import
A plugin for the editor page that allows users to import files into the current document.

Install with `npm i hive-plugin-import`

## hive-plugin-export
A plugin for the editor page that allows users to export and download the current document in various file formats.

Install with `npm i hive-plugin-export`

## hive-plugin-my-documents
A plugin that establishes document ownership and allows owners to manage read/write privileges and adds a management page for your documents. Currently, this is the only way to create new documents.

Install with `npm i hive-plugin-my-documents`