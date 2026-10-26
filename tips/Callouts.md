Use callouts to include additional content without breaking the flow of your notes.
### Change the title

By default, the title of the callout is its type identifier in title case. You can change it by adding text after the type identifier:

```markdown
> [!tip] Callouts can have custom titles
> Like this one.
```

> [!tip ] Callouts can have custom titles
> Like this one.

You can even omit the body to create title-only callouts:

```markdown
> [!tip] Title-only callout
```

> [!tip] Title-only callout

### Foldable callouts

You can make a callout foldable by adding a plus (`+`) or a minus (`-`) directly after the type identifier.
A plus sign expands the callout by default, and a minus sign collapses it instead.

```markdown
> [!faq]- Are callouts foldable?
> Yes! In a foldable callout, the contents are hidden when the callout is collapsed.
```

> [!faq] Are callouts foldable?

### Nested callouts

You can nest callouts in multiple levels.

```markdown
> [!question] Can callouts be nested?
> > [!todo] Yes!, they can.
> > > [!example]  You can even use multiple layers of nesting.
```

> [!question] Can callouts be nested?
> > [!todo] Yes!, they can.
> > > [!example] You can even use multiple layers of nesting.
> 

### Customize callouts

[CSS snippets](https://obsidian.md/help/snippets) and [Community plugins](https://obsidian.md/help/community-plugins) can define custom callouts, or even overwrite the default configuration.

To define a custom callout, create the following CSS block:

```css
.callout[data-callout="custom-question-type"] {
    --callout-color: #000000;
    --callout-icon: lucide-alert-circle;
}
```

The value of the `data-callout` attribute is the type identifier you want to use, for example `[!custom-question-type]`.

- `--callout-color` defines the background color. Any valid CSS color works, for example a hex code (`#000000`) or an `rgb()` value.
- `--callout-icon` can be an icon ID from [lucide.dev](https://lucide.dev/), or an SVG element.

Note about lucide icon versions

Obsidian updates Lucide icons periodically. The current version included is shown below; use these or earlier icons in custom callouts.  

Version `0.446.0`  
ISC License  
Copyright (c) 2020, Lucide Contributors

SVG icons

Instead of using a Lucide icon, you can also use a SVG element as the callout icon.

```css
--callout-icon: '<svg>...custom svg...</svg>';
```

Further customization

### Supported types

You can use several callout types and aliases. Each type comes with a different background color and icon.

To use these default styles, replace `info` in the examples with any of these types, such as `[!tip]` or `[!warning]`. Callout types can also be changed by right-clicking a callout in Live Preview mode.

Unless you [Customize callouts](https://obsidian.md/help/callouts#Customize%20callouts), any unsupported type defaults to the `note` type. The type identifier is case-insensitive.

Note

```md
> [!note]
> Lorem ipsum dolor sit amet
```

> [!note]
> Lorem ipsum dolor sit amet


Abstract

```md
> [!abstract]
> Lorem ipsum dolor sit amet
```

> [!abstract]
> Lorem ipsum dolor sit amet

---

Info

```md
> [!info]
> Lorem ipsum dolor sit amet
```

> [!info]
> Lorem ipsum dolor sit amet

---

Todo

```md
> [!todo]
> Lorem ipsum dolor sit amet
```

> [!todo]
> Lorem ipsum dolor sit amet

---

Tip

```md
> [!tip]
> Lorem ipsum dolor sit amet
```

> [!tip]
> Lorem ipsum dolor sit amet


---

Success

```md
> [!success]
> Lorem ipsum dolor sit amet
```

> [!success]
> Lorem ipsum dolor sit amet

---

Question

```md
> [!question]
> Lorem ipsum dolor sit amet
```

> [!question]
> Lorem ipsum dolor sit amet

---

Warning

```md
> [!warning]
> Lorem ipsum dolor sit amet
```

> [!warning]
> Lorem ipsum dolor sit amet

---

Failure

```md
> [!failure]
> Lorem ipsum dolor sit amet
```

> [!failure]
> Lorem ipsum dolor sit amet

---

Danger

```md
> [!danger]
> Lorem ipsum dolor sit amet
```

> [!danger]
> Lorem ipsum dolor sit amet

---

Bug

```md
> [!bug]
> Lorem ipsum dolor sit amet
```

> [!bug]
> Lorem ipsum dolor sit amet


---

Example

```md
> [!example]
> Lorem ipsum dolor sit amet
```

> [!example]
> Lorem ipsum dolor sit amet

---

Quote

```md
> [!quote]
> Lorem ipsum dolor sit amet
```

> [!quote]
> Lorem ipsum dolor sit amet
