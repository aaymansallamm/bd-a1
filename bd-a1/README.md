# CSCI461 Assignment 1 - Dockerized SMS analysis

Team members and student IDs: **fill in all four members before submission**.

This project uses the supplied SMS Spam Collection CSV for preprocessing, text analysis,
supervised classification and K-Means. The runnable entry point is `notebook/analysis.ipynb`.
The original teammate notebooks are retained in `source_notebooks/` for reference.

Docker Hub repository: `ayman2287/bd-a1`

Release tag: `1.0`

Local build and execution: **completed on the team member's Windows Docker Desktop**.
The saved notebook contains 45 executed code cells, no saved execution errors, and the
final `ALL REQUIRED OUTPUT CHECKS PASSED` message. Required output files and nine figures
are included in this project.

Docker Hub publication: **deferred**. The push encountered a network timeout followed by
`insufficient_scope: authorization failed`; successful publication is not claimed.
Teammate verification of a pulled image: **pending**.

GitHub repository URL: **add the actual URL after uploading**.
Docker Hub publishing commands remain below for completing that rubric item later.

## Upload this project to GitHub now

1. Create a repository named `bd-a1` on GitHub. Choose visibility according to your course's
   rules. Upload the contents of this directory so Dockerfile and README.md are at the root.
2. Include data/, notebook/, output/, and screenshots/. Do not upload only a ZIP: the project
   files should be browsable, including the executed notebook and output figures.
3. The screenshots/ folder includes the supplied notebook screenshot and clearly labeled
   images rendered from the user's terminal transcript. See screenshots/EVIDENCE_INDEX.md
   for provenance and missing evidence, especially the actual docker images screenshot.
4. Fill the four team names and student IDs above and in the notebook, and add the actual
   GitHub URL here after creating the repository.
5. Commit the files with a message such as `Add Dockerized SMS analysis project`.

The source project can be built with the commands below. GitHub source distribution and
Docker Hub image distribution are separate: the GitHub bonus does not replace the required
Docker Hub push and teammate pull/run verification.

## Project structure

```text
bd-a1/
  Dockerfile
  requirements.txt
  README.md
  .dockerignore
  DOCKER_GUIDE.md
  INTEGRATION_NOTES.md
  data/
    dataset.csv
  notebook/
    analysis.ipynb
  output/
    figures/
  screenshots/
    CHECKLIST.txt
  source_notebooks/
    analysis.ipynb
    Text_Analysis_-3.ipynb
    machine_learning.ipynb
```

`output/` contains results from the completed notebook execution: `cleaned_data.csv`,
`model_metrics.txt`, `cluster_summary.txt`, nine PNG figures, and supplementary CSV,
insight and package-version files. Rerunning the notebook regenerates these files.

## Prerequisites and host preparation

Use Docker Desktop with Linux containers and a working WSL 2 backend on Windows.
Commands below are for **PowerShell**, not the Python `>>>` prompt or a container's Bash shell.
Prepare/extract all project files on the host **before** building, as the assignment requires.
The dataset is already included; no Kaggle or Colab upload is needed.

If the supplied ZIP is saved in Downloads, extract it into a new folder:

```powershell
Expand-Archive -LiteralPath "$env:USERPROFILE\Downloads\bd-a1-docker-ready.zip" -DestinationPath "$env:USERPROFILE\Downloads\CSCI461_Docker"
Set-Location "$env:USERPROFILE\Downloads\CSCI461_Docker\bd-a1"
```

If you already extracted elsewhere, change into that `bd-a1` folder instead. In all commands
below, the current folder must contain `Dockerfile`, `data`, `notebook`, `output` and `screenshots`.

```powershell
Get-ChildItem
docker version
New-Item -ItemType Directory -Force -Path output\figures,screenshots | Out-Null
```

## Build the custom image

```powershell
docker build --progress=plain -t bd-a1:1.0 . 2>&1 | Tee-Object -FilePath screenshots\build.log
```

Confirm the final build succeeds. A download message or an intermediate cached layer alone
does not prove a successful build. Capture `01-build-success.png`.

```powershell
docker images
```

Capture `02-images.png`, showing repository `bd-a1` and tag `1.0`.

The Dockerfile uses the required `quay.io/jupyter/scipy-notebook:latest` base, creates the
project directory, installs requirements, copies the notebook and dataset, and sets WORKDIR.
The copied notebook is initially unexecuted (no saved cell outputs); run it after starting Jupyter.
It is a valid notebook, unlike the zero-byte placeholder in the original starter ZIP.
The scientific Python/Jupyter base already supplies many packages; requirements.txt makes
the project's dependencies explicit and adds any missing listed packages.

## Start JupyterLab with a persistent output bind mount

```powershell
docker run -d --name bd-a1-lab -p 127.0.0.1:8888:8888 --mount "type=bind,source=$($PWD.Path)\output,target=/home/jovyan/bd-a1/output" bd-a1:1.0
docker ps
docker ps -a
docker logs bd-a1-lab
docker inspect bd-a1-lab
docker inspect --format '{{json .Mounts}}' bd-a1-lab
```

