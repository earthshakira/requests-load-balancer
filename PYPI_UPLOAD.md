# How to Upload to PyPI

This document explains how to upload the `requests-load-balancer` package to PyPI.

## Prerequisites

1. Install `twine` for uploading to PyPI:
   ```bash
   pip install twine
   ```

2. Create accounts on:
   - PyPI: https://pypi.org/account/register/
   - TestPyPI (optional, for testing): https://test.pypi.org/account/register/

## Build the Package

The package has already been built and is available in the `dist/` directory:

```bash
python3 -m build --no-isolation
```

This creates:
- `dist/requests-load-balancer-0.0.1.tar.gz` (source distribution)
- `dist/requests_load_balancer-0.0.1-py3-none-any.whl` (wheel)

## Upload to TestPyPI (Optional but Recommended)

First, test the upload on TestPyPI:

```bash
twine upload --repository testpypi dist/*
```

You'll be prompted for your TestPyPI username and password.

Then test installing from TestPyPI:

```bash
pip install --index-url https://test.pypi.org/simple/ requests-load-balancer
```

## Upload to PyPI

Once you've verified everything works on TestPyPI, upload to the real PyPI:

```bash
twine upload dist/*
```

You'll be prompted for your PyPI username and password.

## Verify the Upload

After uploading, you can:

1. Visit the package page: https://pypi.org/project/requests-load-balancer/
2. Install the package: `pip install requests-load-balancer`
3. Test the installation:
   ```python
   from requests_load_balancer import LoadBalancer
   lb = LoadBalancer(['http://api1.com', 'http://api2.com'])
   ```

## Using API Tokens (Recommended)

Instead of username/password, use API tokens for better security:

1. Go to https://pypi.org/manage/account/
2. Scroll to "API tokens" section
3. Create a new API token
4. Create a `~/.pypirc` file:
   ```ini
   [distutils]
   index-servers =
       pypi
       testpypi

   [pypi]
   username = __token__
   password = pypi-YourAPITokenHere

   [testpypi]
   username = __token__
   password = pypi-YourTestAPITokenHere
   ```

Then you can upload without being prompted:

```bash
twine upload dist/*
```

## Troubleshooting

### File Already Exists Error

If you get a "File already exists" error, you cannot re-upload the same version. You must:
1. Increment the version number in `pyproject.toml`
2. Rebuild: `python3 -m build --no-isolation`
3. Upload the new version

### Authentication Failed

- Double-check your username/password or API token
- Make sure you're uploading to the correct repository (PyPI vs TestPyPI)

## Next Steps

After successfully uploading:
1. Update the README with the actual PyPI installation command
2. Add badges to show PyPI version, downloads, etc.
3. Set up CI/CD for automated releases
