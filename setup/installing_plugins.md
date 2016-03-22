# Installing plugins

Currently there is only a limited number of plugins available, as the core API is subject to change and it'd be a lot of work to continuously migrate them.

The following is a list of the "official" plugins.

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

## hive-plugin-my-documents
A plugin that adds owner and read/write privileges and a management page for your documents. Currently still very basic.

Install with `npm i hive-plugin-my-documents`