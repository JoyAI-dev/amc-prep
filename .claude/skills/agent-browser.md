# agent-browser skill

Browser automation for AI agents using agent-browser CLI.

## Setup

Before using, ensure agent-browser is installed:

```bash
which agent-browser || npm install -g agent-browser
```

If this is the first time, install browser binaries:

```bash
agent-browser install
```

## Usage

agent-browser is a fast browser automation CLI. Use it via Bash tool calls.

### Core workflow pattern

1. **Open a page**: `agent-browser open <url>`
2. **Wait for load**: `agent-browser wait --load networkidle`
3. **Get interactive elements**: `agent-browser snapshot -i` (returns accessibility tree with `@ref` identifiers)
4. **Interact**: Use `@ref` from snapshot to click, fill, type, etc.
5. **Verify**: Take screenshot or snapshot after actions

### Common commands

```bash
# Navigate
agent-browser open <url>
agent-browser back
agent-browser forward
agent-browser reload

# Inspect page (AI-friendly)
agent-browser snapshot -i                    # Interactive elements with refs
agent-browser snapshot -i -c                 # Compact interactive snapshot
agent-browser snapshot -s "css-selector"     # Scoped snapshot

# Interact using @ref from snapshot
agent-browser click @e2
agent-browser fill @e3 "text"
agent-browser type @e3 "text"
agent-browser press Enter
agent-browser select @e5 "option-value"
agent-browser check @e4
agent-browser uncheck @e4

# Interact using CSS selectors
agent-browser click "button.submit"
agent-browser fill "#email" "user@example.com"

# Keyboard
agent-browser press Enter
agent-browser press Tab
agent-browser press Control+a
agent-browser keyboard type "some text"

# Get information
agent-browser get text @e1
agent-browser get html @e1
agent-browser get value @e1
agent-browser get title
agent-browser get url
agent-browser get count "li.item"
agent-browser is visible @e1
agent-browser is enabled @e1

# Screenshots
agent-browser screenshot                    # Current viewport
agent-browser screenshot --full             # Full page
agent-browser screenshot --annotate         # Labeled with numbered elements
agent-browser screenshot path/to/file.png

# Wait
agent-browser wait @e1                      # Wait for element
agent-browser wait 2000                     # Wait ms
agent-browser wait --load networkidle       # Wait for network idle

# Scroll
agent-browser scroll down
agent-browser scroll down 500
agent-browser scrollintoview @e10

# Find elements by semantic role/text
agent-browser find role button click --name Submit
agent-browser find text "Sign In" click
agent-browser find placeholder "Email" fill "user@example.com"

# Tabs
agent-browser tab list
agent-browser tab new
agent-browser tab 2
agent-browser tab close

# File operations
agent-browser upload @e1 ./file.pdf
agent-browser download @e2 ./downloads/
agent-browser pdf page.pdf
```

### Command chaining

Chain commands with `&&` (browser persists via daemon within a session):

```bash
agent-browser open example.com && agent-browser wait --load networkidle && agent-browser snapshot -i
agent-browser fill @e1 "user@example.com" && agent-browser fill @e2 "pass" && agent-browser click @e3
```

### Sessions

Use `--session <name>` to isolate browser sessions:

```bash
agent-browser --session mytest open example.com
```

### Debugging

```bash
agent-browser console              # View console logs
agent-browser errors               # View page errors
agent-browser highlight @e1        # Highlight element
agent-browser --headed open url    # Show browser window
agent-browser diff snapshot        # Compare current vs last snapshot
```

## Best practices

- Always use `snapshot -i` to discover interactive elements before clicking/filling
- Use `@ref` identifiers from snapshots rather than guessing selectors
- Chain related commands with `&&` for efficiency
- Use `wait --load networkidle` after opening URLs or submitting forms
- Take screenshots to verify visual state when needed
- Use `--annotate` flag on screenshots for labeled element identification
- Use `--session` to keep browser contexts isolated between tasks
