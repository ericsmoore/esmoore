---
title: Kakoune Chronicles III
subtitle: Reading a Plugin
date: 2026-10-17
code: true
---

draft: reading [kakoune_jump_mode][src] by Mathieu Ablasou <taupiqueur.kanto@gmail.com>

## Putting it Together

Last thing called is handle jump input, and it takes text for the prompt and a particular command as a string. This command dictates how the mark genrated by the previous commands is combined with the existing selections. 
There are three main modes. 

### Replace

This is the mode I use primarily. It's ideal for quick navigation with a single cursor. Indicate a position, move to that position. Everything but the main selection is unchanged, provided your jump position doesn't collide with an existing selection. 

```kak
try %{
  execute-keys -save-regs 's' '<esc><a-,>"sZz"s<a-z>a<esc>'
} catch %{
  execute-keys '<esc>z<esc>'
}
```

- `<a-,>` : clear main selection
  - throws if only one selection---just restore with `z`
- `"sZ` : save remaining selections to register `s`
- `z` : restore selection from jump mark
- `"s<a-z>a` : append selections in register `s`

### Append

Append mode is identical to replace but your main selection as well as the secondary selections are also kept and appended to the jump mark. This is a very useful command for building a weird bunch of disconnected selections where regex or other built-in features are inconvenient. 

Hence the main selection is not cleared with `<a-,>`. Otherwise this command is identical to replace. 

```kak
execute-keys -save-regs 's' '<esc>"sZz"s<a-z>a<esc>'
```

### Extend

This is the complementary extend motion of the regular replace mode. It allows you to select from your main selection to any ending word in view. 

```kak
execute-keys -save-regs 's' '<esc>"sZ,<a-z>u"s<a-z>a<esc>'
```

- `"sZ` : save all selections to register `s`
- `,` : collapse selections to main selection
- `<a-z>u` : union main selection with jump mark
- `"s<a-z>a` : append selections in register `s`

[src]: <https://github.com/alexherbo2/dotfiles/blob/6ebc4017fb8cee757aaccbe23f44fa18f908e88c/.local/share/kak/autoload/jump_mode.kak>

