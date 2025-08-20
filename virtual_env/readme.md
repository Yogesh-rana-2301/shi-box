# Virtual Envs Workbook 

## What is a Virtual Environment in Python?
A virtual environment is an isolated workspace for Python projects.
It allows you to install project-specific dependencies without affecting the global Python installation or other projects.
Think of it as a sandbox where your project runs with its own Python interpreter and libraries.

## 🔹 Why Do We Need Virtual Environments?
* Dependency Management
1. Project A needs `Django 3.2`
2. Project B needs `Django 4.0` \
Without venvs → installing one version will break the other project.\
With venvs → both projects can coexist with different dependencies.
* Avoid Global Pollution
Global site-packages become messy if you install everything there.
Venv keeps your project clean and portable.
* Reproducibility
You can freeze dependencies (`pip freeze > requirements.txt`) and recreate the environment anywhere.
* Security & Stability
Prevents accidentally upgrading/downgrading system-wide Python packages.

## 🔹 How Virtual Environments Work Internally
* When you create a venv, Python:
1. Copies the Python interpreter (not the whole installation, just references).
2. Creates an isolated directory structure:
```
myenv/
  ├── bin/ (or Scripts\ on Windows)
  ├── lib/ (all installed packages go here)
  ├── include/
  └── pyvenv.cfg
```
3. Modifies PATH variables so that when activated, Python & pip refer to the venv’s interpreter and packages instead of the global one.
* Example:\
Without venv: which python → /usr/bin/python3\
With venv: which python → /Users/yaar/myenv/bin/python\

## 🔹 Creating and Using Virtual Environments
## 1. Built-in venv (recommended, since Python 3.3+)
  Create\
 `python3 -m venv myenv`

  Activate (Linux/Mac)\
  `source myenv/bin/activate`

  Activate (Windows, PowerShell)\
  `myenv\Scripts\Activate.ps1`

  Deactivate\
  `deactivate`
## 2. Using virtualenv (older but feature-rich)
```
  pip install virtualenv
  virtualenv myenv
```
  Works even for older Python versions.
## 3. Using conda (for Data Science heavy users)
```
conda create --name myenv python=3.11
conda activate myenv
```
Good for ML projects (manages non-Python deps like NumPy, TensorFlow easily).

## Installing Packages Inside venv
```
pip install requests flask
pip list
```
All installed packages go inside myenv/lib/... instead of the global environment.

## 🔹 Requirements.txt (For Reproducibility)
```

# Save installed dependencies
pip freeze > requirements.txt

# Install dependencies elsewhere
pip install -r requirements.txt
```

## 🔹 Checking Environment
```
Which Python?
which python      # Linux/Mac
where python      # Windows
```
Which pip?
```
which pip
```

Should point inside your venv.

## 🔹 Best Practices
✅ Always create a new venv per project.\
✅ Keep requirements.txt in your repo.\
✅ Don’t commit venv folder (.gitignore it).\
✅ Use pip-tools or poetry for advanced dependency management.\
✅ In larger projects, use pyenv + venv for managing multiple Python versions.\
## 🔹 Advanced Tools
1. pipenv \
Combines pip + venv in one tool.
```
pip install pipenv
pipenv install requests
pipenv shell
```
2. poetry \
Modern dependency & packaging tool.
```
pip install poetry
poetry init
poetry add flask
poetry shell
```
3. pyenv (manages multiple Python versions)
```
pyenv install 3.12
pyenv virtualenv 3.12.0 myenv
```
## 🔹 Common Issues
1. Forgot to activate venv → packages install globally.
Fix: always check `which python.`
2. VS Code not using venv
Select interpreter: `Ctrl+Shift+P` → `Python: Select Interpreter.`
3. Permission errors
Avoid `sudo pip install.` Use venv instead.