Capture the running container, logs and mount details. Use `screenshots/CHECKLIST.txt`.
The `Mounts` entry must have type `bind`, a Source ending in your host `bd-a1\output`,
and Destination `/home/jovyan/bd-a1/output`. This is the required persistence connection.

To save inspection details as text evidence:

```powershell
docker inspect bd-a1-lab | Out-File -Encoding utf8 screenshots\container-inspect.json
```

Open the actual `http://127.0.0.1:8888/lab?token=...` URL from `docker logs` in your browser.
If logs show a container hostname, replace that hostname with `127.0.0.1`, keeping the port,
path and token. Do not use the Linux `file:///...` link on Windows. Obscure the token in
submitted screenshots.

### Image, container, ports and mount

- **Dockerfile:** the recipe used to build the image.
- **Image:** the packaged filesystem and configuration, including dependencies, dataset and code.
- **Container:** an instance created from an image, with its own writable layer and running process.
- `-d` starts it in the background; `--name bd-a1-lab` gives the instance a usable name.
- `-p 8888:8888` means **HOST_PORT:CONTAINER_PORT**: host 8888 forwards to Jupyter's port 8888.
  The command above also specifies `127.0.0.1` to listen on this computer's loopback interface.
- **Bind mount:** connects an explicit host folder to a container path. Writing beneath that
  container path writes to the host folder too. It is appropriate for the assignment output folder.
- **Named volume:** persistent storage managed by Docker, rather than an explicit project folder.
  The rubric specifically requests a bind mount here.
- `EXPOSE 8888` documents the expected container port; it does not publish the port by itself.

## Execute the notebook and verify outputs

In JupyterLab open `notebook/analysis.ipynb`. Choose
**Kernel > Restart Kernel and Run All Cells** (wording may vary slightly by JupyterLab version).
Wait for the kernel to become idle and the final cell to print:

```text
ALL REQUIRED OUTPUT CHECKS PASSED
```

Capture a notebook screenshot with the final output and another with a figure such as the
confusion matrix. An idle kernel without the final checks, or saved plots from an old run,
is not sufficient evidence that this execution completed.

Save the executed notebook with Ctrl+S. In PowerShell copy it back to the host project:

```powershell
docker cp bd-a1-lab:/home/jovyan/bd-a1/notebook/analysis.ipynb .\notebook\analysis.ipynb
Copy-Item .\notebook\analysis.ipynb .\output\analysis_executed.ipynb
Get-ChildItem .\output
Get-ChildItem .\output\figures
```

This copy is necessary because only `output/` is bind-mounted; `notebook/` is inside the
container. The results themselves already appear on Windows through the bind mount.

Expected primary outputs:

```text
output/cleaned_data.csv
output/model_metrics.txt
output/cluster_summary.txt
output/figures/correlation_heatmap.png
output/figures/top_20_words.png
output/figures/average_text_length.png
output/figures/average_word_count.png
output/figures/confusion_matrices.png
```

There are additional sampling, stop-word and clustering figures. Package versions from the
actual run are saved in `output/environment_requirements.txt`.

Optional automated check, in addition to the required JupyterLab run:

```powershell
docker exec bd-a1-lab jupyter nbconvert --to notebook --execute --ExecutePreprocessor.timeout=1800 --output analysis_executed --output-dir /home/jovyan/bd-a1/output /home/jovyan/bd-a1/notebook/analysis.ipynb
```

This runs a new kernel and stops on the first cell error. Do not run it at the same time as
Run All in the browser, because both would write the same output files.

## Lifecycle and persistence proof

After the run has completed and you have saved/copied the executed notebook:

```powershell
docker stop bd-a1-lab
docker ps
docker ps -a
```

Capture the stopped container in `docker ps -a`: running-only `docker ps` will omit it.
Then remove this specific assignment container and check the results still exist:

```powershell
docker rm bd-a1-lab
Get-ChildItem .\output
Get-ChildItem .\output\figures
```

Capture persistence evidence. The image still exists, and the host output files still exist.
Only the named assignment container was removed; your earlier lab's `hello` container is unrelated.
Create another container with the same run command above whenever you want to reopen the project.
`docker start` restarts an existing stopped container; it cannot restart one already removed.

## Publish the final image to Docker Hub

Create a repository named `bd-a1` under Docker Hub account `ayman2287`.
Use a public repository for easy teammate access, or give the teammate access if it is private.
The source dataset's attribution is included below.

After saving any final notebook/code edits to the host, rebuild so the image includes them:

```powershell
docker build --progress=plain -t bd-a1:1.0 . 2>&1 | Tee-Object -FilePath screenshots\final-build.log
```

Before pushing, run this final image again using the documented run command and repeat
Restart Kernel and Run All. Record actual success. If notebook code changes during this
check, save/copy it to the host and rebuild/retest before release. Re-saving execution outputs
alone does not change the analysis code.

