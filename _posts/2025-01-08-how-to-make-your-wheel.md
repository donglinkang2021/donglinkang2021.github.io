---
title: 'How to make your wheel'
date: 2025-01-08
permalink: /posts/2025/01/how-to-make-your-wheel/
tags:
  - usage
---

```bash
python -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install setuptools wheel
pip install twine
python setup.py sdist bdist_wheel
```

To wrap your Python package into a `.whl` (Wheel) file, follow these steps:

## Step 1: Prepare your package structure

Ensure that your package has the following structure:

```shell
your_package/
├── setup.py
├── your_package/
│   ├── __init__.py
│   └── (other module files)
├── MANIFEST.in (optional, but recommended)
└── README.md
```

## Step 2: Install `setuptools` and `wheel`

You need `setuptools` and `wheel` to build the package. Install them using:

```bash
pip install setuptools wheel
```

## Step 3: Create `setup.py`

The `setup.py` file is essential for building your package. Here is an example:

```python
from setuptools import setup, find_packages

setup(
    name='your_package_name',
    version='0.1.0',
    packages=find_packages(),
    install_requires=[
        # List of dependencies (if any)
    ],
    classifiers=[
        'Programming Language :: Python :: 3',
        'License :: OSI Approved :: MIT License',
        'Operating System :: OS Independent',
    ],
    python_requires='>=3.6',
)
```

This is a basic setup. Customize the fields like `name`, `version`, `install_requires`, etc., to suit your project.

## Step 4: Add `MANIFEST.in` (Optional)

If you want to include additional files (like README, LICENSE, etc.) in your package, add a `MANIFEST.in` file. For example:

```plaintext
include README.md
include LICENSE
recursive-include your_package *
```

## Step 5: Build the Wheel

In the terminal, navigate to the root of your package (where `setup.py` is located), and run:

```bash
python setup.py sdist bdist_wheel
```

This command will generate two things:

- A source distribution (`.tar.gz`) in the `dist` folder.
- A wheel distribution (`.whl`) in the same folder.

The `.whl` file is your packaged Python distribution.

## Step 6: Install the Wheel File (Optional)

To test your wheel package locally, you can install it using:

```bash
pip install dist/your_package_name-0.1.0-py3-none-any.whl
```

## Step 7: Upload to PyPI (Optional)

If you want to upload your package to the Python Package Index (PyPI), you can use `twine`:

1. Install `twine`:

   ```bash
   pip install twine
   ```

2. Upload the wheel:

   ```bash
   twine upload dist/*
   ```

You'll need to provide your PyPI credentials.

---

This process will create a `.whl` file that can be distributed and installed using `pip`. You can now share your Python package with others!
