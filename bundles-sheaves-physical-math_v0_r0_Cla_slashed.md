# Question

Highly embarassing:

LaTeX synthax in markdown:

```latex
\mathrm{ind}(\not\!D_E) \;=\; \dim\ker(\not\!D_E) - \dim\ker(\not\!D_E^*)
```

produces in pandoc:

*** Error during conversion: Pandoc failed with return code 43. Error: Error producing PDF.
! Missing { inserted.

to be read again
                   \mskip 
l.734 operator} \(\not\!

(Extreme sadness, mine...)

## Answer

No need for embarrassment — this is a classic TeX gotcha, not a LaTeX skill issue.

**What's happening:** `\not` is defined (in plain TeX/LaTeX) to overlay a slash on the *next single character* by measuring its width and stepping back. It does this via low-level kerning/mskip trickery that only works if the next token is an actual math symbol — not a macro like `\!` (negative thin space), which expands into a `\mskip` command instead of a symbol. `\not` chokes trying to overlay a slash on a spacing command, and TeX throws that "Missing { inserted... \mskip" error.

So `\not\!D_E` is the culprit — `\not` is trying to slash `\!`, not `D`.

**Fixes, in order of preference:**

1. **Best: use the `slashed` package**, made exactly for Feynman-slash/Dirac-operator notation:

```latex
\usepackage{slashed}
...
\mathrm{ind}(\slashed{D}_E) \;=\; \dim\ker(\slashed{D}_E) - \dim\ker(\slashed{D}_E^*)
```

This gives a properly centered slash through the `D`, scales correctly with sub/superscripts, and won't choke on the `_E`.

2. **No new package:** just drop the `\!` and let `\not` act directly on `D`:

```latex
\mathrm{ind}(\not{D}_E) \;=\; \dim\ker(\not{D}_E) - \dim\ker(\not{D}_E^*)
```

The braces `{D}` make sure `\not` sees a clean single-token group. This compiles, though the slash placement is a bit uglier than `slashed` — `\not` was really designed for `\not=`-style relations, not letters.

3. If you specifically wanted that extra tiny leftward nudge from `\!`, put it *inside* the group, after bracing, e.g. `\not{\!D}_E` — but honestly `slashed` looks much better for this and is the standard choice in physics/geometry papers using the Dirac operator.

I'd go with option 1.

USER REMARK: I went for option1. adding the slashed package to the LaTeX proprocessor
