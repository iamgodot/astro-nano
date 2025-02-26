---
title: Developing Obsidian Plugins
description: How I developed and published my first Obsidian plugin
date: 2025-02-18 17:09
draft: false
---
As a huge fan of Obsidian, I've been using it as my primary note-taking tool for years. Recently I finally decided to develop my first plugin for it, here I'll share a few things about the experience.

## Getting Started with Obsidian Plugin Development

Obsidian plugins are built using TypeScript, which provides type safety and improved developer experience. Before diving into development, you'll need to set up your environment:

```bash
# Clone the sample plugin repository
git clone https://github.com/obsidianmd/obsidian-sample-plugin.git my-plugin

# Navigate to the plugin directory
cd my-plugin

# Install dependencies
npm install

# Build the plugin
npm run build
```

The sample plugin provides a solid foundation with the necessary structure and configuration files:

- `main.ts`: The entry point for your plugin
- `manifest.json`: Plugin metadata and version information
- `styles.css`: Custom CSS for your plugin's UI components
- `tsconfig.json`: TypeScript configuration

## Understanding Obsidian's Plugin Architecture

Obsidian plugins extend the `Plugin` class from the Obsidian API. The lifecycle of a plugin revolves around two main methods:

```typescript
import { Plugin } from 'obsidian';

export default class MyPlugin extends Plugin {
  // Called when the plugin is enabled
  async onload() {
    console.log('Loading my plugin');
    
    // Register functionality here
    
    // Add a ribbon icon
    this.addRibbonIcon('dice', 'My Plugin', () => {
      // Action when icon is clicked
    });
    
    // Add a command
    this.addCommand({
      id: 'open-my-plugin-modal',
      name: 'Open My Plugin Modal',
      callback: () => {
        // Command action
      }
    });
  }

  // Called when the plugin is disabled
  onunload() {
    console.log('Unloading my plugin');
    // Clean up resources
  }
}
```

## Key Plugin Capabilities

Through my development experience, I've identified several powerful capabilities the API offers:

### 1. Adding UI Elements

```typescript
// Add a ribbon icon
this.addRibbonIcon('pencil', 'Create new note', () => {
  // Create new note action
});

// Add a status bar item
const statusBarItem = this.addStatusBarItem();
statusBarItem.setText('My Plugin Status');

// Register a settings tab
this.addSettingTab(new MyPluginSettingTab(this.app, this));
```

### 2. Registering Commands

Commands allow users to execute plugin functions via the command palette:

```typescript
this.addCommand({
  id: 'insert-template',
  name: 'Insert Template',
  // Add optional hotkey
  hotkeys: [{ modifiers: ["Ctrl"], key: "T" }],
  editorCallback: (editor) => {
    // Manipulate the editor
    editor.replaceSelection("Your template text");
  }
});
```

### 3. Working with the Editor

Many plugins enhance the editing experience:

```typescript
// Get the active editor
const editor = this.app.workspace.getActiveViewOfType(MarkdownView)?.editor;
if (editor) {
  // Get cursor position
  const cursor = editor.getCursor();
  
  // Insert text at cursor
  editor.replaceRange("Text to insert", cursor);
  
  // Get selected text
  const selection = editor.getSelection();
  
  // Replace selection
  editor.replaceSelection(`**${selection}**`);
}
```

### 4. Interacting with Files

```typescript
// Access the vault
const { vault } = this.app;

// Create a new note
await vault.create('New Note.md', 'Note content');

// Read a file's content
const content = await vault.read(file);

// Modify a file
await vault.modify(file, 'New content');
```

### 5. Creating Custom Views

For more complex plugins, you can create custom views:

```typescript
// Register a view
this.registerView(
  VIEW_TYPE_EXAMPLE,
  (leaf) => new ExampleView(leaf)
);

// Create the view
this.app.workspace.getRightLeaf(false).setViewState({
  type: VIEW_TYPE_EXAMPLE,
  active: true,
});
```

## State Management and Settings

Most non-trivial plugins need to persist settings:

```typescript
// Define settings interface
interface MyPluginSettings {
  templateFolder: string;
  defaultTemplate: string;
}

// Default settings
const DEFAULT_SETTINGS: MyPluginSettings = {
  templateFolder: 'templates',
  defaultTemplate: 'default'
};

// Load settings in onload()
await this.loadSettings();

// Save settings
async saveSettings() {
  await this.saveData(this.settings);
}
```

## Testing and Debugging

It's essential to make a test vault and test the plugin in that particular vault instead of your own one:

1. Build your plugin: `npm run build`
2. Create a test vault
3. Create a `.obsidian/plugins/my-plugin` directory
4. Copy your `main.js`, `manifest.json`, and `styles.css` files
5. Enable the plugin in Obsidian settings

For debugging, currently I just use the Developer Console(Ctrl+Shift+I) for log inspection. Additionally, this [hot reload plugin](https://github.com/pjeby/hot-reload) is really handy for auto-updating with the code changes.

## Publishing Your Plugin

Once your plugin is ready for sharing:

1. Ensure your manifest.json is complete
2. Host your plugin on GitHub
3. Submit it to the Community Plugins directory by making a pull request to the [obsidian-releases](https://github.com/obsidianmd/obsidian-releases) repository

One thing to know is that the process takes some time to get through, for me it takes a month to finally get mine merged. If you got stuck for too long, it could be helpful to ask for help in their Discord channel.

At the end, I'm happy to share my first Obsidian plugin: [Claude Assistant for Obsidian](https://github.com/iamgodot/CAO). It leverages on Claude to chat within your notes directly. There're still tons of features I'm developing to add, so welcome to take a look and let me know your ideas!

---

### References

- [Obsidian - Build a plugin](https://docs.obsidian.md/Plugins/Getting+started/Build+a+plugin)
- [Obsidian Sample Plugin](https://github.com/obsidianmd/obsidian-sample-plugin)
- [Create Obsidian Plugins](https://www.youtube.com/watch?v=AgXa03ZxJ88&ab_channel=FlorianLudewig)