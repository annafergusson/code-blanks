# code-blanks

`code-blanks` is a Quarto extension that supports the authoring of interactive worked examples involving code. The extension lets authors create editable code examples where learners can only modify specific aspects (the blanks), run code (in-browser using WebR or alternatives), view plots/output, write responses, and progress through activities step-by-step.

**Why code blanks?**

“code blanks” is a pedagogical approach that provides students with code that contains only some sections that are editable, in order to focus attention on key programming decisions. Code blanks use a combination of visual scaffolding (purple blanks) and constrained interaction mechanisms (text boxes) to support purposeful reasoning and problem-solving within authentic data science workflows.

> Note: The development of this Quarto extension was influenced by the [Flouish Quarto extension](https://github.com/kbodwin/flourish) developed by Kelly Bodwin and Visruth Kandali.

**Associated research**

The "code blanks" approach is being investigated through several research projects led by Dr Anna Fergusson (University of Auckland).

Slides from SDSS2025 talk *Learning through tinkering: Designing interactive worked examples for introductory-level data science students* [to be added].

## Features

* Editable code blanks inside R examples
* Run R code in the browser using WebR
* Text and plot output
* Learner response text boxes
* Instruction callout boxes
* Progressive reveal of sections
* Local storage saving of progress
* Reset / clear saved progress
* Support for custom functions and setup code
* Customisable JavaScript runner and CSS styling

## Installation

```bash
quarto add annafergusson/code-blanks
```

Then in your document:

```yaml
filters:
  - code-blanks
```

## Minimal example

````markdown
---
title: Demo
format: html
filters:
  - code-blanks

code-blanks:
  packages:
    - ggplot2
  setup:
    - library(ggplot2)

```{code-blanks}
#| blank:
#|   target: 100
#|   prefill: true
#|   occurrence: 1
#|   match: token

numbers <- runif(n = 100)

ggplot() +
  geom_histogram(aes(x = numbers))
```
````

## Code blank options

Each blank is defined using metadata lines.

```yaml
#| blank:
#|   target: 100
#|   prefill: true
#|   occurrence: 1
#|   match: token
```

### Options

| Option         | Meaning                                    |
| -------------- | ------------------------------------------ |
| `target`       | Text to replace with a blank               |
| `prefill`      | Start with answer shown (`true` / `false`) |
| `occurrence`   | Which matching occurrence to replace       |
| `match: token` | Match standalone token only                |
| `match: text`  | Match text inside larger strings           |

Example:

```yaml
target: density
match: text
```

will replace `density` inside:

```r
geom_density()
```

## Document options

```yaml
code-blanks:
  packages:
    - ggplot2
  setup:
    - library(ggplot2)
  plot-width: 800
  plot-height: 500
  progressive-reveal: true
```

### Options

| Option               | Meaning                       |
| -------------------- | ----------------------------- |
| `packages`           | WebR packages to install      |
| `setup`              | R code run once on page load  |
| `plot-width`         | Plot width in pixels          |
| `plot-height`        | Plot height in pixels         |
| `progressive-reveal` | Reveal sections one at a time |

## Instruction boxes

````markdown
```{code-blanks-instructions}
Try changing `n` and rerun the code.

- What changes?
- What stays the same?
```
````

## Response boxes

````markdown
```{code-blanks-response}
What do you notice about the shape of the graph?
```
````

## Custom functions

````markdown
```{code-blanks-functions}
double_it <- function(x) {
  x * 2
}
```
````

Functions are automatically available to runnable code blocks.

## Overriding the runner script

By default, `code-blanks` uses `runner-webr.js` with WebR.

Advanced users can replace the runner with their own JavaScript runner (for example, a remote R API, Python backend, or custom execution engine).

### Option 1: Replace the file inside the extension

Swap:

```text
runner-webr.js
```

with your own implementation.

### Option 2: Add your own script after the extension

```yaml
format:
  html:
    include-after-body: custom-runner.js
```

If your script defines:

```javascript
window.codeBlanksRun = async function(code, outputDiv) {
  outputDiv.innerHTML = "<pre>" + code + "</pre>";
}
```

it will override the default runner.

### Required API

Your custom runner should define:

```javascript
window.codeBlanksRun(code, outputDiv)
```

Where:

| Argument    | Meaning                                |
| ----------- | -------------------------------------- |
| `code`      | Reconstructed runnable code            |
| `outputDiv` | Element where output should be written |

## Customising colours / styling

You can override CSS in your Quarto document.

### Example

```yaml
format:
  html:
    css: styles.css
```

Then in `styles.css`:

```css
.code-blanks-instructions-box {
  background: #FFF4CC;
}

.code-blanks-response {
  background: #F8F8F8;
}

.code-blanks-run {
  background: #E5F3FD;
}
```

### Useful classes

| Class                           | Purpose                   |
| ------------------------------- | ------------------------- |
| `.code-blanks`                  | Code block                |
| `.code-blank-input`             | Input blanks              |
| `.code-blanks-toolbar`          | Buttons row               |
| `.code-blanks-output`           | Output area               |
| `.code-blanks-response-box`     | Response container        |
| `.code-blanks-response`         | Response textarea         |
| `.code-blanks-instructions-box` | Instruction box           |
| `.code-blanks-next-section`     | Progressive reveal button |

## Saving progress

The extension saves locally in the learner’s browser:

* code blank values
* written responses
* reveal progress
* generated outputs and plots

No data is sent to a server.

## Browser notes

Best experience in a modern browser such as Chrome, Edge, or Firefox.

RStudio Viewer may not fully support WebR graphics.

## Limitations

HTML output only.

WebR runner uses CDN.

Package loading can take time.

Local storage is browser-local, not submitted anywhere.

And probably a bunch more!

## Current status

Early release (`v0.1.0`) under active development.

Feedback and ideas welcome.

## License

MIT License