```powershell
docker login --username ayman2287
docker tag bd-a1:1.0 ayman2287/bd-a1:1.0
docker push ayman2287/bd-a1:1.0
```

Follow Docker's sign-in prompt on your own machine. Do not put credentials in this README,
the Dockerfile or screenshots. Capture the successful push and the returned digest.

```powershell
docker image inspect ayman2287/bd-a1:1.0 --format '{{json .RepoDigests}}' | Out-File -Encoding utf8 screenshots\published-digest.txt
```

The repository is [ayman2287/bd-a1](https://hub.docker.com/r/ayman2287/bd-a1), tag `1.0`;
this address is the intended publication location, not a claim it is already published.
After pushing, replace the deferred publication status at the top with the actual date and digest.
Do not overwrite this release tag after teammate verification; use `1.1` for subsequent code changes.

## Verification by another team member

This must be performed on another member's machine, not merely in a second container on yours.
They need Docker Desktop, but do not need to build from source or install Python packages locally.
They should use an empty folder and bind-mount only output, so the code and data are demonstrably
coming from the published image.

PowerShell commands for the teammate:

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\Downloads\bd-a1-pull-test\output" | Out-Null
Set-Location "$env:USERPROFILE\Downloads\bd-a1-pull-test"
docker pull ayman2287/bd-a1:1.0
docker run -d --name bd-a1-verify -p 127.0.0.1:8889:8888 --mount "type=bind,source=$($PWD.Path)\output,target=/home/jovyan/bd-a1/output" ayman2287/bd-a1:1.0
docker ps
docker logs bd-a1-verify
```

They open the logged URL using **host port 8889**, keeping the token. For example,
`http://127.0.0.1:8889/lab?token=...`. Port 8888 inside the image is unchanged.
They open `notebook/analysis.ipynb`, restart the kernel, run all cells and confirm the final
output checks and host files. They send screenshots of the pull, running container and
completed notebook, plus their name, date and image digest. Add these to `screenshots/`.

Record actual verification below (do not fill in success until it happens):

```text
Verifier name/student ID:
Date:
Pulled image and digest:
Operating system/CPU architecture:
Notebook Restart and Run All result:
Host output files confirmed:
Screenshot filenames:
```

A Windows Intel/AMD build is normally linux/amd64. If a teammate uses an ARM Mac and the
pull reports no matching platform, first check the image architecture. They can try
`--platform linux/amd64` in the run command for emulation, or the team can build/test a
multi-platform image. Do not claim an untested architecture works.

## Final submission ZIP

Update team details, publication status and teammate verification above; include real screenshots,
the executed notebook and generated outputs. From inside `bd-a1`, package the folder:

```powershell
Set-Location ..
Compress-Archive -Path .\bd-a1 -DestinationPath .\bd-a1-submission.zip
```

Use a different filename if a submission ZIP already exists. The image is distributed through
Docker Hub; the submission ZIP contains project source, data, outputs and evidence.
The initial prepared ZIP is a starting package, not a completed submission.

## Dataset attribution

Almeida, T. & Hidalgo, J. (2011). *SMS Spam Collection* [Dataset]. UCI Machine Learning
Repository. https://doi.org/10.24432/C5CC84.

Source: https://archive.ics.uci.edu/dataset/228/sms%2Bspam%2Bcollection

UCI lists the dataset under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
The included `data/dataset.csv` is the team's supplied two-column CSV representation,
renamed from `Dataset.csv`; the supplied file has 5,572 rows. Cleaning and generated features
are applied during notebook execution, with original message text retained.

## Validation status and reproducibility

The integrated notebook was checked for valid notebook structure, Python syntax and path
consistency. The team member built the image and executed the notebook through Docker
Desktop. The saved execution contains no cell errors and passes the required-output checks.
The assistant verified these saved notebook outputs and files, but did not independently
run the image because its sandbox was denied Docker engine access. The supplied notebook screenshot and labeled transcript images are included;
the actual docker images screenshot, successful Hub push and teammate pull/run verification
remain to be supplied. See screenshots/EVIDENCE_INDEX.md.

The rubric requires `:latest` as the base image, so future fresh builds can resolve to newer
base versions and packages. The published custom image digest identifies a specific built
artifact; `output/environment_requirements.txt` records Python package versions from execution.
For exact image reproduction, the teammate can pull `ayman2287/bd-a1@sha256:ACTUAL_DIGEST`
using the real digest recorded after push. This differs from rebuilding later against latest.

## References

- [Jupyter Docker Stacks: custom images and requirements](https://jupyter-docker-stacks.readthedocs.io/en/latest/using/recipes.html)
- [Jupyter Docker Stacks: running containers](https://jupyter-docker-stacks.readthedocs.io/en/latest/using/running.html)
- [Docker bind mounts](https://docs.docker.com/engine/storage/bind-mounts/)
- [Docker image push](https://docs.docker.com/reference/cli/docker/image/push/)
