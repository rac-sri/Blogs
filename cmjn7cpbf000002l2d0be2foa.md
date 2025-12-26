---
title: "My NeoVim Configuration in the age of AI"
datePublished: Fri Dec 26 2025 18:25:46 GMT+0000 (Coordinated Universal Time)
cuid: cmjn7cpbf000002l2d0be2foa
slug: my-neovim-configuration-in-the-age-of-ai
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1766770858461/b2c4734f-6964-4412-96cc-14706ad2d95e.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1766773518378/1941e92c-7f4d-4a20-92bb-39580e9e280d.png
tags: ai, neovim, opencode

---

Switching to Neovim is a rite of passage for many developers. It's not just about using a terminal editor; it's about crafting a Personalized Development Environment (PDE) that moves at the speed of thought. In this post, I'll walk you through my current Neovim setup, explaining the core components, the visual aesthetics, the coding engine, and the AI superpowers I've integrated. I cover OpenCode independent configuration in a separate section. You can find the full configuration here:

* [https://github.com/rac-sri/neovim-config](https://github.com/rac-sri/neovim-config) - NeoVim Config
    
* [https://github.com/rac-sri/opencode-config](https://github.com/rac-sri/opencode-config) - OpenCode Config
    

Here is a tour of the plugins, the architecture, and the logic behind the chaos.

---

## NeoVim Setup

### 1\. What are Plugins in Neovim? (And Why `lazy.nvim`?)

Neovim out of the box is a powerful text editor, but it's "bare bones." It doesn't ship with a file explorer, autocompletion, or Git integration enabled by default. **Plugins** are Lua scripts that add these features, essentially turning Neovim from a text editor into a full PDE.

Managing dozens of plugins used to be a nightmare of slow startup times and broken dependencies. Enter [**lazy.nvim**](https://github.com/folke/lazy.nvim).

I use `lazy.nvim` as my package manager because, as the name implies, it's lazy. It doesn't load every plugin when I launch Neovim. Instead, it waits.

* Want to use Git? It loads the Git plugin *only* when you open a Git-tracked file.
    
* Want to code in Rust? It loads the Rust LSP *only* when you open a `.rs` file.
    

This keeps my startup time blazing fast (&lt; 100ms) while still having a massive suite of tools at my disposal.

---

### 2\. My Plugin Arsenal

Here is the complete list of plugins powering my setup, broken down by category.

### UI & Aesthetics

Because if you're going to stare at a screen all day, it better look good.

* [**nord.nvim**](https://github.com/shaunsingh/nord.nvim): My theme of choice. A cool, bluish-grey palette that is easy on the eyes, configured with transparency support.
    
* [**lualine.nvim**](https://github.com/nvim-lualine/lualine.nvim): A sleek, configurable status line at the bottom of the window showing branch, mode, and diagnostics.
    
* [**bufferline.nvim**](https://github.com/akinsho/bufferline.nvim): Displays open files as "tabs" at the top, making navigation intuitive for those coming from VS Code.
    
* [**alpha-nvim**](https://github.com/goolord/alpha-nvim): A programmable startup dashboard that greets me with a custom header and quick links to recent files.
    
* [**indent-blankline.nvim**](https://github.com/lukas-reineke/indent-blankline.nvim): Adds vertical indentation guides to help visually track code blocks and nested loops.
    
* [**nvim-scrollbar**](https://github.com/petertriho/nvim-scrollbar): Adds a visual scrollbar that also highlights search results and diagnostic errors in the gutter.
    
* [**smear-cursor.nvim**](https://github.com/sphamba/smear-cursor.nvim): Adds a motion blur trail to the cursor. Purely cosmetic, but it makes jumping around the file feel incredibly smooth.
    
* [**nvim-colorizer.lua**](https://github.com/norcalli/nvim-colorizer.lua): Automatically highlights hex codes (like `#ff0000`) with their actual color.
    

### Coding Intelligence

The engine that understands the code.

* [**nvim-lspconfig**](https://github.com/neovim/nvim-lspconfig): The backbone of language support. It connects Neovim to Language Servers (LSP) for Python, Rust, TS, etc.
    
* [**nvim-cmp**](https://github.com/hrsh7th/nvim-cmp): The main autocompletion engine. It pops up the menu suggesting variables and functions as I type.
    
* [**blink.cmp**](https://github.com/saghen/blink.cmp): A high-performance, Rust-based completion engine I'm experimenting with for even faster suggestions.
    
* [**nvim-treesitter**](https://github.com/nvim-treesitter/nvim-treesitter): Provides advanced syntax highlighting and code parsing, allowing for smarter text selection (like "select function").
    
* [**none-ls.nvim**](https://github.com/nvimtools/none-ls.nvim): Injects standard command-line tools (like Prettier or ESLint) into the LSP workflow for formatting and linting.
    
* [**nvim-autopairs**](https://github.com/windwp/nvim-autopairs): Automatically closes brackets, quotes, and parentheses so I don't have to.
    

### Navigation & Workflow

Tools to move fast and break less things.

* [**telescope.nvim**](https://github.com/nvim-telescope/telescope.nvim): A universal fuzzy finder. I use it to find files, grep text, search help docs, and more.
    
* [**neo-tree.nvim**](https://github.com/nvim-neo-tree/neo-tree.nvim): A file explorer sidebar. Useful for visualizing the directory structure and performing file operations. I don’’t primarily use it, just a nice to have because of seemless integration with git from a visual perspective.
    
* [**yazi.nvim**](https://github.com/mikavilpas/yazi.nvim): Integrates the `yazi` terminal file manager, giving me a super-fast, keyboard-driven way to browse files. Primary tool to navigate through files.
    
* [**gitsigns.nvim**](https://github.com/lewis6991/gitsigns.nvim): Shows Git changes (added/removed lines) right in the gutter and allows for inline previews.
    
* [**vim-tmux-navigator**](https://github.com/christoomey/vim-tmux-navigator): Allows seamless navigation between Neovim splits and Tmux panes using the same keys (`Ctrl-h/j/k/l`).
    
* [**comment.nvim**](https://github.com/numToStr/Comment.nvim): Toggles comments on lines or blocks with a simple shortcut (`gcc` or `gc`).
    
* [**todo-comments.nvim**](https://github.com/folke/todo-comments.nvim): Highlights `TODO`, `FIXME`, and `NOTE` comments so they stand out and can be searched.
    

### The AI Squad

I lean heavily on AI for coding assistance, using multiple specialized tools rather than a single monolith.

* [**opencode.nvim**](https://github.com/NickvanDyke/opencode.nvim): My "Agentic" AI tool. It enables inline asking (`<leader>oi`) and complex task execution via a panel, integrating deep context from the codebase. I use local model: qwen3-coder when data privacy is important, and else I am currently using gemini models since I got access to it’s pro subscription.
    
* [**vim-tabby**](https://github.com/TabbyML/vim-tabby): Provides ML-powered code completion (ghost text) using a self-hosted Tabby server, keeping my data private.
    
* [**copilot.vim**](https://github.com/github/copilot.vim): The classic GitHub Copilot integration. I keep this for quick tab style completion in places where privacy is not a concern..
    
* [**avante.nvim**](https://github.com/yetone/avante.nvim) **( disabled )**: An advanced AI chat interface that sits in a sidebar. It supports multiple providers (Gemini, Claude, OpenAI) and even allows pasting images for analysis.
    
* [**gemini-cli.nvim**](https://github.com/marcinjahn/gemini-cli.nvim) **( disabled )**: A direct line to Google's Gemini models for quick questions and code generation directly from the editor.
    

---

### 3\. The `init.lua`: Where It All Begins

In the world of Neovim, `init.lua` is the captain. It is the first file loaded when the editor starts, and its job is to orchestrate everything else.

My `init.lua` is designed to be modular. Instead of dumping 500 lines of config into one file, it acts as a dispatcher:

1. **Core Loading**: First, it requires modules from the `lua/core/` folder.
    
    * `core.options`: Sets basic settings like line numbers, tab widths, and clipboard behavior.
        
    * `core.keymaps`: Defines my global keyboard shortcuts (like Space as Leader).
        
    * `core.snippets`: Loads custom code snippets.
        
2. **Lazy Bootstrap**: It checks if `lazy.nvim` is installed. If not, it automatically clones the repository. This means I can pull this config on a fresh machine, open Neovim, and it will self-install everything.
    
3. **Plugin Initialization**: Finally, it passes control to `lazy`.
    
    ```lua
    require("lazy").setup({
        require("plugins.neotree"),
        require("plugins.colortheme"),
        -- ... and so on
    })
    ```
    
    Each `require` points to a separate file in `lua/plugins/`. This keeps the config clean; if I want to change how my status line looks, I go to `lua/plugins/lualine.lua`, not a massive monolithic file.
    

---

## 🤖 OpenCode: The AI Orchestrator

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1766773444604/b15e59a5-25de-4ec7-86b9-780022e8df1c.png align="center")

Right after my Neovim configuration comes the brain of the operation: OpenCode. If Neovim is the surgical instrument, OpenCode is the senior engineer looking over my shoulder.

I use a opensource setup called [OhMyOpenCode](https://github.com/code-yeongyu/oh-my-opencode/), heavily customized to switch between local privacy and cloud intelligence.

**The "Sisyphus" Workflow:** The primary agent is named Sisyphus (a fitting name for an engineer, right?). Unlike standard AI assistants, Sisyphus is configured as an orchestrator that delegates tasks to specialized sub-agents. Here is how I map the intelligence in `~/.config/opencode/oh-my-opencode.json`:

```json
{ 
    agents: { 
        Sisyphus: { 
            model: google/gemini-3-pro-high 
        },     
        oracle: { 
            model: google/gemini-3-pro-high 
        }, 
        librarian: { 
            model: google/gemini-3-pro-low 
        }, 
        document-writer: { 
            model: google/gemini-3-flash 
        } 
    }     
}
```

I'm using a hybrid model strategy:

* Heavy Lifting: Complex architecture and planning (Sisyphus/Oracle) go to Gemini 3 Pro High ( as of writing ) with reasoning enabled.
    
* Speed: Documentation and quick lookups use Gemini 3 Flash or Pro Low.
    
* Local: My default fallback in `opencode.json` is Qwen 2.5 Coder (via Ollama) for zero-latency, offline code completion.
    

**Context & Rules**

One feature I rely on is the dynamic context loading. OpenCode automatically ingests my project guidelines before writing a single line of code:

```json
instructions: [ 
    CONTRIBUTING.md, 
    docs/guidelines.md, 
    .cursor/rules/*.md 
]
```

This ensures the AI respects my repo's specific conventions (like "no any types" or "React functional components only") without me repeating prompts.

**Aesthetics**

Since I live in the terminal, visual consistency is non-negotiable. I built a custom transparent theme so OpenCode floats seamlessly over my terminal background, matching the transparency of my Neovim windows.

```json
 // themes/transparent.json 
{ 
    theme: { 
        background: transparent, 
        backgroundPanel: transparent, 
        primary: #00d4ff, 
        success: #2ed573 
    } 
}
```

It’s not just about looking cool—it keeps me in the flow state without jarring context switches between my editor and my AI agent.

### **Custom Primary Agents**

**Custom Agents** One of the most powerful features of OpenCode is the ability to define custom "personas" or agents. I don’t just have one generic AI; I have a squad of specialists, each defined in `~/.config/opencode/agent/`. I’ve defined two custom agents to handle specific non-coding workflows:

1. **The "Ask" Agent (Code Consultant)**
    
    Sometimes I just need an answer, not a code change. I created the Ask agent (agent/[Ask.md](http://Ask.md)) specifically for this.
    
    * **Role**: Code Q&A Specialist.
        
    * **Constraint**: Strictly Read-Only. It has explicit instructions to never edit files or run commands.
        
    * **Use Case:** "How does this function work?" or "Explain this error." It uses Gemini 3 Pro Low to be cost-effective but smarter than a basic model.
        
2. **The "Webcrawler" Agent (Researcher)**
    
    I don't want my coding assistant hallucinating facts about the world. Webcrawler (agent/[Webcrawler.md](http://Webcrawler.md)) is my general knowledge researcher.
    
    * **Role**: General Q&A (News, Science, Trends).
        
    * **Constraint**: Explicitly forbidden from giving technical coding advice—it redirects those to the Ask agent.
        
    * **Use Case:** "What are the latest features in React 19?" or general web research.
        

---

## Terminal - Ghostty

I use [Ghostty](https://ghostty.org/) as my primary terminal with the following config:

[https://github.com/rac-sri/personal-config/blob/main/ghostty.config](https://github.com/rac-sri/personal-config/blob/main/ghostty.config).  
Main appearance limited config is below:

```yaml
# ---- Appearance ----
theme = Adventure
background-blur = true
background-opacity = 1
background-image = ~/.config/ghostty/image.png
background-image-opacity = 0.5
....
```

with the image being a free to use artwork I pulled from the internet : [https://github.com/rac-sri/personal-config/blob/main/image.png](https://github.com/rac-sri/personal-config/blob/main/image.png).

---

That's the tour! This setup balances raw speed with modern features, giving me an environment that feels like *mine*.