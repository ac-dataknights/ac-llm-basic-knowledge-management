# Windows Compatibility Rules

You are running on Windows. Ensure all commands and file operations are Windows-compatible:

## Terminal Commands
- Use Windows PowerShell syntax
- Avoid Linux/Unix command chaining with `&&` - use separate commands instead
- Use Windows path separators `\` or relative paths
- Use Windows-compatible commands (e.g., `dir` instead of `ls`, `type` instead of `cat`)

## File Operations
- Use Windows-compatible file paths
- Respect Windows file naming conventions
- Use appropriate Windows file permissions and operations

## Examples
✅ **Correct Windows approach:**
```
mkdir folder1
cd folder1
mkdir subfolder
```

❌ **Incorrect Linux approach:**
```
mkdir folder1 && cd folder1 && mkdir subfolder
```