# [lazyvim] Home

## Installation

### Common

```bash
git clone git@github.com/ysl2/lazyvim.git ~/.config/nvim

# For basic environmet
brew install rustup nodejs golang fzf fd ripgrep chafa

# For tree-sitter (Optional, only needed if you encountered GLIBC compile error)
rustup default stable
cargo install --locked tree-sitter-cli
:MasonUninstall tree-sitter-cli  # Run this in nvim

# (No need anymore) For blink.cmp
# rustup toolchain install nightly

# NOTE: Don't install xsel, it might be conflict with xclip (xclip is needed by img-clip.nvim)
# sudo apt install -y xsel  # For clipboard support
# You should install xclip instead of xsel
brew install xclip  # Linux only
# NOTE: If you are in macOS:
brew install pngpaste  # macOS only
```

NOTE: `custom = true`: This means that the plugin is added by myself, not by lazyvim.

### For treesitter's download network problem

```bash
cd ~/.local/share/nvim/lazy/nvim-treesitter/lua/nvim-treesitter
```

```diff
diff --git a/lua/nvim-treesitter/install.lua b/lua/nvim-treesitter/install.lua
index 71d5a3c7..58331c93 100644
--- a/lua/nvim-treesitter/install.lua
+++ b/lua/nvim-treesitter/install.lua
@@ -229,7 +229,18 @@ local function do_download(logger, url, project_name, cache_dir, revision, outpu
   a.schedule()

   url = url:gsub('.git$', '')
-  local target = string.format('%s/archive/%s.tar.gz', url, revision)
+  -- local target = string.format('%s/archive/%s.tar.gz', url, revision)
+  local target
+  if url:match("^https://github%.com/") then
+    local repo = url:gsub("^https://github%.com/", "")
+    target = string.format(
+      "https://codeload.github.com/%s/tar.gz/%s",
+      repo,
+      revision
+    )
+  else
+    target = string.format("%s/archive/%s.tar.gz", url, revision)
+  end

   local tarball_path = fs.joinpath(cache_dir, project_name .. '.tar.gz')
```

### For windows specific

```bash
git clone git@github.com:ysl2/starter.git 'C:\Users\Songli Yu\AppData\Local\nvim'
```

## firenvim

For firefox, `ctrl + shift + 6`:

<img src=".assets/!Home/img/2025-07-13-16-45-58.png" alt="" width=100%>

<img src=".assets/!Home/img/2025-07-13-16-46-39.png" alt="" width=100%>

## vimtex

### xelatex

The default latex compiler is latexmk, the default compile engine is pdflatex.

If you want to use `xelatex` (for Chinese support) as compile engine in specific project, you should add a `.latexmkrc` in your project root, then add this into this `.latexmkrc`:

```lua
$pdf_mode = 5;
```

This tells latexmk to use `xelatex` as this project's compile engine. the mode 5 represents xelatex. Check vimtex's help doc for more info.
