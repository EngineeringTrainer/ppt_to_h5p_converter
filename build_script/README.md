# Converter source and executable builds

The [main README](../README.md) is the user guide for both the Windows GUI and
cross-platform CLI. It contains setup, usage, troubleshooting, H5P compatibility,
and a [version comparison](../README.md#version-differences). This file covers the
source entry points and Windows GUI packaging without duplicating that guide.

## Source entry points

| Version | Actual filename in this directory | Interface |
| --- | --- | --- |
| v1.1 | [ppt_to_h5p_converter v1.1.py](<ppt_to_h5p_converter v1.1.py>) | Windows Tkinter GUI; fixed 3840×2160 exports |
| v1.2 | [pptx_to_h5p_converter buttons v1_2.py](<pptx_to_h5p_converter buttons v1_2.py>) | Windows Tkinter GUI; high/low resolution selection |
| v1.3 | [pptx_to_h5p_converter cli v1_3.py](<pptx_to_h5p_converter cli v1_3.py>) | Cross-platform CLI; single-file and folder processing |

## Working directory and dependencies

Run the documented commands from the **repository root**, one level above this
directory. The shared [requirements.txt](../requirements.txt) and
[LICENSE](../LICENSE) are also at that level.

- GUI source: Windows, desktop PowerPoint, Tkinter, Pillow, and pywin32.
- CLI source: Pillow and FontTools for font verification, plus pywin32 for Windows
  PowerPoint, or pypdfium2 and installed LibreOffice for the PDF-rendering route.
  On Linux, font verification also requires fontconfig (`fc-list`). The
  `--ignore-missing-fonts` flag bypasses font verification and its warnings.
- The documented shared environment uses Python 3.11 or newer. PyInstaller is an
  optional build dependency, not a conversion requirement.

## Building a Windows GUI executable

Build on Windows with desktop PowerPoint installed. From the repository root in
PowerShell, create the environment if needed and install the build dependency:

```powershell
py -3 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m pip install pyinstaller
```

Package v1.2 directly from its existing source file:

```powershell
.\.venv\Scripts\python.exe -m PyInstaller --onefile --windowed --name ppt_to_h5p_converter_v1_2 "build_script/pptx_to_h5p_converter buttons v1_2.py"
```

For v1.1, use its own source file and a separate executable name:

```powershell
.\.venv\Scripts\python.exe -m PyInstaller --onefile --windowed --name ppt_to_h5p_converter_v1_1 "build_script/ppt_to_h5p_converter v1.1.py"
```

With these commands, the executable is written to `dist/` under the selected
name, such as `dist/ppt_to_h5p_converter_v1_2.exe`. PyInstaller generates the
corresponding `.spec` file in the current directory and intermediate files in
`build/`; a pre-existing spec file is not required. See the official
[PyInstaller usage documentation](https://pyinstaller.org/en/stable/usage.html).

The executable bundles Python dependencies; it does not bundle Microsoft
PowerPoint. Test the chosen build on Windows before distributing it. These
packaging commands were checked against PyInstaller's documentation but have
not been executed on Windows during this work.

The v1.3 CLI is currently distributed as a Python script. Its PowerPoint worker
relaunches the script through the Python interpreter; the GUI packaging commands
above are not a validated CLI packaging workflow.
Keep the CLI's [font verification module](<pptx_to_h5p_converter cli v1_3 verify fonts.py>)
beside its entry script.

## Verification

Follow the [main test instructions](../README.md#development-and-verification).
The CLI tests cover shared packaging and app-selection behavior, with optional
real LibreOffice integration. They do not launch either legacy GUI.
