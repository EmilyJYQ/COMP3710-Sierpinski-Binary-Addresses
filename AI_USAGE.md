# AI Usage and Verification Record

## Disclosure

OpenAI ChatGPT/Codex was used as a development assistant during this
project.

AI assistance was used for:

- discussing the PyTorch implementation structure;
- explaining the binary-address rule;
- improving the performance experiment;
- debugging notebook and Markdown problems;
- designing the box-counting analysis;
- reviewing documentation.

The mathematical method, experimental decisions, code execution,
result verification, and Git commits were reviewed by the author.

The prompts below are representative summaries of the interaction.
Some wording has been shortened or translated into English for clarity;
they are not presented as a complete verbatim chat transcript.

## Interaction 1: Selecting the binary-address formulation

### Representative prompt

> I want to generate the Sierpiński gasket from binary addresses rather
> than repeatedly removing triangles. How can the condition
> `k & (n-k) == 0` be evaluated across a complete coordinate grid using
> PyTorch?

### Reasoning behind the prompt

The goal was to connect the parity pattern of Pascal's triangle with a
tensor-based implementation. A coordinate rule was preferred because it
could evaluate many points simultaneously and demonstrate PyTorch
parallelism.

### AI assistance

The AI suggested:

- creating row and column tensors with `torch.arange`;
- using broadcasting to form the complete coordinate grid;
- restricting valid coordinates with `columns <= rows`;
- applying `torch.bitwise_and`;
- storing the result in a Boolean tensor.

### Human verification and decisions

The implementation was executed at 256 rows.

The result contained `6561` active points:

$$
6561 = 3^8.
$$

This agreed with the expected self-similar point count and confirmed
that the binary-address rule was being applied correctly.

## Interaction 2: Separating membership from visualisation

### Representative prompt

> The Pascal-triangle output is left aligned. Can the active binary
> addresses be mapped to a centred Sierpiński triangle without changing
> the rule that decides whether a point belongs to the fractal?

### Reasoning behind the prompt

The raw representation was mathematically correct, but its appearance
was different from the conventional centred Sierpiński triangle.

The intention was to keep the binary-address calculation unchanged and
alter only the display coordinates.

### AI assistance

The AI suggested finding all active coordinates and applying the mapping

$$
x = (N-1-n) + 2k.
$$

### Human verification and decisions

The number of active points was compared before and after the coordinate
transformation.

Both representations contained exactly `6561` active points at 256
rows. This confirmed that the transformation changed only the
visualisation and not fractal membership.

## Interaction 3: Questioning the original performance graph

### Representative prompt

> The graph has too few checkpoints, and the timings appear random.
> Can the experiment use more sizes and repeated measurements to show a
> more accurate performance trend?

### Reasoning behind the prompt

The original benchmark contained only four image sizes. A single timing
value at each size could be affected by background activity, memory
allocation, and accelerator start-up overhead.

More measurement points and repeated runs were needed before drawing a
performance conclusion.

### AI assistance

The AI suggested:

- testing 11 image sizes;
- performing warm-up runs;
- collecting 20 measured runs per size and device;
- synchronising MPS before and after timing;
- randomising the CPU/MPS testing order;
- reporting the median;
- displaying the interquartile range.

### Human verification and decisions

The revised benchmark was executed locally.

The output showed that CPU was faster for smaller coordinate grids,
while MPS became competitive for larger grids. In the recorded run, MPS
became faster at 2048 rows.

The written interpretation was updated using the latest measurements
rather than values from an earlier run.

## Interaction 4: Verifying fractal dimension experimentally

### Representative prompt

> The theoretical dimension is `log(3) / log(2)`. Can the generated
> PyTorch image verify this numerically using box counting? Which box
> scales should be used so that the comparison is meaningful?

### Reasoning behind the prompt

Producing a correct-looking image was not considered sufficient. The
project also needed a quantitative test of the fractal's self-similar
structure.

Power-of-two scales were selected because the generated image size was
also a power of two, allowing the grid to be divided exactly.

### AI assistance

The AI suggested:

- using a 1024-row raw binary image;
- testing box sizes from 1 to 512;
- reshaping the tensor into non-overlapping boxes;
- counting boxes containing at least one active point;
- fitting a line to the logarithmic scale and count values.

### Human verification and decisions

The measured counts followed the pattern:

```text
59049, 19683, 6561, 2187, 729, 243, 81, 27, 9, 3
```

Doubling the box side length divided the occupied-box count by three.

The calculated results were:

```text
Theoretical dimension: 1.584963
Estimated dimension:   1.584963
Absolute error:        8.881784e-16
```

The experiment therefore agreed with the theoretical dimension.

## Interaction 5: Checking consistency before committing

### Representative prompts

> The performance output changed after repeating the experiment. Should
> the explanation be rewritten so that it matches the latest results?

> The mathematical formatting does not look correct. Is the cell using
> the wrong Markdown equation format?

> Review the complete notebook before I commit it.

### Reasoning behind the prompts

The notebook should not contain an explanation based on older benchmark
values. Mathematical equations also needed to render correctly, and
previous cell-execution errors needed to be resolved before committing.

### AI assistance

The AI helped identify:

- an outdated performance interpretation;
- incompatible equation delimiters;
- Markdown text that had been executed as Python;
- an unnecessary empty cell.

### Human verification and decisions

The explanation was rewritten using the latest benchmark output.

Mathematical expressions were changed to Jupyter-compatible `$...$` and
`$$...$$` notation. The incorrect cell type was fixed, and empty cells
were removed.

Before the Git commit, VS Code reported zero errors and zero warnings.

## Final verification statement

AI outputs were treated as suggestions rather than automatically
accepted answers.

The author:

- executed all notebook code locally;
- checked active-point counts against powers of three;
- compared raw and centred point counts;
- repeated CPU and MPS performance measurements;
- updated explanations when measurements changed;
- compared the box-counting estimate with the theoretical dimension;
- corrected formatting and execution errors;
- used incremental Git commits to record the development process.

The author remains responsible for the final code, analysis,
interpretation, and submitted repository.
