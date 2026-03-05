# Bewegingen-trillingen-project
This repository contains the notebooks, code and resources used for the Bewegingen & Trillingen project. The project is intended to be developed collaboratively using Git, VS Code, and Jupyter Notebooks.
Each collaborator works with their own local Python environment and kernel, while the repository only contains the shared project files.

Python Environment

Each user should create their own virtual environment.
This ensures that dependencies are isolated and prevents conflicts between different systems.

(Example using venv:
python -m venv .venv
Activate the environment.
Windows
.venv\Scripts\activate)

Virtual environments
.venv/
venv/
env/
These folders contain:
installed Python packages
platform-specific binaries
local configuration
They can be very large and differ between machines.
Each collaborator should create their own environment locally.

Notes on Jupyter Notebooks

Jupyter notebooks (.ipynb) are JSON files and can sometimes cause merge conflicts if two people edit the same notebook simultaneously.
Recommended practices:
avoid editing the same notebook cells at the same time
split work across different notebooks when possible
commit frequently
