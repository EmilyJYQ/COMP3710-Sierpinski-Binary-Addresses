# Sierpiński Gasket Using Binary Addresses

A PyTorch implementation and analysis of the Sierpiński gasket using
binary coordinate addresses, vectorised tensor operations, CPU/MPS
performance benchmarking, and box-counting fractal-dimension analysis.

## Project overview

The Sierpiński gasket is a self-similar fractal composed of three smaller
copies of itself.

This project generates the gasket from the parity pattern of Pascal's
triangle. PyTorch tensor operations evaluate all coordinate addresses
simultaneously instead of using a Python loop over individual pixels.

The notebook includes:

- binary-address generation of the Sierpiński gasket;
- raw and centred visualisations;
- a reusable high-resolution PyTorch implementation;
- CPU and Apple MPS performance comparison;
- repeated timing measurements and interquartile ranges;
- theoretical and experimental fractal-dimension analysis.

## Binary-address rule

For a position $k$ in row $n$ of Pascal's triangle, the corresponding
binomial coefficient is odd when

$$
k \mathbin{\&} (n-k) = 0,
$$

where `&` represents the bitwise AND operation.

The PyTorch implementation creates tensors containing every row and
column address and applies the rule to the complete coordinate grid:

```python
valid_region = columns <= rows
binary_test = torch.bitwise_and(columns, rows - columns) == 0
gasket = valid_region & binary_test
```

This avoids calculating potentially large binomial coefficients
directly.

## PyTorch parallelism

The main computation uses vectorised PyTorch operations:

- `torch.arange` creates coordinate addresses;
- broadcasting evaluates every row and column combination;
- `torch.bitwise_and` applies the binary-address rule;
- Boolean masking selects active fractal points;
- tensor indexing constructs the centred visualisation;
- tensor reshaping and Boolean reduction perform box counting.

These operations process large groups of coordinates simultaneously.
When Apple MPS or NVIDIA CUDA is available, the tensors can be evaluated
on the accelerator.

The notebook selects the computational device in the following order:

1. Apple MPS;
2. NVIDIA CUDA;
3. CPU fallback.

## Visualisation

The notebook produces two representations:

- **Pascal-triangle representation:** the original binary-address grid;
- **centred representation:** active points are mapped to symmetric
  horizontal coordinates to produce the conventional triangular shape.

The coordinate transformation changes only the display position.
Fractal membership is still determined by the same binary-address rule.

## Performance analysis

The generator was benchmarked on CPU and Apple MPS across 11 image sizes
from 256 to 4096 rows.

For each image size and device:

- three warm-up runs were performed;
- twenty measured runs were collected;
- MPS was synchronised before and after timing;
- the device testing order was randomised;
- the median was used as the representative execution time;
- the interquartile range represented timing variability.

In the recorded benchmark, CPU execution was faster for small
coordinate grids. MPS became faster from 2048 rows in that run.

At 4096 rows, the recorded median times were:

| Device    | Median time |
| --------- | ----------: |
| CPU       |   15.943 ms |
| Apple MPS |   12.731 ms |

This corresponds to an MPS speedup of approximately `1.25x` and an
execution-time reduction of approximately `20.1%`.

Exact timing values may vary because of background activity, device
temperature, memory allocation, and operating-system scheduling.
Therefore, the benchmark focuses on the overall performance trend.

## Fractal-dimension analysis

A Sierpiński gasket contains three self-similar copies, each scaled by a
factor of one half. Its theoretical fractal dimension is

$$
D = \frac{\log(3)}{\log(2)} \approx 1.584963.
$$

The notebook also estimates the dimension numerically using the
box-counting method.

At each scale, doubling the box side length reduces the number of
occupied boxes by a factor of three.

The resulting dimension estimates were:

```text
Theoretical dimension: 1.584963
Estimated dimension:   1.584963
Absolute error:        8.881784e-16
```

The small difference is caused by floating-point rounding. The
experimental estimate therefore agrees with the theoretical dimension.

## Project structure

```text
COMP3710-Sierpinski-Binary-Addresses/
├── sierpinski_binary_addresses.ipynb
├── README.md
├── AI_USAGE.md
├── requirements.txt
├── LICENSE
└── .gitignore
```

- `sierpinski_binary_addresses.ipynb` contains the implementation,
  visualisations, benchmarks, and mathematical analysis.
- `README.md` provides the project overview and running instructions.
- `AI_USAGE.md` documents representative AI interactions and human
  verification.
- `requirements.txt` lists the required Python packages.
- `LICENSE` contains the MIT licence.
- `.gitignore` excludes temporary and environment-specific files.

## Requirements

Recommended environment:

- Python 3.11 or later;
- PyTorch;
- NumPy;
- Matplotlib;
- Jupyter Notebook or VS Code with the Jupyter extension.

Apple MPS is optional. The notebook also supports NVIDIA CUDA and CPU
execution.

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/EmilyJYQ/COMP3710-Sierpinski-Binary-Addresses.git
cd COMP3710-Sierpinski-Binary-Addresses
```

### 2. Create a virtual environment

On macOS or Linux:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

On Windows:

```powershell
python -m venv .venv
.venv\Scripts\activate
```

### 3. Install the dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

## Running the notebook

### VS Code

1. Open the repository folder in VS Code.
2. Open `sierpinski_binary_addresses.ipynb`.
3. Select the Python environment containing the installed dependencies.
4. Click **Run All**.
5. Confirm that every cell finishes without an error.

### Jupyter Notebook

Start Jupyter with:

```bash
jupyter notebook
```

Open `sierpinski_binary_addresses.ipynb` and select **Run All**.

The first code cell reports whether the selected device is `mps`,
`cuda`, or `cpu`.

## AI-use disclosure

Generative AI was used to support code structuring, debugging,
benchmark design, mathematical explanation, Markdown formatting, and
documentation.

All suggestions were reviewed and verified through local execution,
mathematical checks, output inspection, and incremental Git commits.

Representative AI interactions and the corresponding human decisions
are documented in [AI_USAGE.md](AI_USAGE.md).

## License

This project is distributed under the MIT License. See `LICENSE` for
details.
